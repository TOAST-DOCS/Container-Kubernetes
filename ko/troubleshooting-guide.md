## Container > NHN Kubernetes Service(NKS) > 문제 해결 가이드

NHN Kubernetes Service(NKS)를 사용하면서 겪을 수 있는 다양한 문제들에 대한 해결 방법을 설명합니다.

### > 워커 노드의 컨테이너 로그 파일 크기가 커지면서 디스크 공간이 줄어듭니다.

#### 로그 로테이션 설정하기
컨테이너 로그 파일 관리(최대 파일 크기, 로그 파일 개수 설정 등)를 위해 워커 노드에 아래와 같은 설정을 추가합니다.

```
$ sudo bash -c "cat > /etc/logrotate.d/docker" <<EOF
/var/lib/docker/containers/*/*.log {
    rotate 10
    copytruncate
    missingok
    notifempty
    compress
    maxsize 100M
    daily
    dateext
    dateformat -%Y%m%d-%s
    create 0644 root root
}
EOF
```

워커 노드에서는 매일 새벽 3시경 cron을 통해 상기 설정 기반의 컨테이너 로그 로테이션이 수행됩니다.

> [참고] `CentOS 7.8 - Container (2021.07.27)` 이후의 인스턴스 이미지에는 위와 같은 로그 로테이션 설정이 기본으로 제공됩니다.
<br>

#### 로그 로테이션 설정 동기화하기

클러스터 운용 과정에서 다음과 같은 경우 일부 워커 노드의 로그 로테이션 설정이 달라지는 상황이 발생할 수도 있습니다.

  * 노드 그룹 간 인스턴스 이미지가 다른 경우
    * 로그 로테이션 설정 적용 이미지 기반 노드 vs 미적용 이미지 기반 노드
  * 로그 로테이션 설정 미적용 이미지 기반 노드에 직접 설정을 추가한 경우
    * 클러스터 오토 스케일러 혹은 노드 그룹 크기 조정을 통해 추가된 신규 노드 vs 기존 노드
  * 로그 로테이션 설정 내역을 직접 변경 적용한 경우
    * 클러스터 오토 스케일러 혹은 노드 그룹 크기 조정을 통해 추가된 신규 노드 vs 기존 노드

위와 같은 상황에서 모든 워커 노드에 대해 일관된 로그 로테이션 설정을 유지하고 싶다면 다음과 같은 동기화 방법을 고려해 볼 수 있습니다.

##### ```SSH를 통한 로그 로테이션 설정 파일 동기화하기```

아래는 클러스터의 모든 워커 노드에 대해 ssh를 기반으로 로그 로테이션 설정 파일을 비교 후, 필요한 노드에 복사해 주는 스크립트를 생성해 주는 셸 커맨드입니다.

커맨드 실행에 앞서 필요한 것은 다음과 같습니다.

* 워커 노드에 대한 ssh 포트 오픈 (security group에서 tcp 22번 포트 오픈)
* 워커 노드 생성 시 사용한 keypair 파일
* kubectl 바이너리
* 대상 클러스터에 대한 kubeconfig 파일
* 동기화 소스로 사용할 logrotate 설정 파일

아래에서 3개의 cp 명령의 첫 파라미터 값을 적절히 수정하여 실행하면 됩니다.<br>
실행 완료 후 생성된 셸 스크립트 및 cron job을 통해 매일 자정에 동기화 작업이 수행됩니다.
```
$ cd ~
$ mkdir logrotate_for_container
$ cd logrotate_for_container
$
$ cp /path/to/my/kubeconfig/file kubeconfig.yaml
$ cp /path/to/my/keypair/file keypair.pem
$ cp /path/to/my/docker/logrotate/file docker_logrotate_config
$
$ cat > sync_logrotate.sh <<EOF
#!/bin/bash

set -o errexit

##################################################################
# KUBECONFIG:   kubeconfig file for a target cluster             #
# KEYPAIR:      keypair file for worker nodes                    #
# LOCAL_CONFIG: logrotate configuration file used as sync source #
##################################################################
KUBECONFIG="kubeconfig.yaml"
KEYPAIR="keypair.pem"
LOCAL_CONFIG="docker_logrotate_config"
REMOTE_CONFIG="/etc/logrotate.d/docker"

base_config_hash=`md5sum ${LOCAL_CONFIG} | awk '{print $1}'`
worker_nodes=$(kubectl --kubeconfig=$KUBECONFIG get nodes -A -o jsonpath='{.items[*].status.addresses[?(@.type=="InternalIP")].address}')

echo "[`date`] Start to synchronize the logrotate configuration for docker container"
echo "  * Worker nodes list = ${worker_nodes}"
echo "  * Comparing local config hash with remote config hash (local config hash = ${base_config_hash})"

sync_nodes=""
for node in ${worker_nodes}; do
  node_conf_hash=`ssh -i ${KEYPAIR} -o StrictHostKeyChecking=no centos@${node} "md5sum ${REMOTE_CONFIG}"| awk '{print $1}'`

  if [ "${base_config_hash}" != "${node_conf_hash}" ]; then
    echo "    -> Different hash with /etc/logrotate.d/docker@${node} (remote config hash = ${node_conf_hash})"
    sync_nodes="${sync_nodes} ${node}"
  fi
done

if [ -n "${sync_nodes}" ]; then
  echo "  * Copying ${LOCAL_CONFIG} to ${REMOTE_CONFIG} at target nodes: ${sync_nodes}"
  for node in ${sync_nodes}; do
    scp -i ${KEYPAIR} -o StrictHostKeyChecking=no ${LOCAL_CONFIG} centos@${node}:~/${LOCAL_CONFIG}.tmp >/dev/null
    node_conf_hash=`ssh -i ${KEYPAIR} -o StrictHostKeyChecking=no centos@${node} "sudo cp ${LOCAL_CONFIG}.tmp ${REMOTE_CONFIG} && rm ${LOCAL_CONFIG}.tmp && md5sum ${REMOTE_CONFIG}" | awk '{print $1}'`
    if [ $? == 0 ]; then
      echo "    -> Copy done... New hash of ${REMOTE_CONFIG}@${node} = ${node_conf_hash}"
    else
      echo "    -> Something's wrong at ${node}"
    fi
  done
else
  echo "  * Logrotate configurations are up to date on all worker nodes"
fi
echo "[`date`] Finish to synchronize logrotate configuration"
EOF
$
$ chmod +x sync_logrotate.sh
$
$ crontab <<EOF
0 0  * * * ~/logrotate_for_container/sync_logrotate.sh > ~/logrotate_for_container/sync_logrotate.log
EOF
$
```



> [참고] 상기 내용은 동기화를 위한 하나의 방법일 뿐이며, 사용자 환경에 더 적절한 방법이 있다면 그것을 통해 동기화 작업이 수행되도록 하면 됩니다.


### > 파드의 상태가 ImagePullBackOff로 나타납니다.

2020년 11월 20일부터 dockerhub는 컨테이너 이미지 pull 요청 횟수에 다음과 같은 제한을 두는 정책을 시행하였습니다. 제한과 관련된 자세한 사항은 [Understanding Docker Hub Rate Limiting](https://www.docker.com/increase-rate-limits)과 [Pricing & Subscriptions](https://www.docker.com/pricing)을 참고하세요.


| 계정 등급 | 2020년 11월 20일 이전 | 2020년 11월 20일 이후 |
| --- | --- | --- |
| 미인증 사용자 | 2,500req/6H | 100req/6H |
| Free Tier | 2,500 req/6H | 200 req/6H |
| Pro/Team/Large Tier | Unlimit | Unlimit |

NKS의 워커 노드에서 dockerhub로부터 컨테이너 이미지를 내려받는(pull) 경우, dockerhub에 로그인 없이 6시간 이내에 100건 이상을 내려받으면 더 이상 이미지를 받아오지 못하게 됩니다. 특히 플로팅 IP가 연결되지 않은 워커는 공용 퍼블릭 IP를 이용하기 때문에 이와 같은 제약이 더 빨리 걸리게 될 수 있습니다.

해결 방안은 다음과 같습니다.

* dockerhub에 로그인하면 이미지를 받을 수 있는 개수가 늘어나게 되고, 퍼블릭 IP에 의한 제한이 아닌 계정 등급별 제한을 받게 됩니다. dockerhub 계정을 만들어 원하는 pull 개수를 제공하는 Tier에 가입하고 NKS 를 이용합니다. [Kubernetes에서 Private Registry 사용 방법](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/) 을 참고하세요.
* dockerhub에 로그인하지 않은 상황에서 독립적인 퍼블릭 IP에 의한 제약을 받고 싶은 경우, 워커 노드에 플로팅 IP를 할당합니다. 


### > 폐쇄망 환경에서 failed to pull image "k8s.gcr.io/pause:3.2"가 발생합니다.

폐쇄망 환경의 NKS는 Public registry로부터 이미지를 받아 오지 못하기 때문에 발생하는 문제입니다. "k8s.gcr.io/pause:3.2" 이미지처럼 기본으로 배포되어 있는 이미지는 워커 노드 생성 시 NHN Cloud 내부 레지스트리로부터 pull 받습니다. 클러스터 생성 시 기본으로 배포되는 이미지 목록은 아래와 같습니다.

* kubernetesui/dashboard
* k8s.gcr.io/pause
* k8s.gcr.io/kube-proxy
* kubernetesui/dashboard
* kubernetesui/metrics-scraper
* quay.io/coreos/flannel
* quay.io/coreos/flannel-cni
* calico-kube-controllers
* calico-typha
* calico-cni
* calico-node
* coredns/coredns
* k8s.gcr.io/metrics-server-amd64
* k8s.gcr.io/metrics-server/metrics-server
* gcr.io/google_containers/cluster-proportional-autoscaler-amd64
* k8s.gcr.io/cpa/cluster-proportional-autoscaler-amd64
* k8s.gcr.io/cpa/cluster-proportional-autoscaler-amd64
* k8s.gcr.io/sig-storage/csi-attacher
* k8s.gcr.io/sig-storage/csi-provisioner
* k8s.gcr.io/sig-storage/csi-snapshotter
* k8s.gcr.io/sig-storage/csi-resizer
* k8s.gcr.io/sig-storage/csi-node-driver-registrar
* k8s.gcr.io/sig-storage/snapshot-controller
* docker.io/k8scloudprovider/cinder-csi-plugin
* k8s.gcr.io/node-problem-detector
* k8s.gcr.io/node-problem-detector/node-problem-detector
* k8s.gcr.io/autoscaling/cluster-autoscaler
* nvidia/k8s-device-plugin

해당 이미지에 대해 동일한 문제가 발생할 수 있습니다.

기본 이미지는 kubelet의 Image garbage collection에 의해 삭제될 수 있습니다. kubelet garbage collection 관련 정보는 [Garbage Collection](https://kubernetes.io/docs/concepts/architecture/garbage-collection/)을 참고하세요. NKS의 경우 imageGCHighThresholdPercent, imageGCLowThresholdPercent가 기본값으로 설정되어 있습니다.
```
imageGCHighThresholdPercent=85 : 디스크 사용률이 85%를 초과하는 경우 항상 이미지 Garbage Collection을 실행하여 사용하지 않는 이미지를 제거합니다.
imageGCLowThresholdPercent=80 : 디스크 사용률이 80% 이하일 경우 이미지 Garbage Collection을 실행하지 않습니다.
```

해결 방안은 다음과 같습니다.
이미지 pull에 실패한 경우 아래 명령을 통해 NHN Cloud 내부 레지스트리에서 이미지를 pull 받을 수 있습니다. NKS 1.24.3 version 이상인 경우 docker가 아닌 nerdctl로 사용해야 합니다.
```
TARGET_IMAGE="failed to pull 발생한 image"
INFRA_REGISTRY="harbor-kr1.cloud.toastoven.net/container_service/$(basename $TARGET_IMAGE)"
docker pull $INFRA_REGISTRY
docker tag $INFRA_REGISTRY $TARGET_IMAGE
docker rmi $INFRA_REGISTRY
```

### > k8s v1.24 이상의 버전에서 `pulling from host docker.pkg.github.com failed` 오류가 발생하며 이미지 pull이 실패합니다. 

github의 패키지 레지스트리가 Docker 레지스트리에서 Container 레지스트리로 변경되었기 때문에 발생한 문제입니다. v1.24 이전 버전의 클러스터는 컨테이너 런타임으로 Docker를 사용하여 `docker.pkg.github.com` 레지스트리에서 이미지 pull이 가능했지만, v1.24 이상 버전의 NKS 클러스터는 컨테이너 런타임으로 cotainerd를 사용하기 때문에 더 이상 `docker.pkg.github.com` 레지스트리에서 이미지 pull이 불가능합니다. 패키지 레지스트리 이전에 관한 자세한 사항은 [Migration to Container registry from the Docker registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/migrating-to-the-container-registry-from-the-docker-registry)를 참고하세요.


해결 방안은 다음과 같습니다.
파드 매니페스트에 정의된 image URL의 base를 `docker.pkg.github.com`에서 `gchr.io`로 변경합니다.

### > `cannot allocate memory` 오류가 발생하며 파드의 상태가 `FailedCreatePodContainer`로 나타납니다.

리눅스 커널의 기능 중 memory cgroup에 대한 kernel object accounting 기능의 버그로 발생하는 현상입니다. 주로 리눅스 커널 3.x, 4.x 버전에서 발생하며, dying memory cgroup problem 이슈로 알려져 있습니다. 사용자가 이미지 수준에서 memory cgroup에 대한 kernel object accounting 기능을 비활성화해 이 문제를 우회할 수 있습니다. 

#### 기존에 생성된 클러스터에 해결 방안 적용
워커 노드에 접속하여 부팅 옵션을 변경한 후 재시작합니다.

1. `/etc/default/grub` 파일을 열고 `GRUB_CMDLINE_LINUX`의 기존 값에 `cgroup.memory=nokmem`을 추가합니다.

```diff
# vim /etc/default/grub
- GRUB_CMDLINE_LINUX="..."
+ GRUB_CMDLINE_LINUX="... cgroup.memory=nokmem"
```

2. 설정 사항을 반영합니다.
```
$ grub2-mkconfig -o /boot/grub2/grub.cfg
```

3. 워커 노드를 재시작합니다.
```
$ reboot
```

해당 이슈는 항상 발생하는 것은 아니며, 사용자의 애플리케이션 특성에 따라 발생할 수 있습니다. 만약 이슈 발생이 우려될 경우 NKS의 커스텀 이미지 기능을 이용해 처음부터 위와 같은 해결 방안이 적용된 워커 노드 이미지를 사용할 수 있습니다.

#### NKS 커스텀 이미지 기능을 사용하여 새로 생성한 클러스터에 해결 방안 적용
NKS에서는 사용자의 커스텀 이미지를 기반으로 한 워커 노드 그룹을 생성하는 기능을 제공하고 있습니다. NKS 커스텀 이미지 기능을 사용하여 memory cgroup에 대한 kernel object accounting 기능이 비활성화된 이미지를 만들고 클러스터 생성 시 활용할 수 있습니다. 커스텀 이미지 사용 기능에 대한 자세한 내용은 [커스텀 이미지를 워커 이미지로 활용](/Container/NKS/ko/user-guide/#_25)을 참고하세요.

1. 이미지 템플릿 생성 과정에서 사용자 스크립트에 아래 내용을 입력합니다.
```
#!/bin/bash
args="cgroup.memory=nokmem"
grub_file="/etc/default/grub"
sudo sed -i "s/GRUB_CMDLINE_LINUX=\"\(.*\)\"/GRUB_CMDLINE_LINUX=\"\1 $args\"/" "$grub_file"

sudo grub2-mkconfig -o /boot/grub2/grub.cfg
```

### > calico-typha, calico-kube-controller 이미지 pull 실패 오류가 발생하고 calico-node 파드가 정상 동작하지 않아서 클러스터 네트워크 장애가 발생합니다.
Calico 이미지가 Kubelet의 Garbage Collection에 의해 제거된 후, 올바르지 않은 컨테이너 이미지 리포지터리 주소로 인해 재다운로드할 수 없게 되어 발생하는 문제입니다. Kubelet은 노드의 디스크 사용량을 관리하기 위해 사용되지 않는 컨테이너 이미지를 정리하는 Garbage Collection 기능을 제공합니다. 이 기능에 대한 자세한 정보는 [Garbage Collection](https://kubernetes.io/docs/concepts/architecture/garbage-collection/) 문서에서 확인할 수 있습니다. NKS의 경우 Kubelet의 imageGCHighThresholdPercent, imageGCLowThresholdPercent가 기본값으로 설정되어 있습니다.
```
imageGCHighThresholdPercent=85 : 디스크 사용률이 85%를 초과하는 경우 항상 이미지 Garbage Collection을 실행하여 사용하지 않는 이미지를 제거합니다.
imageGCLowThresholdPercent=80 : 디스크 사용률이 80% 이하일 경우 이미지 Garbage Collection을 실행하지 않습니다.
```

#### 증상 발생 시 확인 방법
2024년 05월 이전에 생성된 클러스터에서 문제가 발생할 수 있습니다. `kubectl get all -n kube-system` 명령 확인 시 calico-kube-controller 또는 calico-typha 파드의 상태가 **ImagePullBackOff** 또는 **ErrImagePull** 로 유지됩니다. calico-node 파드는 **Running** 상태로 보이지만, Ready 항목은 **0/1**로 나타납니다. calico-node 파드는 daemonset으로 배포되므로 kubelet의 GC에 의한 이미지 삭제 대상이 아닙니다. 그러나 calico-typha와의 통신 실패로 인해 정상적으로 동작하지 않아 위와 같은 문제가 발생할 수 있습니다. 2024년 05월 이후에 생성된 클러스터의 경우 calico image 리포지터리 설정이 변경되어 해당 문제가 발생하지 않습니다. 

#### 해결 방안
calico 관련 image 리포지터리 url을 public 리포지터리로 변경하는 스크립트를 실행하여 해결할 수 있습니다. 단, 이 해결 방안은 **인터넷에 연결 가능한 클러스터**에만 적용할 수 있으며 스크립트 실행 중 **일시적으로 클러스터 파드 네트워킹이 단절될 수 있으므로 작업 진행 시 주의**가 필요합니다. 스크립트를 실행하기 전에, 모든 워커 노드가 'Ready' 상태인지 확인해야 합니다. 문제 해결 스크립트는 아래와 같습니다.

```
#!/bin/bash

tag="v3.24.1"
namespace="kube-system"

calico_cni_image="calico/cni:$tag"
calico_node_image="calico/node:$tag"
calico_typha_image="calico/typha:$tag"
calico_kube_controllers_image="calico/kube-controllers:$tag"
images=($calico_cni_image $calico_node_image $calico_typha_image $calico_kube_controllers_image)
default_timeout=4

declare -a failed_updates

check_image_match() {
    local resource_type=$1
    local resource_name=$2
    local namespace=$3
    local expected_image=$4

    current_image=$(kubectl get $resource_type $resource_name -n $namespace -o jsonpath="{.spec.template.spec.containers[*].image}")
    echo "Current $resource_type $resource_name image: $current_image"

    if [ "$current_image" == "$expected_image" ]; then
        echo "The image repo is not a target because it does not match the $expected_image"
        exit 1
    fi
}

pull_and_verify_image() {
    local node=$1
    local image=$2
    local pod_name=$(kubectl debug node/"$node" --image="$image" --namespace=$namespace -- sleep 1 --quiet | awk '{print $4}')
    echo "Created pod $pod_name in $namespace namespace"

    local start_time=$(date +%s)
    local timeout_seconds=360

    while :; do
        local current_time=$(date +%s)
        local elapsed_time=$((current_time - start_time))

        if [ $elapsed_time -ge $timeout_seconds ]; then
            echo "Timeout reached: $timeout_seconds seconds for node $node, image $image."
            echo "Exiting due to timeout failure."
            kubectl delete pod $pod_name -n $namespace >/dev/null 2>&1
            exit 1
        fi

        local container_state=$(kubectl get pod $pod_name -n $namespace -o jsonpath='{.status.containerStatuses[0].state}' 2>/dev/null)

        if echo "$container_state" | grep -q "terminated"; then
            echo "Container has terminated, deleting pod $pod_name from $namespace namespace"
            kubectl delete pod $pod_name -n $namespace >/dev/null 2>&1
            break
        elif echo "$container_state" | grep -q "running"; then
            echo "Container is running, deleting pod $pod_name from $namespace namespace"
            kubectl delete pod $pod_name -n $namespace >/dev/null 2>&1
            break
        elif echo "$container_state" | grep -q "waiting"; then
            local reason=$(kubectl get pod $pod_name -n $namespace -o jsonpath='{.status.containerStatuses[0].state.waiting.reason}' 2>/dev/null)
            if [[ "$reason" == "ImagePullBackOff" || "$reason" == "ErrImagePull" ]]; then
                echo "Failed to pull image $image on node $node due to $reason. Exiting."
                kubectl delete pod $pod_name -n $namespace >/dev/null 2>&1
                exit 1
            fi
        fi

        sleep 5
    done
}

update_image() {
    local resource_type=$1
    local resource_name=$2
    local timeout=$3
    shift 3

    echo ""
    echo "Updating $resource_type $resource_name with timeout ${timeout} minutes..."
    local update_command="kubectl set image $resource_type/$resource_name"
    for arg in "$@"; do
        local container_name=$(echo $arg | cut -d'=' -f1)
        local image_name=$(echo $arg | cut -d'=' -f2)
        update_command+=" $container_name=$image_name"
    done
    update_command+=" -n $namespace"

    if ! eval $update_command; then
        echo "Failed to update $resource_type $resource_name"
        failed_updates+=("$resource_type/$resource_name")
        return
    fi
    check_rollout_status $resource_type $resource_name $timeout
    return $?
}

check_rollout_status() {
    local resource_type=$1
    local resource_name=$2
    local timeout=$3

    echo "Checking rollout status for $resource_type $resource_name..."
    if ! kubectl rollout status $resource_type $resource_name -n $namespace --timeout=${timeout}m; then
        echo "Rollout status check failed for $resource_type $resource_name"
        failed_updates+=("$resource_type/$resource_name")
        return 1
    fi
    echo "$resource_type $resource_name updated successfully."
    return 0
}

delete_old_pods() {
    local resource_type=$1
    local resource_name=$2
    local old_pods=$3

    for pod in $old_pods; do
        if kubectl get pods $pod -n $namespace &> /dev/null; then
            echo "Deleting old pod: $pod"
            kubectl delete pod $pod -n $namespace
        fi
    done
}

update_calico_node() {
    local resource_type="daemonset"
    local resource_name="calico-node"
    local timeout=$(( $(kubectl get nodes --no-headers | wc -l) * 4 ))
    update_image $resource_type $resource_name $timeout "$resource_name=$calico_node_image" "install-cni=$calico_cni_image" "mount-bpffs=$calico_node_image"
}

update_calico_kube_controller() {
    local resource_type="deployment"
    local resource_name="calico-kube-controllers"
    update_image $resource_type $resource_name $default_timeout "$resource_name=$calico_kube_controllers_image"
}

update_calico_typha_image() {
    local resource_type="deployment"
    local resource_name="calico-typha"
    local old_pods=$(kubectl get pods -n $namespace -l k8s-app="$resource_name" -o jsonpath="{.items[*].metadata.name}")
    
    if ! update_image $resource_type $resource_name $default_timeout "$resource_name=$calico_typha_image"; then
        delete_old_pods $resource_type $resource_name $old_pods
    fi
}

check_image_match "daemonset" "calico-node" $namespace $calico_node_image
check_image_match "deployment" "calico-kube-controllers" $namespace $calico_kube_controllers_image
check_image_match "deployment" "calico-typha" $namespace $calico_typha_image

for node in $(kubectl get nodes --no-headers | awk '{print $1}'); do
    echo ""
    echo "Worker node : [$node] calico images pull start!!"
    for image in "${images[@]}"; do
        echo "Pulling $image"
        pull_and_verify_image $node $image
    done
done
echo "The calico image pull has been completed!"
echo ""

update_calico_node
update_calico_kube_controller
update_calico_typha_image

echo ""

if [ ${#failed_updates[@]} -eq 0 ]; then
    echo "Calico images update completed!"
    exit 0
else
    echo "[WARNING] Please check to resources status:"
    for resource in "${failed_updates[@]}"; do
        echo "- $resource"
    done
    exit 1
fi
```
스크립트 과정은 아래와 같습니다.
1. 모든 워커 노드에 calico 관련 이미지를 pull 받습니다.
2. calico-node daemonset 이미지 리포지터리를 변경하는 롤링 업데이트를 진행합니다.
3. calico-kube-controllers deployment 이미지 리포지터리를 변경하는 롤링 업데이트를 진행합니다.
4. calico-typha deployment 이미지 리포지터리를 변경하는 롤링 업데이트를 진행합니다.

해당 스크립트는 kubectl 명령이 가능한 환경에서 실행할 수 있습니다. 실행 방법은 아래와 같습니다.
* vim calico_manifest_image_change.sh
* 본문 스크립트 내용 저장
* KUBECONFIG 환경 변수에 kubeconfig 설정 파일 경로 저장
* chmod 755 calico_manifest_image_change.sh
* ./calico_manifest_image_change.sh


### > rpc.statd is not running but is required for remote locking 오류가 발생하며 파드에서 NAS 볼륨 마운트가 실패합니다.

워커 노드의 rpc.statd 프로세스가 좀비 프로세스가 되거나 관리자의 명령에 의해 정지되어 발생하는 문제입니다. 볼륨을 마운트하기 위해서는 워커 노드에 rpcbind 및 rpc.statd 프로세스가 정상적으로 실행되고 있어야 합니다. 해결 방안은 다음과 같습니다.
```
systemctl restart rpc-statd
systemctl restart rpcbind
```

### > PV 용량 증설 후에도 파드의 파일 시스템에 증설된 용량이 반영되지 않습니다.
2024년 8월 이전에 생성된 1.20 이상 버전의 클러스터에서 발생할 수 있는 문제입니다. 아래 스크립트 실행을 통해 클러스터에 배포된 cinder-csi-driver를 업데이트하여 문제를 해결할 수 있습니다. 스크립트 실행 이후 새로 생성하거나 용량 증설된 PV에만 설정 업데이트가 반영됩니다.

kubeconfig_file_path 변수에 클러스터의 kubeconfig 파일이 위치한 절대경로 값을 정의한 후 스크립트를 실행합니다.
```
#!/bin/bash
kubeconfig_file_path={kubeconfig 파일 절대 경로}
SECRET_NAME="cinder-csi-cloud-config"
NAMESPACE="kube-system"
# Fetch the secret using kubectl and parse the JSON output with jq
secret_data=$(kubectl --kubeconfig=$kubeconfig_file_path get secret "$SECRET_NAME" -n "$NAMESPACE" -o json)
# Extract the 'cloud-config' key and decode its value
cloud_config_base64=$(echo "$secret_data" | jq -r '.data["cloud-config"]')
if [[ "$cloud_config_base64" != "null" ]]; then
    # Decode the base64 value
    cloud_config=$(echo "$cloud_config_base64" | base64 --decode)
    # Add the [BlockStorage] section with rescan-on-resize=true
    modified_cloud_config=$(cat <<EOF
$cloud_config
[BlockStorage]
rescan-on-resize=true
EOF
)
    # Encode the modified cloud-config back to base64
    modified_cloud_config_base64=$(echo "$modified_cloud_config" | base64 | tr -d '\n')
    # Update the Kubernetes secret with the new base64-encoded data
    kubectl --kubeconfig=$kubeconfig_file_path patch secret "$SECRET_NAME" -n "$NAMESPACE" --type=json \
        -p="[{'op': 'replace', 'path': '/data/cloud-config', 'value':'$modified_cloud_config_base64'}]"
    echo "Secret $SECRET_NAME updated successfully."
else
    echo "cloud-config key not found in secret $SECRET_NAME"
fi
kubectl -n kube-system rollout restart daemonet cinder-csi-nodeplugin
kubectl -n kube-system rollout restart statefulset cinder-csi-controllerplugin
```

### > timed out waiting for condition 오류가 발생하며 파드에 볼륨 마운트가 실패합니다.
파드에 큰 사이즈의 볼륨을 마운트하는 경우 발생할 수 있는 문제입니다. 기본적으로 Kubernetes는 볼륨을 마운트할 때 파드의 SecurityContext에 지정된 fsGroup과 일치하도록 각 볼륨의 내용에 대한 소유 및 권한을 변경합니다. 볼륨이 큰 경우 소유 및 권한을 확인하고 변경하는 데 많은 시간이 소요되어 타임아웃이 발생할 수 있습니다. 

타임아웃이 발생하는 것을 막기 위해 securityContext의 fsGroupChangePolicy 필드를 사용하여 Kubernetes가 볼륨에 대한 소유 및 권한을 확인하고 관리하는 방식을 변경할 수 있습니다. 자세한 내용은 [Configure volume permission and ownership change policy for pods](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/#configure-volume-permission-and-ownership-change-policy-for-pods)를 참고하세요.