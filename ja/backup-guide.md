<!-- machine_translated: true -->

<!-- pre-align:aligned sig=dd3a912dfca5 -->

<a id="container-nhn-kubernetes-service-nks-backup-guide"></a>
## Container > NHN Kubernetes Service(NKS) > バックアップガイド { #container-nhn-kubernetes-service-nks-backup-guide }

<a id="overview"></a>
## 概要 { #overview }

NHN Kubernetes Service(NKS)クラスタのバックアップが必要な場合、Veleroプラグインを使用してObject Storageにバックアップできます。
この文書はObject StorageとVeleroを活用してクラスタをバックアップおよび復元する方法について記述します。

* 用語整理
    * バックアップクラスタ：バックアップするクラスタを意味します。
    * 復元クラスタ：バックアップした内容を活用して復元されるクラスタを意味します。

Veleroの詳細については[Velero Docs](https://velero.io/docs/v1.9/)を参照してください。

<a id="cluster-backup-and-restoration-with-velero"></a>
## Veleroを利用したクラスタのバックアップと復元 { #cluster-backup-and-restoration-with-velero }

<a id="prerequisites"></a>
### 事前準備 { #prerequisites }

Object Storage APIを使用するにはテナントID(tenant ID)およびAPIエンドポイント(endpoint)の確認、APIパスワードの設定、Temporary URL Keyの作成を行う必要があります。

<a id="prerequisites-check-the-tenant-id-and-api-endpoint"></a>
#### テナントIDおよびAPIエンドポイントの確認

テナントIDとAPIのエンドポイントはObject Storageサービスページの**API Endpoint設定**ボタンをクリックして確認できます。

| 項目 | API Endpoint | 用途 |
| --- | --- | --- |
| Identity | https://api-identity-infrastructure.nhncloudservice.com/v2.0 | 認証トークン発行 |
| Tenant ID | 数字 + 英字で構成された32文字の文字列 | 認証トークン発行 |

<a id="prerequisites-set-an-api-password"></a>
#### APIパスワード設定

APIパスワードはObject Storageサービスページの**API Endpoint設定**ボタンをクリックして設定できます。

1. **API Endpoint設定**ボタンをクリックします。
2. **API Endpoint設定**下の**APIパスワード設定**入力ダイアログボックスに、トークン発行時に使用するパスワードを入力します。
3. **保存**ボタンをクリックします。

Object Storage APIの詳細については[Object Storage APIガイド](/Storage/Object%20Storage/ja/api-guide/)を参照してください。

<a id="prerequisites-create-temporary-url-key"></a>
#### Temporary URL Keyの作成

Veleroクライアントで`velero log`コマンドを使用するにはObject StorageにTemporary URL Keyを作成する必要があります。

1. [Object Storage 認証および権限](/Storage/Object%20Storage/ja/api-guide/#auth)を参照して、トークンを発行します。
2. **[API Endpoint 設定]** ボタンをクリックして、サービスのObject Store URLを確認します。
3. APIを使用して、Temporary URL Keyを生成します。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-Auth-Token | Header | String | O | トークンID |
| X-Account-Meta-Temp-Url-Key | Header | String | O | Temporary URLに使用されるKey情報 |

```
$ curl -X POST {Object Store} -H "X-Auth-Token: {tokenId}" -H "X-Account-Meta-Temp-Url-Key: {key}"
```

<a id="install-the-velero-client"></a>
### Veleroクライアントのインストール { #install-the-velero-client }

Velero クライアントは、クラスターのバックアップおよびリストアコマンドを入力するプログラムです。
Velero GitHub リポジトリから Velero クライアントをダウンロードして、クラスターのバックアップおよびリストア時に活用できます。ダウンロードした Velero クライアントのコマンドを実行する前に、バックアップおよびリストア対象クラスターの kubeconfigファイルをウェブコンソールからダウンロードし、**KUBECONFIG 環境変数を設定してバックアップおよびリストア対象クラスターを正確に指定**する必要があります。


kubeconfigの設定の詳細については、[kubectl のインストール](./user-guide/#kubectl-install)を参照してください。

<a id="install-the-velero-client-download-the-velero-client"></a>
#### Veleroクライアントのダウンロード

```
$ wget https://github.com/vmware-tanzu/velero/releases/download/v1.17.0/velero-v1.17.0-linux-amd64.tar.gz
```

<a id="install-the-velero-client-decompress-the-file"></a>
#### 解凍

```
$ tar xzf velero-v1.17.0-linux-amd64.tar.gz
```

<a id="install-the-velero-client-change-the-location-or-set-the-path"></a>
#### 位置変更またはパス指定

どのパスからでもVeleroクライアントを実行できるように環境変数に指定されたパスに移すか、Veleroがあるパスを環境変数に追加します。

* 環境変数で指定されたパスに位置を変更

```
$ sudo mv velero-v1.17.0-linux-amd64/velero /usr/local/bin
```

* 環境変数にパスを追加

```
$ export PATH=$PATH:$(pwd)
```

<a id="install-the-velero-server"></a>
### Veleroサーバーのインストール { #install-the-velero-server }

VeleroサーバーはHelmを利用してインストールします。

<a id="install-the-velero-server-download-helm"></a>
#### Helmダウンロード

```
$ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
```

<a id="install-the-velero-server-change-a-permission"></a>
#### 権限変更

ダウンロードしたファイルは基本的に実行権限がありません。実行権限を追加してください。

```
$ chmod 700 get_helm.sh
```

<a id="install-the-velero-server-install-helm"></a>
#### Helmインストール

```
$ ./get_helm.sh
```

<a id="install-the-velero-server-add-the-helm-repository"></a>
#### Helm Repository追加

VeleroサーバーをインストールするにはHelm Repositoryを追加する必要があります。

```
$ helm repo add vmware-tanzu https://vmware-tanzu.github.io/helm-charts
```

<a id="install-the-velero-server-2"></a>
#### Veleroサーバーのインストール

Veleroサーバーは`バックアップクラスタ`と`復元クラスタ`にそれぞれインストールする必要があります。同じObject Storageを使用するには`2つのクラスタに同じhelmコマンド`を使用してインストールすることを推奨します。

##### Object Storage S3認証secretの作成

Object Storageコンソールで**S3 API認証情報**としてaccess_key、secret_keyを作成し、次のようにファイルを作成します。
```
cat > credentials-velero <<EOF
[default]
aws_access_key_id=${access_keyの値}
aws_secret_access_key=${secret_keyの値}
EOF
```

veleroからObject Storageへのアクセス時に認証に使用するsecretを作成します。
```
kubectl create namespace velero --dry-run=client -o yaml | kubectl apply -f -
kubectl create secret generic cloud-credentials \
  --namespace velero \
  --from-file=cloud=credentials-velero
```

##### velero install helm

```
$ helm install velero vmware-tanzu/velero \
--namespace velero --create-namespace \
--version 11.0.0 \
--set snapshotsEnabled=false \
--set credentials.useSecret=true \
--set credentials.existingSecret=cloud-credentials \
--set deployNodeAgent=true \
--set-string configuration.backupStorageLocation[0].config.checksumAlgorithm="" \
--set configuration.defaultVolumesToFsBackup=true \
--set configuration.uploaderType=kopia \
--set initContainers[0].name=velero-plugin-for-aws \
--set initContainers[0].image=velero/velero-plugin-for-aws:v1.13.2 \
--set initContainers[0].volumeMounts[0].name=plugins \
--set initContainers[0].volumeMounts[0].mountPath=/target \
--set configuration.defaultRepoMaintainFrequency=1m \
--set configuration.backupStorageLocation[0].name=default \
--set configuration.backupStorageLocation[0].provider=aws \
--set-string configuration.backupStorageLocation[0].bucket={Container} \
--set-string configuration.backupStorageLocation[0].config.region="{Region}" \
--set-string configuration.backupStorageLocation[0].config.s3Url="${OBS endpoint}" \   
--set-string configuration.backupStorageLocation[0].config.s3ForcePathStyle=true
```

| 項目 | 説明 |
| --- | --- |
| Container | Object Sotrageで使用するコンテナ名 |
| Region | 韓国(パンギョ)リージョン：`KR1`<br>韓国(坪村)リージョン：`KR2`<br>韓国(クァンジュ)リージョン: `KR3` |
| OBS endpoint | Object Storage API Endpoint |

<a id="install-the-velero-server-delete-the-velero-server"></a>
#### Veleroサーバー削除
Veleroサーバーは`velero uninstall`コマンドで削除できます。

<a id="back-up-a-cluster"></a>
### クラスタのバックアップ { #back-up-a-cluster }

クラスタのバックアップは`velero backup create`コマンドで設定できます。

```
$ export KUBECONFIG={バックアップクラスタのkubeconfigファイル}
$ velero backup create {name} --exclude-namespaces kube-system,velero
```

* nameは任意のバックアップ名を指定します。
* クラスタのバックアップ時にResource Filteringを設定できます。詳細については[resource-filtering](https://velero.io/docs/v1.17/resource-filtering/)を参照してください。

> [注意]
> `kube-system`、`velero`などのバックアップが必要ないnamespaceは除外する必要があります。
> バックアップに含まれると復元時に問題が発生する可能性があります。

クラスタのバックアップ状態は`velero backup get`コマンドで確認できます。

```
$ velero backup get
NAME         STATUS      ERRORS   WARNINGS   CREATED                         EXPIRES   STORAGE LOCATION   SELECTOR
my-backup    Completed   0        0          2025-10-13 11:01:53 +0900 KST   29d       default            <none>
```

* バックアップされた情報はObject Storageサービスページで確認できます。

<a id="restore-a-cluster"></a>
### クラスタの復元 { #restore-a-cluster }

クラスタバックアップ/復元は`velero restore create`コマンドで設定できます。

```
$ export KUBECONFIG={復元クラスタのkubeconfigファイル}
$ velero restore create --from-backup {name}
```

* nameにバックアップ名を指定すると、そのバックアップの内容どおりにクラスタが復元されます。

> [注意]
> ストレージクラス(StorageClass)のリソースは、バックアップおよび復元されないため、復元前に`バックアップクラスタ`に存在するものと同じ名前のストレージクラスを`復元クラスタ`にあらかじめ作成しておく必要があります。
> [注意]
> `バックアップクラスタ`と`復元クラスタ`のバージョンが異なる場合、復元時に問題が発生する可能性があります。

<a id="examples"></a>
### 例 { #examples }

<a id="examples-example-of-cluster-backup-and-restoration"></a>
#### クラスタバックアップ/復元例

* バックアップクラスタでvelero backup createコマンドを使用してバックアップします。

```
$ velero backup create my-backup --exclude-namespaces kube-system,velero
```

* velero backup getコマンドを利用してバックアップ状態を確認します。

```
$ velero backup get
NAME         STATUS      ERRORS   WARNINGS   CREATED                         EXPIRES   STORAGE LOCATION   SELECTOR
my-backup    Completed   0        0          2025-10-13 11:01:53 +0900 KST   29d       default            <none>
```

* 復元クラスタでvelero restore createコマンドを使用して復元します。

```
$ velero restore create --from-backup my-backup
```

* kubectlを使用して復元情報を確認します。

```
$ kubectl get pod --all-namespaces
```

<a id="examples-example-of-setting-periodic-backups"></a>
#### 定期的バックアップの設定例

`velero schedule create`コマンドで定期的なバックアップを設定できます。詳細については[schedule-a-backup](https://velero.io/docs/v1.17/backup-reference/#schedule-a-backup)を参照してください。

* バックアップクラスタでvelero schedule createコマンドを使用して定期的なバックアップを設定します。 (例は10分間隔)

```
$ velero schedule create my-schedule --schedule="*/10 * * * *" --exclude-namespaces kube-system,velero
```

* velero backup getコマンドを使用して、設定した時間間隔でバックアップされることを確認できます。

```
$ velero backup get
NAME                         STATUS      ERRORS   WARNINGS   CREATED                         EXPIRES   STORAGE LOCATION   SELECTOR
my-schedule-20251013055022   Completed   0        0          2025-10-13 14:50:22 +0900 KST   29d       default            <none>
my-schedule-20251013054022   Completed   0        0          2025-10-13 14:40:22 +0900 KST   29d       default            <none>
```

<a id="examples-example-of-clearing-periodic-backups"></a>
#### 定期的バックアップ設定の解除例
`velero schedule delete`コマンドで定期的なバックアップを解除できます。

* バックアップクラスタでvelero schedule getコマンドを使用して設定情報を確認します。

```
$ velero schedule get
NAME          STATUS    CREATED                         SCHEDULE       BACKUP TTL   LAST BACKUP   SELECTOR   PAUSED
my-schedule   Enabled   2025-10-13 14:38:11 +0900 KST   */10 * * * *   0s           18s ago       <none>     false
```


* velero schedule deleteコマンドを使用して定期的なバックアップ設定を解除します。

```
$ velero schedule delete my-schedule
Are you sure you want to continue (Y/N)? y
Schedule deleted: my-schedule
```
