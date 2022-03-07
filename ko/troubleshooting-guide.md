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


#### > Pod의 상태가 ImagePullBackOff로 나타납니다.

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
