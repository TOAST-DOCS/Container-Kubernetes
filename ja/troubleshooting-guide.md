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


### > 閉鎖ネットワーク環境でfailed to pull image "k8s.gcr.io/pause:3.2"が発生します。

閉鎖ネットワーク環境のNKSはPublic registryからイメージを取得できないため発生する問題です。 "k8s.gcr.io/pause:3.2"イメージのようにデフォルトで配布されているイメージはワーカーノード作成時、NHN Cloud内部レジストリからpullします。クラスタ作成時にデフォルトで配布されるイメージリストは次のとおりです。
* kubernetesui/dashboard
* k8s.gcr.io/pause
* k8s.gcr.io/kube-proxy
* kubernetesui/dashboard
* kubernetesui/metrics-scraper
* quay.io/coreos/flannel
* quay.io/coreos/flannel-cni
* docker.io/calico/kube-controllers
* docker.io/calico/typha
* docker.io/calico/cni
* docker.io/calico/node
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
imageGCHighThresholdPercent : 85
imageGCLowThresholdPercent : 80
```

解決策は次のとおりです。
イメージpullに失敗した場合、次のコマンドを使用してNHN Cloud内部レジストリからイメージをpullできます。NKS 1.24.3 version以上の場合はdockerではなくnerdctlとして使用する必要があります。
```
TARGET_IMAGE="failed to pullが発生したimage"
INFRA_REGISTRY="harbor-kr1.cloud.toastoven.net/container_service/$(basename $TARGET_IMAGE)"
docker pull $INFRA_REGISTRY
docker tag $INFRA_REGISTRY $TARGET_IMAGE
docker rmi $INFRA_REGISTRY
```
