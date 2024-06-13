## Container > NHN Kubernetes Service(NKS) > バックアップガイド

## 概要

NHN Kubernetes Service(NKS)クラスタのバックアップが必要な場合、Veleroプラグインを使用してObject Storageにバックアップできます。
この文書はObject StorageとVeleroを活用してクラスタをバックアップおよび復元する方法について記述します。

* 用語整理
    * バックアップクラスタ：バックアップするクラスタを意味します。
    * 復元クラスタ：バックアップした内容を活用して復元されるクラスタを意味します。

Veleroの詳細については[Velero Docs](https://velero.io/docs/v1.9/)を参照してください。

## Veleroを利用したクラスタのバックアップと復元

### 事前準備

Object Storage APIを使用するにはテナントID(tenant ID)およびAPIエンドポイント(endpoint)の確認、APIパスワードの設定、Temporary URL Keyの作成を行う必要があります。

#### テナントIDおよびAPIエンドポイントの確認

テナントIDとAPIのエンドポイントはObject Storageサービスページの**API Endpoint設定**ボタンをクリックして確認できます。

| 項目 | API Endpoint | 用途 |
| --- | --- | --- |
| Identity | https://api-identity-infrastructure.nhncloudservice.com/v2.0 | 認証トークン発行 |
| Tenant ID | 数字 + 英字で構成された32文字の文字列 | 認証トークン発行 |

#### APIパスワード設定

APIパスワードはObject Storageサービスページの**API Endpoint設定**ボタンをクリックして設定できます。

1. **API Endpoint設定**ボタンをクリックします。
2. **API Endpoint設定**下の**APIパスワード設定**入力ダイアログボックスに、トークン発行時に使用するパスワードを入力します。
3. **保存**ボタンをクリックします。

Object Storage APIの詳細については[Object Storage APIガイド](/Storage/Object%20Storage/ja/api-guide/)を参照してください。

#### Temporary URL Keyの作成

Veleroクライアントで`velero log`コマンドを使用するにはObject StorageにTemporary URL Keyを作成する必要があります。

1. [Object Storage認証トークン発行](/Storage/Object%20Storage/ko/api-guide/#_2)を行います。
2. **API Endpoint設定**ボタンをクリックしてサービスのObject Store URLを確認します。
3. APIを利用してTemporary URL Keyを作成します。

| 名前 | 種類 | 形式 | 必須 | 説明 |
| --- | --- | --- | --- | --- |
| X-Auth-Token | Header | String | O | トークンID |
| X-Account-Meta-Temp-Url-Key | Header | String | O | Temporary URLに使用されるKey情報 |

```
$ curl -X POST {Object Store} -H "X-Auth-Token: {tokenId}" -H "X-Account-Meta-Temp-Url-Key: {key}"
```

### Veleroクライアントのインストール

Veleroクライアントはクラスタのバックアップおよび復元コマンドを入力するプログラムです。
Velero GithubリポジトリからVeleroクライアントをダウンロードして、クラスタのバックアップおよび復元に活用できます。ダウンロードしたVeleroクライアントコマンドを実行する前にバックアップおよび復元クラスタのkubeconfigファイルをWebコンソールからダウンロードし、**KUBECONFIG環境変数を設定してバックアップおよび復元対象クラスタを正確に指定**する必要があります。
kubeconfig設定の詳細については[kubectlインストール](/Container/NKS/ja/user-guide/#kubectl)を参照してください。

#### Veleroクライアントのダウンロード

```
$ wget https://github.com/vmware-tanzu/velero/releases/download/v1.9.4/velero-v1.9.4-linux-amd64.tar.gz
```

#### 解凍

```
$ tar xzf velero-v1.9.4-linux-amd64.tar.gz
```

#### 位置変更またはパス指定

どのパスからでもVeleroクライアントを実行できるように環境変数に指定されたパスに移すか、Veleroがあるパスを環境変数に追加します。

* 環境変数で指定されたパスに位置を変更

```
$ sudo mv velero-v1.9.4-linux-amd64/velero /usr/local/bin
```

* 環境変数にパスを追加

```
$ export PATH=$PATH:$(pwd)
```

### Veleroサーバーのインストール

VeleroサーバーはHelmを利用してインストールします。

#### Helmダウンロード

```
$ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
```

#### 権限変更

ダウンロードしたファイルは基本的に実行権限がありません。実行権限を追加してください。

```
$ chmod 700 get_helm.sh
```

#### Helmインストール

```
$ ./get_helm.sh
```

#### Helm Repository追加

VeleroサーバーをインストールするにはHelm Repositoryを追加する必要があります。

```
$ helm repo add vmware-tanzu https://vmware-tanzu.github.io/helm-charts
```

#### Veleroサーバーのインストール

Veleroサーバーは`バックアップクラスタ`と`復元クラスタ`にそれぞれインストールする必要があります。同じObject Storageを使用するには`2つのクラスタに同じhelmコマンド`を使用してインストールすることを推奨します。

1.26以下バージョンのクラスタにVeleroサーバーをインストールする場合、下記のコマンドを実行します。 

```
$ helm install velero vmware-tanzu/velero \
--namespace velero \
--create-namespace \
--version 2.32.6 \
--set configuration.provider=community.openstack.org/openstack \
--set initContainers[0].name=velero-plugin-for-openstack \
--set initContainers[0].image=lirt/velero-plugin-for-openstack:v0.3.0 \
--set initContainers[0].volumeMounts[0].mountPath=/target \
--set initContainers[0].volumeMounts[0].name=plugins \
--set deployRestic=true \
--set configuration.defaultVolumesToRestic=true \
--set configuration.defaultResticPruneFrequency=0h1m0s \
--set configuration.backupStorageLocation.bucket={Container} \
--set configuration.backupStorageLocation.config.region={Region} \
--set configuration.backupStorageLocation.config.resticRepoPrefix=swift:{Container}:/restic \
--set configuration.extraEnvVars.OS_AUTH_URL={IDサービス(Identity)} \
--set configuration.extraEnvVars.OS_TENANT_ID={テナントID} \
--set configuration.extraEnvVars.OS_USERNAME={NHN Cloud ID} \
--set configuration.extraEnvVars.OS_PASSWORD={APIパスワード} \
--set configuration.extraEnvVars.OS_REGION_NAME={Region} \
--set configuration.extraEnvVars.OS_DOMAIN_ID=default
```

1.27以上バージョンのクラスタにVeleroサーバーをインストールする場合、下記のコマンドを実行します。 

```
$ helm install velero vmware-tanzu/velero \
--namespace velero \
--create-namespace \
--version 2.32.6 \
--set configuration.provider=community.openstack.org/openstack \
--set initContainers[0].name=velero-plugin-for-openstack \
--set initContainers[0].image=lirt/velero-plugin-for-openstack:v0.3.0 \
--set initContainers[0].volumeMounts[0].mountPath=/target \
--set initContainers[0].volumeMounts[0].name=plugins \
--set kubectl.image.tag=1.26.14-debian-11-r6 \
--set deployRestic=true \
--set configuration.defaultVolumesToRestic=true \
--set configuration.defaultResticPruneFrequency=0h1m0s \
--set configuration.backupStorageLocation.bucket={Container} \
--set configuration.backupStorageLocation.config.region={Region} \
--set configuration.backupStorageLocation.config.resticRepoPrefix=swift:{Container}:/restic \
--set configuration.extraEnvVars.OS_AUTH_URL={IDサービス(Identity)} \
--set configuration.extraEnvVars.OS_TENANT_ID={テナントID} \
--set configuration.extraEnvVars.OS_USERNAME={NHN Cloud ID} \
--set configuration.extraEnvVars.OS_PASSWORD={APIパスワード} \
--set configuration.extraEnvVars.OS_REGION_NAME={Region} \
--set configuration.extraEnvVars.OS_DOMAIN_ID=default
```

| 項目 | 説明 |
| --- | --- |
| Container | Object Sotrageで使用するコンテナ名 |
| Region | 韓国(パンギョ)リージョン：`KR1`<br>韓国(坪村)リージョン：`KR2` |
| IDサービス(Identity) | API Endpoint設定のIDサービス(Identity) |
| テナントID | API Endpoint設定のテナントID |
| NHN Cloud ID | NHN Cloud ID |
| APIパスワード | API Endpoint設定に入力したAPIパスワード |

#### Veleroサーバー削除
Veleroサーバーは`velero uninstall`コマンドで削除できます。

### クラスタのバックアップ

クラスタのバックアップは`velero backup create`コマンドで設定できます。

```
$ export KUBECONFIG={バックアップクラスタのkubeconfigファイル}
$ velero backup create {name} --exclude-namespaces kube-system,velero
```

* nameは任意のバックアップ名を指定します。
* クラスタのバックアップ時にResource Filteringを設定できます。詳細については[resource-filtering](https://velero.io/docs/v1.7/resource-filtering/)を参照してください。

> [注意]
> `kube-system`、`velero`などのバックアップが必要ないnamespaceは除外する必要があります。
> バックアップに含まれると復元時に問題が発生する可能性があります。

クラスタのバックアップ状態は`velero backup get`コマンドで確認できます。

```
$ velero backup get
NAME         STATUS      ERRORS   WARNINGS   CREATED                         EXPIRES   STORAGE LOCATION   SELECTOR
my-backup    Completed   0        0          2022-02-09 10:13:44 +0900 KST   29d       default            <none>
```

* バックアップされた情報はObject Storageサービスページで確認できます。

### クラスタの復元

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

### 例

#### クラスタバックアップ/復元例

* バックアップクラスタでvelero backup createコマンドを使用してバックアップします。

```
$ velero backup create my-backup --exclude-namespaces kube-system,velero
```

* velero backup getコマンドを利用してバックアップ状態を確認します。

```
$ velero backup get
NAME         STATUS      ERRORS   WARNINGS   CREATED                         EXPIRES   STORAGE LOCATION   SELECTOR
my-backup    Completed   0        0          2022-02-09 13:23:13 +0900 KST   29d       default            <none>
```

* 復元クラスタでvelero restore createコマンドを使用して復元します。

```
$ velero restore create --from-backup my-backup
```

* kubectlを使用して復元情報を確認します。

```
$ kubectl get pod --all-namespaces
```

#### 定期的バックアップの設定例

`velero schedule create`コマンドで定期的なバックアップを設定できます。詳細については[schedule-a-backup](https://velero.io/docs/v1.7/backup-reference/#schedule-a-backup)を参照してください。

* バックアップクラスタでvelero schedule createコマンドを使用して定期的なバックアップを設定します。 (例は10分間隔)

```
$ velero schedule create my-schedule --schedule="*/10 * * * *" --exclude-namespaces kube-system,velero
```

* velero backup getコマンドを使用して、設定した時間間隔でバックアップされることを確認できます。

```
$ velero backup get
NAME                          STATUS      ERRORS   WARNINGS   CREATED                         EXPIRES   STORAGE LOCATION   SELECTOR
my-schedule-20220209044049    Completed   0        0          2022-02-09 13:40:49 +0900 KST   29d       default            <none>
my-schedule-20220209043115    Completed   0        0          2022-02-09 13:31:15 +0900 KST   29d       default            <none>
```

#### 定期的バックアップ設定の解除例
`velero schedule delete`コマンドで定期的なバックアップを解除できます。

* バックアップクラスタでvelero schedule getコマンドを使用して設定情報を確認します。

```
$ velero schedule get
NAME          STATUS    CREATED                         SCHEDULE       BACKUP TTL   LAST BACKUP   SELECTOR
my-schedule   Enabled   2022-03-17 13:48:53 +0900 KST   */10 * * * *   720h0m0s     4s ago        <none>
```


* velero schedule deleteコマンドを使用して定期的なバックアップ設定を解除します。

```
$ velero schedule delete my-schedule
Are you sure you want to continue (Y/N)? y
Schedule deleted: my-schedule
```
