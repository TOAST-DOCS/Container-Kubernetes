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


### > 폐쇄망 환경에서 failed to pull image `k8s.gcr.io/pause:3.2`가 발생합니다.
폐쇄망 환경의 클러스터가 퍼블릭 레지스트리로부터 이미지를 받아 오지 못하기 때문에 발생하는 문제로, 2024년 8월 이전에 생성된 클러스터에서 발생할 수 있습니다. `k8s.gcr.io/pause:3.2` 이미지처럼 기본으로 배포되어 있는 이미지는 워커 노드 생성 시 NHN Cloud 내부 레지스트리로부터 pull 받습니다. 하지만, 최초 이미지를 pull 받은 이후 이미지가 삭제되는 경우 문제가 발생할 수 있습니다. 클러스터 생성 시 기본으로 배포되는 이미지 목록은 아래와 같습니다.

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

#### 해결 방안
NKS 레지스트리를 활성화하면 폐쇄망 환경에서 컨테이너 이미지를 퍼블릭 레지스트리로부터 받아 오지 않고, NHN Cloud 내부 레지스트리에서 받아 오도록 클러스터 설정을 변경할 수 있습니다. NKS 레지스트리는 클러스터 조회 화면에서 활성화할 수 있습니다.


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
NKS에서는 사용자의 커스텀 이미지를 기반으로 한 워커 노드 그룹을 생성하는 기능을 제공하고 있습니다. NKS 커스텀 이미지 기능을 사용하여 memory cgroup에 대한 kernel object accounting 기능이 비활성화된 이미지를 만들고 클러스터 생성 시 활용할 수 있습니다. 커스텀 이미지 사용 기능에 대한 자세한 내용은 [커스텀 이미지를 워커 이미지로 활용](/Container/NKS/ko/user-guide/#custom-image)을 참고하세요.

1. 이미지 템플릿 생성 과정에서 사용자 스크립트에 아래 내용을 입력합니다.
```
#!/bin/bash
args="cgroup.memory=nokmem"
grub_file="/etc/default/grub"
sudo sed -i "s/GRUB_CMDLINE_LINUX=\"\(.*\)\"/GRUB_CMDLINE_LINUX=\"\1 $args\"/" "$grub_file"

sudo grub2-mkconfig -o /boot/grub2/grub.cfg
```

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

### > Calico-eBPF CNI를 사용하는 클러스터의 파드에 hostnetwork: true, dnsPolicy: ClusterFirstWithHostNet 옵션을 설정하면 UDP 통신 문제가 발생합니다.
Calico v3.28.0에서 UDP 통신 중 BPF NAT 테이블이 네트워크 패킷을 올바르게 처리하지 못하여 발생하는 문제입니다. eBPF를 사용하는 경우 TCP는 `CTLB(connect-time load balancing)` 방식으로 통신하며 UDP는 BPF가 관리하는 `NAT 테이블`을 통해 통신합니다. 이 문제는 UDP 통신도 CTLB 방식으로 변경하면 해결할 수 있습니다.

`CTLB(connect-time load balancing)`는 네트워크 로드 밸런싱 기술의 하나로 클라이언트가 서버에 처음 연결할 때 첫 번째 패킷에서 백엔드 서버를 선택하고 이후의 모든 트래픽은 선택된 백엔드 서버로 직접 전달됩니다. 이를 통해 세션 지속성이 보장되며, 매번 로드 밸런싱을 수행하는 오버헤드를 줄일 수 있습니다.

calico-node daemonset의 UDP CTLB 설정을 변경하여 문제를 해결할 수 있습니다.
아래는 설정을 변경하는 과정입니다.
```
kubectl edit daemonset.apps/calico-node -n kube-system
```
spec.template.spec.containers.env 항목에 아래와 같은 설정 추가가 필요합니다.
파드 템플릿이 수정되면 롤링 업데이트 방식으로 calico-node가 재시작됩니다.
```
- name: FELIX_BPFCONNECTTIMELOADBALANCING
    value: "Enabled"
- name: FELIX_BPFHOSTNETWORKEDNATWITHOUTCTLB
    value: "Disabled"
```

#### 해결 방안 적용 후 UDP 통신 설정 시 주의 사항
UDP는 비연결형 프로토콜로 서버/클라이언트 통신 시 별도 세션을 설정하거나 연결을 유지하지 않고 데이터를 전송합니다. 그러나 Golang `net.DialUDP()` 함수와 같은 UDP의 `connect()` 함수를 사용하면 UDP 소켓을 특정 주소와 연결해 지정된 주소로만 데이터를 전송하고 수신할 수 있습니다. 
Calico의 eBPF를 사용하면 UDP에 CTLB(connect-time load balancing)가 활성화된 클러스터에 UD `connect()` 함수를 사용하는 파드를 배포한 경우 서버 역할을 하는 파드가 재배포되면 통신 문제가 발생할 수 있습니다. 이는 UDP 소켓이 초기 연결된 서버 주소에만 데이터 전송을 시도하기 때문입니다. 서버 파드가 재배포되면 IP 주소나 네트워크 경로가 변경될 수 있는데 UDP connect() 소켓은 이전 서버 주소에만 데이터를 보내므로 통신에 실패할 가능성이 있습니다.
이 문제는 UDP connect()의 동작 방식과 CTLB 환경에서 발생하는 알려진 문제이므로 Calico eBPF와 UDP CTLB를 사용하는 클러스터에서 UDP의 connect() 함수를 사용할 경우 이러한 통신 문제가 발생할 수 있음을 인지하고 주의가 필요합니다.

### > Calico-eBPF 클러스터에서 istio가 오동작합니다.
eBPF가 활성화되면 `CTLB(connect-time load balancing)` 방식으로 연결 시점에 로드 밸런싱을 수행하여 클라이언트가 서비스에 연결을 시도할 때 첫 번째 패킷에서 백엔드 파드를 선택하고 이후의 모든 패킷은 해당 백엔드로 직접 전달됩니다. 한편, Istio는 서비스 메시를 구성하기 위해 사이드카 프록시를 배포하고 프록시는 애플리케이션 트래픽을 가로채 제어 및 모니터링 역할을 합니다. 
CTLB가 활성화된 경우 패킷은 BPF MAP에서 목적지 Pod로 직접 전달되며 이 과정에서 패킷이 변조됩니다. 따라서 Istio의 프록시를 거치지 않고, 패킷이 바로 목적지 Pod로 전달됩니다. 이와 같은 eBPF 네트워킹 구조로 인해 Istio 기능이 정상적으로 동작하지 않을 수 있습니다. istio를 사용한 클러스터 관리가 필요한 경우 Calico-VXLAN 클러스터 사용을 고려해야 합니다.


### > Calico-eBPF CNI를 사용하는 클러스터에서 노드 감축 후 증설 시 네트워크 장애가 발생합니다.
Calico v3.28.0의 calico/kube-controllers에서 발견된 버그로 인해 발생하는 문제입니다. 노드 감축 진행 시 calico/kube-controllers 파드가 배포된 노드가 제거되면 해당 파드는 다른 노드로 스케줄링되어 실행됩니다. calico/kube-controllers가 재실행되는 동안 노드 정보가 동기화되지 않습니다. 이 상태에서 제거했던 노드와 동일한 이름의 노드가 추가되면 네트워크 장애가 발생할 수 있습니다.

이 문제는 Calico v3.28.2에서 해결되었습니다. Calico v3.28.2를 사용하기 위해서는 Kubernetes 버전을 업그레이드하거나 클러스터를 다시 생성해야 합니다. 

### > 클러스터 업그레이드에 실패합니다.

#### NKS 생성 시 기본으로 배포되는 리소스에 finalizers 설정 여부를 확인해야 합니다.
NKS 생성 시 배포된 리소스에 finalizers가 설정되어 있는 경우, 리소스가 제거되지 못하여 업그레이드에 실패합니다. 모든 워커 노드 그룹의 업그레이드가 완료되면 NKS 초기 배포 리소스가 재배포됩니다. 이 과정에서 NKS 초기 배포 리소스에 finalizers가 설정되어 있으면 리소스 재배포에 실패해 업그레이드가 중단됩니다. 이 문제를 해결하기 위해서는 업그레이드 전 NKS 초기 배포 리소스에 finalizers 설정을 제거해야 합니다.

finalizers 설정 제거 명령어는 다음과 같습니다.
```
kubectl patch {리소스 유형} {리소스 이름} -n {네임스페이스} --type=json -p='[{"op": "remove", "path": "/metadata/finalizers"}]'
```
예시
```
kubectl patch clusterrole calico-kube-controllers --type=json -p='[{"op": "remove", "path": "/metadata/finalizers"}]'
```


### > NKS 레지스트리가 비활성 상태인 v1.29.3 이하 버전 클러스터에서 노드 증설 혹은 노드 그룹 추가 시 calico-node 파드 배포에 실패하여 노드 초기화 작업에 실패합니다.
NKS 레지스트리가 활성화 상태인 Kubernetes v1.29.3 이하 버전의 클러스터에서 노드 증설 혹은 노드 그룹 추가 시 calico 관련 파드(calico-node, calico-kube-controllers, calico-typha)가 배포되지 않아 증설 작업이 실패하는 문제가 발생합니다.

이 문제는 주로 2024년 5월 이전에 생성된 클러스터에서 발생할 수 있습니다. 당시 생성된 클러스터는 NKS 전용 이미지 레지스트리가 기본적으로 비활성 상태이고, Calico 컨테이너 이미지의 리포지토리 경로가 올바르지 않아 이미지 다운로드가 불가능한 것이 원인입니다.

#### 증상 발생 시 확인 방법
`kubectl get all -n kube-system` 명령 확인 시 증설 작업에 실패한 노드에 배포되어있는 아래의 파드의 상태가 **ImagePullBackOff** 또는 **ErrImagePull** 로 유지됩니다.
- calico-node
- calico-kube-controllers
- calico-typha

#### 해결 방안
calico 관련 image 리포지터리 url을 public 리포지터리로 변경하여 문제를 해결할 수 있습니다. 단, **인터넷에 연결 가능한 클러스터**에만 적용 가능합니다. 작업 진행 중 **일시적으로 클러스터 파드 네트워킹이 단절될 수 있으므로 작업 진행 시 주의**가 필요합니다. 작업 단계는 아래와 같습니다.

1. 증설에 실패한 노드 제거
2. calico 관련 image 리포지터리 url을 public 리포지터리로 변경
3. 노드 증설 작업 진행

calico 관련 image 리포지터리 url을 public 리포지터리로 변경하는 명령어는 아래와 같습니다.
"${CALICO_TAG}"는 현재 클러스터에서 사용 중인 Calico 버전을 의미합니다.

**calico-node Daemonset 이미지 repo 변경**
```
kubectl -n kube-system set image daemonset/calico-node \
  calico-node=calico/node:${CALICO_TAG} \
  install-cni=calico/cni:${CALICO_TAG} \
  mount-bpffs=calico/node:${CALICO_TAG}

[예제]
kubectl -n kube-system set image daemonset/calico-node \
  calico-node=calico/node:v3.24.1 \
  install-cni=calico/cni:v3.24.1 \
  mount-bpffs=calico/node:v3.24.1
```

**calico-typha Deployment 이미지 repo 변경**
```
kubectl -n kube-system set image deployment/calico-typha \
  calico-typha=calico/typha:${CALICO_TAG}

[예제]
kubectl -n kube-system set image deployment/calico-typha \
  calico-typha=calico/typha:v3.24.1
```

**calico-kube-controller Deployment 이미지 repo 변경**
```
kubectl -n kube-system set image deployment/calico-kube-controllers \
  calico-kube-controllers=calico/kube-controllers:${CALICO_TAG}

[예제]
kubectl -n kube-system set image deployment/calico-kube-controllers \
  calico-kube-controllers=calico/kube-controllers:v3.24.1
```
