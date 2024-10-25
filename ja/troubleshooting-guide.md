## Container > NHN Kubernetes Service(NKS) > トラブルシューティング

NHN Kubernetes Service(NKS)を使用する際に発生する可能性のあるさまざまな問題の解決方法を説明します。

### > ワーカーノードのコンテナログファイルサイズが大きくなり、ディスクスペースが減ります。

#### ログローテーションを設定する
コンテナログファイル管理(最大ファイルサイズ、ログファイル数設定など)のためにワーカーノードに以下のような設定を追加します。

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

ワーカーノードでは毎日3時頃cronを介して上記設定のコンテナログローテーションが行われます。

> [参考] `CentOS 7.8 - Container (2021.07.27)`以降のインスタンスイメージには上記のようなログローテーション設定が基本的に提供されます。
<br>

#### ログローテーション設定を同期する

クラスタ運用過程で次のような場合は一部ワーカーノードのログローテーション設定が変わる状況が発生することもあります。
  * ノードグループ間のインスタンスイメージが異なる場合
    * ログローテーション設定適用イメージベースのノードvs未適用イメージベースのノード
  * ログローテーション設定未適用イメージベースのノードに直接設定を追加した場合
    * クラスタオートスケーラーまたはノードグループサイズ調整を行って追加された新規ノードvs既存ノード
  * ログローテーション設定履歴を直接変更適用した場合
    * クラスタオートスケーラーまたはノードグループサイズ調整を行って追加された新規ノードvs既存ノード

上記のような状況で全てのワーカーノードに一貫性のあるログローテーション設定を維持したい場合は、次のような同期方法を検討することができます。

##### ```SSH経由でログローテーション設定ファイルを同期する```

以下はクラスタのすべてのワーカーノードに対してsshを経由してログローテーション設定ファイルを比較した後、必要なノードにコピーするスクリプトを作成するコマンドです。

コマンド実行に先立って必要なことは次のとおりです。

* ワーカーノードに対するsshポートオープン(security groupでtcp 22番ポートオープン)
* ワーカーノード作成時に使用したkeypairファイル
* kubectlバイナリ
* 対象クラスタのkubeconfigファイル
* 同期ソースとして使用するlogrotate設定ファイル

下で3つのcpコマンドの最初のパラメータの値を適切に修正して実行します。<br>
実行完了後に作成されたシェルスクリプトとcron jobを通して毎日0時に同期処理が行われます。
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



> [参考]上記の内容は同期を行うための1つの方法にすぎません。ユーザーの環境に、より適切な方法があれば、その方法で同期処理を行ってください。


#### > Podの状態がImagePullBackOffと表示されます。

2020年11月20日からdockerhubはコンテナイメージpullリクエスト回数に次のような制限を設けるポリシーを実施しました。制限の詳細については、[Understanding Docker Hub Rate Limiting](https://www.docker.com/increase-rate-limits)と[Pricing & Subscriptions](https://www.docker.com/pricing)を参照してください。


| アカウント等級 | 2020年11月20日以前 | 2020年11月20日以降 |
| --- | --- | --- |
| 未認証ユーザー | 2,500req/6H | 100req/6H |
| Free Tier | 2,500 req/6H | 200 req/6H |
| Pro/Team/Large Tier | Unlimit | Unlimit |

NKSのワーカーノードでdockerhubからコンテナイメージをダウンロードする(pull)場合、dockerhubにログインせずに6時間以内に100件以上をダウンロードすると、それ以上イメージを受け取ることができなくなります。特にFloating IPが接続されていないワーカーは共用パブリックIPを利用するため、このような制約がより早くかかることがあります。

解決策は次のとおりです。
* dockerhubにログインすると、イメージを受け取ることができる数が増え、パブリックIPによる制限ではなくアカウント等級に基づいて制限を受けます。dockerhubアカウントを作成し、必要なpull数を提供するTierに加入してNKSを利用します。 [KubernetesでPrivate Registryを使用する方法](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/)を参照してください。
* dockerhubにログインしていない状況で独立したパブリックIPによる制約を受けたい場合は、ワーカーノードにFloating IPを割り当てます。


### > クローズドネットワーク環境でfailed to pull image `k8s.gcr.io/pause:3.2`が発生します。
クローズドネットワーク環境のクラスターがパブリックレジストリからイメージを取得できないため発生する問題であり、2024年8月以前に作成されたクラスターで発生する可能性があります。k8s.gcr.io/pause:3.2`イメージのように、デフォルトで配布されているイメージは、ワーカーノード作成時にNHN Cloud内部レジストリからプルされます。しかし、最初にイメージをプルされた後、イメージが削除された場合、問題が発生する可能性があります。クラスター作成時、基本的に配布されるイメージのリストは次のとおりです。
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
該当イメージに対して同じ問題が発生する可能性があります。

基本イメージはkubeletのImage garbage collectionによって削除されることがあります。 kubelet garbage collection関連情報は[Garbage Collection](https://kubernetes.io/docs/concepts/architecture/garbage-collection/)をご覧ください。NKSの場合、imageGCHighThresholdPercent, imageGCLowThresholdPercentがデフォルト値に設定されています。
```
imageGCHighThresholdPercent=85 :ディスク使用率が85%を超える場合、常にイメージGarbage Collectionを実行して使用しないイメージを削除します。
imageGCLowThresholdPercent=80 :ディスク使用率が80%以下の場合、イメージGarbage Collectionを実行しません。
```

#### 解決策
NKSレジストリを有効にすると、クローズドネットワーク環境でコンテナイメージをパブリックレジストリから取得せず、NHN Cloud内部レジストリから取得するようにクラスター設定を変更できます。NKSレジストリは、クラスター照会画面で有効化できます。


### > k8s v1.24 以上のバージョンで `pulling from host docker.pkg.github.com failed` エラーが発生し、イメージpullが失敗します。

githubのパッケージレジストリがDockerレジストリからContainerレジストリに変更されたため発生した問題です。 v1.24以前のバージョンのクラスタはコンテナランタイムとしてDockerを使用して `docker.pkg.github.com` レジストリからイメージpullが可能でしたが、v1.24以降のバージョンのNKSクラスタはコンテナランタイムとしてcotainerdを使用するため、`docker.pkg.github.com` レジストリからイメージpullができません。パッケージレジストリの移行に関する詳細は[Migration to Container registry from the Docker registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/migrating-to-the-container-registry-from-the-docker-registry) を参照してください。


解決方法は次のとおりです。
Podマニフェストに定義されたimage URLのbaseを`docker.pkg.github.com`から`gchr.io`に変更します。

### > `cannot allocate memory`エラーが発生し、Podの状態が`FailedCreatePodContainer`と表示されます。

Linuxカーネルの機能の中でmemory cgroupに対するkernel object accounting機能のバグで発生する現象です。主にLinuxカーネル3.x, 4.xバージョンで発生し、dying memory cgroup problem問題として知られています。ユーザーがイメージレベルでmemory cgroupに対するkernel object accounting機能を無効にしてこの問題を回避できます。

#### 作成済みのクラスタに解決策を適用
ワーカーノードに接続して起動オプションを変更した後、再起動します。

1. `/etc/default/grub`ファイルを開き、`GRUB_CMDLINE_LINUX`の既存値に`cgroup.memory=nokmem`を追加します。

```diff
# vim /etc/default/grub
- GRUB_CMDLINE_LINUX="..."
+ GRUB_CMDLINE_LINUX="... cgroup.memory=nokmem"
```

2. 設定事項を反映します。
```
$ grub2-mkconfig -o /boot/grub2/grub.cfg
```

3. ワーカーノードを再起動します。
```
$ reboot
```

この問題は常に発生するわけではなく、ユーザーのアプリケーション特性によって発生する可能性があります。もし問題発生が懸念される場合、NKSのカスタムイメージ機能を利用して、最初から上記のような解決策が適用されたワーカーノードイメージを使用できます。

#### NKSのカスタムイメージ機能を使って新しく作成したクラスタに解決策を適用
NKSではユーザーのカスタムイメージをベースにしたワーカーノードグループを作成する機能を提供しています。NKSカスタムイメージ機能を使用してmemory cgroupに対するkernel object accounting機能が無効化されたイメージを作成し、クラスタ作成時に活用することができます。カスタムイメージの使用機能の詳細については、[カスタムイメージをワーカーイメージとして活用](/Container/NKS/ja/user-guide/#_25)を参照してください。

1. イメージテンプレート作成過程でユーザースクリプトに下記の内容を入力します。
```
#!/bin/bash
args="cgroup.memory=nokmem"
grub_file="/etc/default/grub"
sudo sed -i "s/GRUB_CMDLINE_LINUX=\"\(.*\)\"/GRUB_CMDLINE_LINUX=\"\1 $args\"/" "$grub_file"
sudo grub2-mkconfig -o /boot/grub2/grub.cfg
```

### > calico-typha, calico-kube-controllerイメージpull失敗エラーが発生し、calico-node Podが正常に動作しないため、クラスタのネットワーク障害が発生します。
この問題は、CalicoイメージがKubeletのGarbage Collectionによって削除された後、不適切なコンテナイメージリポジトリアドレスが原因で再ダウンロードできないために発生します。Kubeletは、ノードのディスク使用量を管理するために、未使用のコンテナイメージをクリーンアップするGarbage Collection機能を提供しています。この機能の詳細については、[Garbage Collection](https://kubernetes.io/docs/concepts/architecture/garbage-collection/)文書で確認できます。 NKSの場合KubeletのimageGCHighThresholdPercent, imageGCLowThresholdPercentがデフォルト値に設定されています。
```
imageGCHighThresholdPercent=85 :ディスク使用率が85%を超える場合、常にイメージGarbage Collectionを実行して未使用のイメージを削除します。
imageGCLowThresholdPercent=80 :ディスク使用率が80%以下の場合、イメージGarbage Collectionを実行しません。
```

#### 症状発生時の確認方法
2024年05月以前に作成されたクラスタで問題が発生する可能性があります。`kubectl get all -n kube-system`コマンドを確認すると、calico-kube-controllerまたはcalico-typha Podの状態が**ImagePullBackOff**または**ErrImagePull**で維持されます。calico-node Podは**Running**状態に見えますが、Ready項目は**0/1**と表示されます。calico-node Podはdaemonsetとして配布されるため、kubeletのGCによるイメージ削除対象ではありません。しかし、calico-typhaとの通信の失敗により正常に動作せず、上記のような問題が発生する可能性があります。2024年05月以降に作成されたクラスタの場合、calico imageリポジトリの設定が変更され、この問題は発生しません。

#### 解決策
calico関連imageリポジトリurlをpublicリポジトリに変更するスクリプトを実行して解決できます。ただし、この解決策は**インターネットに接続可能なクラスタ**にのみ適用することができ、スクリプト実行中に**一時的にクラスタPodネットワーキングが切断される可能性があるため、作業進行時に注意**が必要です。スクリプトを実行する前に、すべてのワーカーノードが'Ready'状態であることを確認する必要があります。問題解決スクリプトは次のとおりです。

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
スクリプトの流れは次のとおりです。
1. 全てのワーカーノードにcalico関連イメージをpulllします。
2. calico-node daemonsetイメージリポジトリを変更するローリングアップデートを行います。
3. calico-kube-controllers deploymentイメージリポジトリを変更するローリングアップデートを行います。
4. calico-typha deploymentイメージリポジトリを変更するローリングアップデートを行います。

このスクリプトはkubectlコマンドが使える環境で実行できます。実行方法は次のとおりです。
* vim calico_manifest_image_change.sh
* 本文スクリプトの内容を保存
* KUBECONFIG環境変数にkubeconfig設定ファイルのパスを保存
* chmod 755 calico_manifest_image_change.sh
* ./calico_manifest_image_change.sh


### > rpc.statd is not running but is required for remote lockingエラーが発生し、PodでNASボリュームマウントが失敗します。

ワーカーノードのrpc.statdプロセスがゾンビプロセスになったり、管理者のコマンドによって停止して発生する問題です。ボリュームをマウントするためには、ワーカーノードでrpcbindとrpc.statdプロセスが正常に実行されている必要があります。解決策は次のとおりです。
```
systemctl restart rpc-statd
systemctl restart rpcbind
```

### > PV容量を増設しても、Podのファイルシステムに増設された容量が反映されません。
2024年8月以前に作成された1.20以上のバージョンのクラスターで発生する可能性がある問題です。下記のスクリプトを実行してクラスターに配布されたcinder-csi-driverをアップデートして問題を解決できます。スクリプトの実行後、新しく作成されたPVまたは容量が増設されたPVにのみ設定の更新が反映されます。

kubeconfig_file_path変数にクラスターのkubeconfigファイルが位置する絶対パス値を定義した後、スクリプトを実行します。
```
#!/bin/bash
kubeconfig_file_path={kubeconfigファイルの絶対パス}
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

### > timed out waiting for conditionエラーが発生し、Podのボリュームマウントが失敗します。
これは、大きなサイズのボリュームをPodにマウントする場合に発生する可能性のある問題です。基本的に、Kubernetesはボリュームをマウントする際、各ボリュームの内容に対する所有権と権限を変更し、PodのSecurityContextに指定されたfsGroupと一致するようにします。ボリュームが大きい場合、所有権と権限を確認して変更するのに時間がかかり、タイムアウトが発生する可能性があります。

タイムアウトの発生を防ぐために、securityContextのfsGroupChangePolicyフィールドを使用して、Kubernetesがボリュームの所有権と権限を確認して管理する方法を変更できます。詳細は[Configure volume permission and ownership change policy for pods](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/#configure-volume-permission-and-ownership-change-policy-for-pods)を参照してください。

### > Calico-eBPF CNIを使用するクラスターのPodにhostnetwork: true, dnsPolicy: ClusterFirstWithHostNetオプションを設定すると、UDP通信の問題が発生します。
Calico v3.28.0でUDP通信中、BPF NATテーブルがネットワークパケットを正しく処理できないために発生する問題です。 eBPFを使用する場合、TCPは`CTLB(connect-time load balancing)`方式で通信し、UDPはBPFが管理する`NATテーブル`を通じて通信します。この問題はUDP通信もCTLB方式に変更すれば解決できます。

`CTLB(connect-time load balancing)`は、ネットワークロードバランシング技術の1つで、クライアントがサーバーに初めて接続する際、最初のパケットでバックエンドサーバーを選択し、その後の全てのトラフィックは選択されたバックエンドサーバーに直接転送されます。これにより、セッションの永続性が保証され、毎回ロードバランシングを実行するオーバーヘッドを減らすことができます。

calico-node daemonsetのUDP CTLB設定を変更することで問題を解決できます。
以下は設定を変更する過程です。
```
kubectl edit daemonset.apps/calico-node -n kube-system
```
spec.template.spec.containers.env項目に下記のような設定を追加する必要があります。
Podのテンプレートが修正されると、ローリングアップデート方式でcalico-nodeが再起動されます。
```
- name: FELIX_BPFCONNECTTIMELOADBALANCING
    value: "Enabled"
- name: FELIX_BPFHOSTNETWORKEDNATWITHOUTCTLB
    value: "Disabled"
```

#### 解決策適用後、UDP通信を設定する際の注意事項
UDPは非接続型プロトコルで、サーバー/クライアント通信時、別途セッションを設定したり、接続を維持することなくデータを送信します。しかし、Golang `net.DialUDP()`関数などのUDPの`connect()`関数を使用するとUDPソケットを特定アドレスと接続して指定されたアドレスにのみデータを送受信できます。
CalicoのeBPFを使用すると、UDPのCTLB(connect-time load balancing)が有効化になっているクラスターにUD `connect()`関数を使用するPodを配布した場合、サーバーの役割を行うPodが再配布されると、通信問題が発生する可能性があります。これは、UDPソケットが最初に接続されたサーバーアドレスにのみデータ転送を試みるためです。サーバーPodが再配布されると、IPアドレスやネットワークパスが変更される可能性がありますが、UDP connect()ソケットは以前のサーバーアドレスにのみデータを送信するため、通信に失敗する可能性があります。
この問題はUDP connect()の動作方法とCTLB環境で発生する既知の問題なので、Calico eBPFとUDP CTLBを使用するクラスターでUDPのconnect()関数を使用する場合、このような通信問題が発生する可能性があることを認識して注意する必要があります。

### > Calico-eBPFクラスターでistioが誤動作します。
eBPFが有効になると、`CTLB(connect-time load balancing)`方式で接続時にロードバランシングを行い、クライアントがサービスに接続しようとする最初のパケットでバックエンドPodを選択し、その後の全てのパケットは該当バックエンドに直接転送されます。一方、Istioはサービスメッシュを構成するためにサイドカープロキシを配備し、プロキシはアプリケーショントラフィックを傍受して制御とモニタリングの役割を果たします。
CTLBが有効になっている場合、パケットはBPF MAPから目的地Podに直接転送され、この過程でパケットが改ざんされます。そのため、Istioのプロキシを経由せず、パケットは目的地Podに直接転送されます。このようなeBPFネットワーク構造により、Istioの機能が正常に動作しない場合があります。istioを使ったクラスター管理が必要な場合は、Calico-VXLANクラスターの使用を考慮する必要があります。


### > Calico-eBPF CNIを使用するクラスターでノード削減後、増設時にネットワーク障害が発生します。
Calico v3.28.0のcalico/kube-controllersで発見されたバグにより発生する問題です。ノード削減の際、calico/kube-cube-controllers Podが配布されたノードが削除されると、そのノードは他のノードにスケジューリングされて実行されます。calico/kube-controllersが再実行されている間、ノード情報が同期されません。この状態で削除したノードと同じ名前のノードが追加されると、ネットワーク障害が発生する可能性があります。

この問題はCalico v3.28.2で修正されました。Calico v3.28.2を使うためには、Kubernetesのバージョンをアップグレードするか、クラスターを再作成する必要があります。
