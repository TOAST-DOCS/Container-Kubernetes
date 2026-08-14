<!-- machine_translated: true -->

## Container > NHN Kubernetes Service(NKS) > トラブルシューティングガイド

NHN Kubernetes Service(NKS)を使用する際に発生する可能性のあるさまざまな問題の解決方法を説明します。

### > ワーカーノードのコンテナログファイルサイズが大きくなり、ディスクスペースが減ります。

#### ログローテーションを設定する
コンテナログファイルの管理（最大ファイルサイズ、ログファイル数の設定など）のために、ワーカーノードに以下の設定を追加します。

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

* ノードグループ間でインスタンスイメージが異なる場合
    * ログローテーション設定適用イメージベースのノード vs 未適用イメージベースのノード
  * ログローテーション設定未適用イメージベースのノードに直接設定を追加した場合
    * クラスターオートスケーラーまたはノードグループのサイズ調整によって追加された新規ノード vs 既存ノード
  * ログローテーション設定内容を直接変更・適用した場合
    * クラスターオートスケーラーまたはノードグループのサイズ調整によって追加された新規ノード vs 既存ノード

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


### > Podの状態がImagePullBackOffと表示されます。

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


* Docker Hubにログインすると、イメージをプルできる数が増加し、パブリックIPによる制限ではなくアカウントのティアごとの制限が適用されます。Docker Hubアカウントを作成し、希望するプル数を提供するティアに登録してNKSを使用します。[KubernetesでのPrivate Registryの使用方法](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/)を参照してください。
* Docker Hubにログインしていない状態で、独立したパブリックIPによる制限を受けたい場合は、ワーカーノードにFloating IPを割り当てます。

### > クローズドネットワーク環境でfailed to pull image `k8s.gcr.io/pause:3.2`が発生します。
クローズドネットワーク環境のクラスターがパブリックレジストリからイメージを取得できないために発生する問題で、2024 年 8 月以前に作成されたクラスターで発生する場合があります。`k8s.gcr.io/pause:3.2` イメージのようにデフォルトでデプロイされているイメージは、ワーカーノード作成時に NHN Cloud 内部レジストリから pull されます。ただし、最初にイメージを pull した後にイメージが削除された場合、問題が発生する可能性があります。クラスター作成時にデフォルトでデプロイされるイメージの一覧は次のとおりです。

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

該当のイメージについても同様の問題が発生する可能性があります。

基本イメージは、kubelet の Image garbage collection によって削除される場合があります。kubelet garbage collection の詳細については、[Garbage Collection](https://kubernetes.io/docs/concepts/architecture/garbage-collection/) を参照してください。NKS の場合、imageGCHighThresholdPercent、imageGCLowThresholdPercent はデフォルト値に設定されています。
```
imageGCHighThresholdPercent=85 : ディスク使用率が 85% を超える場合、常にイメージ Garbage Collection を実行し、使用していないイメージを削除します。
imageGCLowThresholdPercent=80 : ディスク使用率が 80% 以下の場合、イメージ Garbage Collection を実行しません。
```

#### 解決策
NKS レジストリを有効にすると、閉域網環境でコンテナイメージをパブリックレジストリから取得せず、NHN Cloud 内部レジストリから取得するようにクラスター設定を変更できます。NKS レジストリはクラスター照会画面から有効にできます。

### > `quay.io`からFlannel CNI関連イメージのpullが失敗します。

Flannel関連コンテナイメージのリポジトリドメインは`quay.io`をベースに設定されています。`quay.io`で該当イメージに対するpullサービスが終了したため、該当イメージをpullできなくなりました。

この問題を解決する方法は次のとおりです。

* 方法1: NKSレジストリの有効化
    * NKSレジストリを有効にすると、Flannelを含む必須コンテナのリポジトリアドレスをNKS内部アドレスに変更するため、イメージpullの問題が発生しません。
* 方法2: Flannel関連イメージのリポジトリアドレス変更
    * イメージpullが可能なリポジトリアドレスに変更します。
    * 対象リソース: `kube-flannel`または`kube-system` namespaceの`kube-flannel-ds-amd64` DaemonSet
    * 変更前: `quay.io/coreos/flannel*`
    * 変更後: `ghcr.io/flannel-io/flannel*`
    
### > k8s v1.24 以上のバージョンで `pulling from host docker.pkg.github.com failed` エラーが発生し、イメージpullが失敗します。

githubのパッケージレジストリがDockerレジストリからContainerレジストリに変更されたため発生した問題です。 v1.24以前のバージョンのクラスタはコンテナランタイムとしてDockerを使用して `docker.pkg.github.com` レジストリからイメージpullが可能でしたが、v1.24以降のバージョンのNKSクラスタはコンテナランタイムとしてcotainerdを使用するため、`docker.pkg.github.com` レジストリからイメージpullができません。パッケージレジストリの移行に関する詳細は[Migration to Container registry from the Docker registry](https://docs.github.com/en/packages/working-with-a-github-packages-registry/migrating-to-the-container-registry-from-the-docker-registry) を参照してください。


解決方法は次のとおりです。
Podマニフェストに定義されたimage URLのbaseを`docker.pkg.github.com`から`gchr.io`に変更します。

### > `cannot allocate memory`エラーが発生し、Podの状態が`FailedCreatePodContainer`と表示されます。

Linuxカーネルの機能の中でmemory cgroupに対するkernel object accounting機能のバグで発生する現象です。主にLinuxカーネル3.x, 4.xバージョンで発生し、dying memory cgroup problem問題として知られています。ユーザーがイメージレベルでmemory cgroupに対するkernel object accounting機能を無効にしてこの問題を回避できます。

#### 作成済みのクラスタに解決策を適用
ワーカーノードに接続してブートオプションを変更し、再起動します。

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
NKS では、ユーザーのカスタムイメージを基にしたワーカーノードグループを作成する機能を提供しています。NKS カスタムイメージ機能を使用して、memory cgroup に対する kernel object accounting 機能が無効化されたイメージを作成し、クラスター作成時に活用できます。カスタムイメージ使用機能の詳細については、[カスタムイメージをワーカーイメージとして活用](/Container/NKS/ja/user-guide/#custom-image)を参照してください。

1. イメージテンプレート作成過程でユーザースクリプトに下記の内容を入力します。
```
#!/bin/bash
args="cgroup.memory=nokmem"
grub_file="/etc/default/grub"
sudo sed -i "s/GRUB_CMDLINE_LINUX=\"\(.*\)\"/GRUB_CMDLINE_LINUX=\"\1 $args\"/" "$grub_file"
sudo grub2-mkconfig -o /boot/grub2/grub.cfg
```


### > rpc.statd is not running but is required for remote lockingエラーが発生し、PodでNASボリュームマウントが失敗します。

ワーカーノードのrpc.statdプロセスがゾンビプロセスになったり、管理者のコマンドによって停止して発生する問題です。ボリュームをマウントするためには、ワーカーノードでrpcbindとrpc.statdプロセスが正常に実行されている必要があります。解決策は次のとおりです。
```
systemctl restart rpc-statd
systemctl restart rpcbind
```

### > PV容量を増設しても、Podのファイルシステムに増設された容量が反映されません。
2024年8月以前に作成されたバージョン 1.20 以降のクラスターで発生する可能性がある問題です。以下のスクリプトを実行することで、クラスターにデプロイされた cinder-csi-driver をアップデートして問題を解決できます。スクリプト実行後、新規作成または容量拡張された PV にのみ設定のアップデートが反映されます。

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
Pod に大きなサイズのボリュームをマウントする場合に発生する可能性がある問題です。デフォルトで Kubernetes は、ボリュームをマウントする際に Pod の SecurityContext に指定された fsGroup と一致するよう、各ボリュームの内容に対する所有権と権限を変更します。ボリュームが大きい場合、所有権と権限の確認および変更に多くの時間がかかり、タイムアウトが発生する場合があります。

タイムアウトの発生を防ぐために、securityContextのfsGroupChangePolicyフィールドを使用して、Kubernetesがボリュームの所有権と権限を確認して管理する方法を変更できます。詳細は[Configure volume permission and ownership change policy for pods](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/#configure-volume-permission-and-ownership-change-policy-for-pods)を参照してください。

### > Calico-eBPF CNIを使用するクラスターのPodにhostnetwork: true, dnsPolicy: ClusterFirstWithHostNetオプションを設定すると、UDP通信の問題が発生します。
Calico v3.28.0 において、UDP 通信中に BPF NAT テーブルがネットワークパケットを正しく処理できないことで発生する問題です。eBPF を使用する場合、TCP は `CTLB(connect-time load balancing)` 方式で通信し、UDP は BPF が管理する `NAT テーブル` を通じて通信します。この問題は、UDP 通信も CTLB 方式に変更することで解決できます。

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
UDP は非接続型プロトコルであり、サーバー/クライアント間の通信時に別途セッションを確立したり接続を維持したりせずにデータを送信します。しかし、Golang の `net.DialUDP()` 関数のような UDP の `connect()` 関数を使用すると、UDP ソケットを特定のアドレスと接続し、指定されたアドレスにのみデータを送受信できます。
Calico の eBPF を使用する場合、UDP に CTLB(connect-time load balancing) が有効なクラスターに UD `connect()` 関数を使用する Pod をデプロイした場合、サーバーの役割を担う Pod が再デプロイされると通信の問題が発生する場合があります。これは、UDP ソケットが初期接続されたサーバーアドレスにのみデータ送信を試みるためです。サーバー Pod が再デプロイされると IP アドレスやネットワーク経路が変更される場合があり、UDP connect() ソケットは以前のサーバーアドレスにのみデータを送信するため、通信に失敗する可能性があります。
この問題は UDP connect() の動作方式と CTLB 環境で発生する既知の問題であるため、Calico eBPF と UDP CTLB を使用するクラスターで UDP の connect() 関数を使用する場合は、このような通信の問題が発生する可能性があることを認識し、注意が必要です。

### > Calico-eBPFクラスターでistioが誤動作します。
eBPF が有効になると、`CTLB(connect-time load balancing)` 方式で接続時点にロードバランシングを実行し、クライアントがサービスへの接続を試みる際に最初のパケットでバックエンド Pod を選択し、以降のすべてのパケットはそのバックエンドに直接転送されます。一方、Istio はサービスメッシュを構成するためにサイドカープロキシをデプロイし、プロキシはアプリケーショントラフィックを傍受して制御および監視の役割を担います。
CTLB が有効な場合、パケットは BPF MAP から目的地の Pod に直接転送され、この過程でパケットが変換されます。そのため、Istio のプロキシを経由せず、パケットが直接目的地の Pod に転送されます。このような eBPF ネットワーキング構造により、Istio の機能が正常に動作しない場合があります。istio を使用したクラスター管理が必要な場合は、Calico-VXLAN クラスターの使用を検討する必要があります。

### > Calico-eBPF CNIを使用するクラスターでノード削減後、増設時にネットワーク障害が発生します。
Calico v3.28.0 の calico/kube-controllers で発見されたバグにより発生する問題です。ノード削減の進行時に calico/kube-controllers Pod がデプロイされたノードが削除されると、その Pod は別のノードにスケジューリングされて実行されます。calico/kube-controllers が再実行される間、ノード情報が同期されません。この状態で削除したノードと同じ名前のノードが追加されると、ネットワーク障害が発生する場合があります。

この問題はCalico v3.28.2で修正されました。Calico v3.28.2を使うためには、Kubernetesのバージョンをアップグレードするか、クラスターを再作成する必要があります。

### > クラスターアップグレードに失敗します。

#### NKS作成時に基本的に配布されるリソースにfinalizersが設定されているかどうかを確認する必要があります。
NKS 作成時にデプロイされたリソースに finalizers が設定されている場合、リソースを削除できずアップグレードが失敗します。すべてのワーカーノードグループのアップグレードが完了すると、NKS 初期デプロイリソースが再デプロイされます。この過程で NKS 初期デプロイリソースに finalizers が設定されていると、リソースの再デプロイに失敗してアップグレードが中断されます。この問題を解決するには、アップグレード前に NKS 初期デプロイリソースの finalizers 設定を削除する必要があります。

finalizers設定を削除するコマンドは次のとおりです。
```
kubectl patch {リソースタイプ} {リソース名} -n {名前空間} --type=json -p='[{"op": "remove", "path": "/metadata/finalizers"}]'
```
例
```
kubectl patch clusterrole calico-kube-controllers --type=json -p='[{"op": "remove", "path": "/metadata/finalizers"}]'
```


### > NKSレジストリが非活性状態のv1.29.3以下バージョンのクラスタで、ノード増設またはノードグループ追加時にcalico-node Podのデプロイに失敗し、ノード初期化作業に失敗します。
誤ったイメージリポジトリの設定により、ノード増設またはノードグループ追加時に calico 関連の Pod（calico-node、calico-kube-controllers、calico-typha）がデプロイされないことで発生する問題です。

この問題は主に2024年5月以前に作成されたクラスタで発生する可能性があります。当時作成されたクラスタはNKS専用イメージレジストリが基本的に非活性状態であり、Calicoコンテナイメージのリポジトリパスが正しくないため、イメージのダウンロードが不可能なことが原因です。

#### 症状発生時の確認方法
`kubectl get all -n kube-system` コマンドで確認すると、増設作業が失敗したノードにデプロイされている以下の Pod のステータスが **ImagePullBackOff** または **ErrImagePull** のままになります。
- calico-node
- calico-kube-controllers
- calico-typha

#### 解決策
calico 関連のイメージリポジトリ URL をパブリックリポジトリに変更することで問題を解決できます。ただし、インターネットに接続可能なクラスターにのみ適用可能です。作業中に一時的にクラスターの Pod ネットワークが切断される場合があるため、作業時には注意が必要です。作業手順は以下のとおりです。

1. 増設に失敗したノードの削除
2. calico関連imageリポジトリurlをpublicリポジトリに変更
3. ノード増設作業の進行

calico関連imageリポジトリurlをpublicリポジトリに変更するコマンドは次のとおりです。
${CALICO_TAG}は、現在クラスタで使用中のCalicoバージョンを意味します。

**calico-node Daemonsetイメージrepo変更**
```
kubectl -n kube-system set image daemonset/calico-node \
  calico-node=calico/node:${CALICO_TAG} \
  install-cni=calico/cni:${CALICO_TAG} \
  mount-bpffs=calico/node:${CALICO_TAG}
[例]
kubectl -n kube-system set image daemonset/calico-node \
  calico-node=calico/node:v3.24.1 \
  install-cni=calico/cni:v3.24.1 \
  mount-bpffs=calico/node:v3.24.1
```

**calico-typha Deploymentイメージrepo変更**
```
kubectl -n kube-system set image deployment/calico-typha \
  calico-typha=calico/typha:${CALICO_TAG}
[例]
kubectl -n kube-system set image deployment/calico-typha \
  calico-typha=calico/typha:v3.24.1
```

**calico-kube-controller Deploymentイメージrepo変更**
```
kubectl -n kube-system set image deployment/calico-kube-controllers \
  calico-kube-controllers=calico/kube-controllers:${CALICO_TAG}
[例]
kubectl -n kube-system set image deployment/calico-kube-controllers \
  calico-kube-controllers=calico/kube-controllers:v3.24.1
```
### > GPU フレーバーのワーカーノードの GPU 関連モニタリング情報が表示されません。
dcgm-exporter が参照するライブラリリンクに問題があることで発生します。dcgm-exporter が `libdcgm.so.4` ライブラリを見つけられず実行に失敗し、その結果 GPU 関連のモニタリング指標が収集されません。

この問題は、以下のイメージを使用する GPU ワーカーノードで発生します。
* Rocky Linux 8.10 - Container (2026.03.10)
* Rocky Linux 9.7 - Container (2026.03.10)
* Ubuntu Server 22.04.5 LTS - Container (2026.03.10)
* Ubuntu Server 24.04.4 LTS - Container (2026.03.10)

#### 症状が発生した場合の確認方法
GPUワーカーノードでdcgm-exporterを実行すると、次のようなエラーログが出力されます。
```
# /usr/bin/dcgm-exporter --address localhost:9400
time=2026-08-06T00:13:18.786+09:00 level=INFO msg="Starting dcgm-exporter" Version=4.4.0-4.5.0
time=2026-08-06T00:13:18.792+09:00 level=ERROR msg="the libdcgm.so.4 library was not found. Install Data Center GPU Manager (DCGM)."
```

#### 解決方法
この問題は、2026年8月の定期メンテナンス時に対応される予定です。定期メンテナンスまでの間は、各GPUワーカーノードで次のコマンドを実行して一時的に対処できます。
```
sed -i 's/DCGM_FI_PROF/#DCGM_FI_PROF/g' /etc/dcgm-exporter/default-counters.csv
ldconfig && systemctl restart dcgm-exporter.service
```
