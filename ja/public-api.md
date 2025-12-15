## Container > NHN Kubernetes Service(NKS) > API v2ガイド

Kubernetesクラスタを構成するためのAPIを記述します。
APIを使用するにはAPIエンドポイントとトークンなどが必要です。 [API使用準備](/Compute/Compute/ja/identity-api/)を参照してAPIの使用に必要な情報を準備します。

すべてのAPIは`kubernetes`タイプエンドポイントを利用して呼び出します。

| タイプ | リージョン | エンドポイント |
|---|---|---|
| kubernetes | 韓国(パンギョ)リージョン<br>韓国(坪村)リージョン | https://kr1-api-kubernetes-infrastructure.nhncloudservice.com <br>https://kr2-api-kubernetes-infrastructure.nhncloudservice.com |


APIレスポンスにガイドに明示されていないフィールドが表示されることがあります。これらのフィールドはNHN Cloud内部用途で使用され、予告なしに変更されることがあるため使用しません。

## APIに使用されるリソース情報の確認

NHN Kubernetes Service(NKS)APIは、クラスタおよびノードグループを構成するために複数のリソースを使用します。各リソースの情報確認方法は次のとおりです。

### インターネットゲートウェイに接続されたVPCネットワークUUID

インターネットゲートウェイに接続されたVPCネットワークは、VPCネットワークリスト照会APIに**router:external=True**クエリパラメータを利用して照会できます。

```
GET /v2.0/networks?router:external=True
```

ネットワークリスト照会APIの詳細については、[ネットワークリスト表示](/Network/VPC/ja/public-api/#_2)を参照してください。


### インターネットゲートウェイに接続されたサブネットUUIDリスト

インターネットゲートウェイに接続されたVPCネットワークに接続されたサブネットUUIDを入力します。複数のサブネットが検索された場合はコロン(`:`)でつなげて入力します。サブネットリスト照会APIの詳細については、[サブネットリスト表示](/Network/VPC/ja/public-api/#vpc_7)を参照してください。


### VPCネットワークUUID

ノードと接続する内部VPCネットワークUUIDを入力します。ネットワークリスト照会APIの詳細については[ネットワークリスト表示](/Network/VPC/ja/public-api/#vpc_1)を参照してください。

### VPCサブネットUUID

ノードと接続する内部VPCネットワークに接続されたサブネットUUIDを入力します。サブネットリスト照会APIの詳細については[サブネットリスト表示](/Network/VPC/ja/public-api/#vpc_7)を参照してください。

### アベイラビリティゾーンUUID

ノードを作成するアベイラビリティゾーンUUIDを入力します。アベイラビリティゾーンリスト照会APIの詳細については[可用性リスト表示](/Compute/Instance/ja/public-api/#_9)を参照してください。

### キーペアUUID

ノード接続時に使用するキーペアを入力します。キーペアリスト照会APIの詳細については[キーペアリスト表示](/Compute/Instance/ja/public-api/#_13)を参照してください。

### ベースイメージUUID

ノードの作成に使用するベースイメージのUUIDを入力します。NKSノードの作成に使用されるベースイメージだけをフィルタリングするため、API呼び出し時にクエリ文字列パラメータに`nhncloud_allow_nks_cpu_flavor=true&visibility=public`を入力します。ベースイメージリスト照会APIの詳細については、[イメージリスト照会](/Compute/Image/ja/public-api/#_2)を参照してください。

### ブロックストレージの種類

ノードに使用するブロックストレージUUIDを入力します。ブロックストレージタイプリスト照会APIの詳細については[ボリュームタイプリスト表示](/Storage/Block%20Storage/ja/public-api/#_2)を参照してください。

### インスタンスタイプUUID

作成するノードのインスタンスタイプUUIDを入力します。インスタンスタイプリスト照会APIの詳細については[インスタンスタイプリスト表示](/Compute/Instance/ja/public-api/#_2)を参照してください。



## クラスタ

### クラスタリスト表示

クラスタリストを照会します。

```
GET /v1/clusters
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |

#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| clusters | Body | Array | クラスタ情報オブジェクトリスト |
| clusters.uuid | Body | UUID | クラスタUUID |
| clusters.name | Body | String | クラスタ名 |
| clusters.flavor_id | Body | UUID | 基本ワーカーノードのインスタンスタイプUUID|
| clusters.keypair | Body | UUID | 基本ワーカーノードグループに適用されたキーペアUUID |
| clusters.node_count | Body | Integer| ワーカーノードの総数 |
| clusters.stack_id | Body | UUID | コントロールプレーンと接続されたheat stack UUID |
| clusters.status | Body | String | クラスターの作業状態 |
| clusters.status_reason | Body | String | クラスターの作業状態の理由(null可能) |
| clusters.health_status | Body | String | クラスターのk8s API及びk8sノード状態情報の有効性。 <br>* `FRESH`: k8s API及びk8sノード状態情報が有効である <br>* `STALE`:一定時間k8s API及びノード状態情報が更新されず、情報の有効性が低下している<br>* `ROTTEN`:長期間k8s API及びノード状態情報が更新されず、情報を信頼できない |
| clusters.health_status_reason | Body | Object | クラスターのk8s API及びワーカーノードグループごとのk8sノード状態の詳細情報を含むオブジェクト |
| clusters.health_status_reason.timestamp | Body | String | クラスターk8s API及びk8sノード状態情報の更新時刻(UTC) |
| clusters.health_status_reason.cluster.api_status | Body | String | k8s APIの状態情報の統計。 <br>* `NORMAL`: k8s API状態が正常 <br>* `STALED_DATA`:一定時間k8s API状態情報が更新されず情報の有効性が低下している <br>* `ROTTEN_DATA`:長期間k8s API状態情報が更新されず情報を信頼できない <br>* `K8S_API_NOT_WORKING`: k8s API状態が異常 |
| clusters.health_status_reason.api | Body | String | k8s APIの状態情報。 <br>* `OK`: k8s API状態が正常 <br>* `NOT_OK`: k8s API状態が異常 |
| clusters.health_status_reason.cluster.node_status | Body | String | 全てのワーカーノードグループのk8sノード状態情報の統計。 <br>* `NORMAL`:全てのk8sノードがReady状態 <br>* `STALED_DATA`:一定時間k8sノード状態情報が更新されず情報の有効性が低下している <br>* `ROTTEN_DATA`:長期間k8s API状態情報が更新されず情報を信頼できない <br>* `NOT_READY_NODE_EXIST`:クラスターにNot Ready状態のk8sノードが存在 <br>* `ALL_NODES_NOT_READY`:クラスターの全てのk8sノード状態がNot Ready |
| clusters.health_status_reason.nodegroup.node_status.{WORKER_NODEGROUP_NAME} | Body | String | 特定ワーカーノードグループのk8sノード状態情報の統計。 <br>* `NORMAL`:該当ワーカーノードグループの全てのk8sノードがReady状態 <br>* `STALED_DATA`:一定時間k8sノード状態情報が更新されず情報の有効性が低下している <br>* `ROTTEN_DATA`:長期間k8s API状態情報が更新されず情報を信頼できない <br>* `NOT_READY_NODE_EXIST`:該当ワーカーノードグループにNot Ready状態のk8sノードが存在 <br>* `ALL_NODES_NOT_READY`:該当ワーカーノードグループの全てのk8sノード状態がNot Ready |
| clusters.health_status_reason.nodegroup-stats.{WORKER_NODEGROUP_NAME} | Body | String | 特定ワーカーノードグループのk8sノード状態情報。 {Ready Nodeの数}:{Not Ready Nodeの数}を意味 |
| clusters.labels | Body | Object | クラスタのラベル |
| clusters.labels.kube_tag | Body |String | コントロールプレーンのKubernetesバージョン |
| clusters.labels.availability_zone | Body | String | 基本ワーカーノードグループ適用：アベイラビリティゾーン |
| clusters.labels.node_image | Body | UUID | 基本ワーカーノードグループ適用：ベースイメージuuid |
| clusters.labels.external_network_id | Body | String | インターネットゲートウェイに接続されたVPC network UUID |
| clusters.labels.external_subnet_id_list | Body | String | インターネットゲートウェイに接続されたサブネットUUIDリスト(コロンで区切る) |
| clusters.labels.cert_manager_api | Body | String | CSR(Certificate Signing Request)機能を有効にするかどうか。必ず"True"に設定 |
| clusters.labels.master_lb_floating_ip_enabled | Body | String | Kubernetes APIエンドポイントに公認ドメインアドレスを作成するかどうか("True" / "False") |
| clusters.labels.strict_sg_rules | Body | String | ワーカーノードセキュリティグループに必須セキュリティルールのみ作成("True" / "False"), (2024.02.27. 以降に作成されたクラスタで確認可能) |
| clusters.labels.additional_network_id_list | Body | String | 基本ワーカーノードグループ適用：追加ネットワークのVPCネットワークUUIDリスト(コロン区切り) |
| clusters.labels.additional_subnet_id_list | Body | String | 基本ワーカーノードグループ適用：追加ネットワークのVPCサブネットUUIDリスト(コロン区切り) |
| clusters.labels.cni_driver | Body | String | クラスタCNI(2023.03.31. 以降に作成されたクラスタで確認可能) |
| clusters.labels.service_cluster_ip_range | Body | String | K8sサービスネットワーク、クラスタでサービス作成時、ClusterIPに割り当てられるIP帯域(2023.05.30. 以降に作成されたクラスタで確認可能) |
| clusters.labels.pods_network_cidr | Body | String | クラスタPodネットワーク(2023.05.30. 以降に作成されたクラスタで確認可能) |
| clusters.labels.pods_network_subnet | Body | String | クラスタPodサブネットサイズ(2023.05.30. 以降に作成されたクラスタで確認可能) |
| clusters.labels.ncr_sgw | Body | String | NCRタイプのサービスゲートウェイUUID |
| clusters.labels.obs_sgw | Body | String | OBSタイプのサービスゲートウェイUUID |
| clusters.labels.term_of_validity | Body | String | 証明書の有効期間 |
| clusters.labels.certificate_expiry | Body | String | 証明書の有効期限 | 


<details><summary>例</summary>
<p>

```json
{
    "clusters": [
        {
            "cluster_template_id": "b4503d97-6012-499d-a31a-5200f94a7890",
            "create_timeout": 60,
            "docker_volume_size": null,
            "flavor_id": "6ef27f21-c774-4c0e-84ff-7dd4a762571f",
            "health_status": "HEALTHY",
            "keypair": "testkeypair",
            "labels": {
                "availability_zone": "kr2-pub-b",
                "cert_manager_api": "True",
                "clusterautoscale": "nodegroupfeature",
                "etcd_volume_size": "10",
                "external_network_id": "751b8227-7b45-440a-9349-dbf829d0aba5",
                "external_subnet_id_list": "59ddc195-76b1-431d-9693-f09880747dc6",
                "flavor_type": "core",
                "hypervisor_type": "qemu",
                "kube_tag": "v1.23.3",
                "login_username": "centos",
                "master_lb_floating_ip_enabled": "true",
                "strict_sg_rules": "True",
                "node_image": "f462a2a5-ba24-46d6-b7a1-9a9febcd3cfc",
                "os_arch": "amd64",
                "os_distro": "CentOS",
                "os_type": "linux",
                "os_version": "7.8",
                "project_domain": "NORMAL",
                "server_group_meta": "k8s_2b778d83-8b67-45b1-920e-b0c5ad5c2f30_561c3f55-a23f-4e1a-b2fa-a5459b2c0575",
                "service_cluster_ip_range": "10.254.0.0/16",
                "pods_network_cidr" : "10.100.0.0/16",
                "pods_network_subnet" : "24"
            },
            "links": [
                {
                    "href": "https://kr2-api-kubernetes-infrastructure.nhncloudservice/clusters/f0af4484-0a16-433a-a15c-295d9ba6537d",
                    "rel": "self"
                },
                {
                    "href": "http://10.162.148.141:9511/clusters/2b778d83-8b67-45b1-920e-b0c5ad5c2f30",
                    "rel": "bookmark"
                }
            ],
            "name": "k8s-test",
            "node_count": 1,
            "stack_id": "7f497472-9729-4b89-9124-1c097335b856",
            "status": "CREATE_COMPLETE",
            "uuid": "2b778d83-8b67-45b1-920e-b0c5ad5c2f30"
        }
    ]
}
```

</p>
</details>

---

### クラスタ表示

個別クラスタ情報を照会します。

```
GET /v1/clusters/{CLUSTER_ID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| CLUSTER_ID_OR_NAME| URL | UUID or String | O | クラスタUUIDまたはクラスタ名 |

#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| uuid | Body | UUID | クラスタUUID |
| name | Body | String | クラスタ名 |
| flavor_id | Body | UUID | 基本ワーカーノードのインスタンスタイプUUID|
| keypair | Body | UUID | 基本ワーカーノードグループに適用されたキーペアUUID |
| node_count | Body | Integer| ワーカーノードの総数 |
| stack_id | Body | UUID | コントロールプレーンと接続されたheat stack UUID |
| status | Body | String | クラスターの作業状態 |
| status_reason | Body | String | クラスターの作業状態の理由(null可能) |
| health_status | Body | String | クラスターのk8s API及びk8sノード状態情報の有効性。 <br> * `FRESH`: k8s API及びk8sノード状態情報が有効である <br>* `STALE`:一定時間k8s API及びノード状態情報が更新されず情報の有効性が低下している <br>* `ROTTEN`:長期間k8s API及びノード状態情報が更新されず情報を信頼できない |
| health_status_reason | Body | Object | クラスターのk8s API及びワーカーノードグループごとのk8sノード状態の詳細情報を含むオブジェクト |
| health_status_reason.timestamp | Body | String | クラスターk8s API及びk8sノード状態情報の更新時刻(UTC) |
| health_status_reason.cluster.api_status | Body | String | k8s APIの状態情報の統計。 <br>* `NORMAL`: k8s API状態が正常 <br>* `STALED_DATA`:一定時間k8s API状態情報が更新されず情報の有効性が低下している <br>* `ROTTEN_DATA`:長期間k8s API状態情報が更新されず情報を信頼できない <br>* `K8S_API_NOT_WORKING`: k8s API状態が異常 |
| health_status_reason.api | Body | String | k8s APIの状態情報。 <br>* `OK`: k8s API状態が正常 <br>* `NOT_OK`: k8s API状態が異常 |
| health_status_reason.cluster.node_status | Body | String | 全てのワーカーノードグループのk8sノード状態情報の統計。 <br>* `NORMAL`:全てのk8sノードがReady状態 <br>* `STALED_DATA`:一定時間k8sノード状態情報が更新されず情報の有効性が低下している <br>* `ROTTEN_DATA`:長期間k8s API状態情報が更新されず情報を信頼できない <br>* `NOT_READY_NODE_EXIST`:クラスターにNot Ready状態のk8sノードが存在 <br>* `ALL_NODES_NOT_READY`:クラスターの全てのk8sノード状態がNot Ready  |
| health_status_reason.nodegroup.node_status.{WORKER_NODEGROUP_NAME} | Body | String | 特定ワーカーノードグループのk8sノード状態情報の統計。 <br>* `NORMAL`:該当ワーカーノードグループの全てのk8sノードがReady状態 <br>* `STALED_DATA`:一定時間k8sノード状態情報が更新されず情報の有効性が低下している <br>* `ROTTEN_DATA`:長期間k8s API状態情報が更新されず情報を信頼できない <br>* `NOT_READY_NODE_EXIST`:該当ワーカーノードグループにNot Ready状態のk8sノードが存在 <br>* `ALL_NODES_NOT_READY`:該当ワーカーノードグループの全てのk8sノード状態がNot Ready |
| health_status_reason.nodegroup-stats.{WORKER_NODEGROUP_NAME} | Body | String | 特定ワーカーノードグループのk8sノード状態情報。 {Ready Nodeの数}:{Not Ready Nodeの数}を意味 |
| api_address | Body | String | Kubernetes APIエンドポイント |
| project_id | Body | String | プロジェクト(テナント) ID |
| fixed_network | Body | UUID | VPC UUID|
| fixed_subnet | Body | UUID | VPC Subnet UUID |
| node_addresses | Body | String List | ワーカーノードIPアドレスリスト |
| created_at | Body | String | 作成時間(UTC) |
| updated_at | Body | String | 最終更新日(UTC) |
| labels | Body | Object | クラスタラベル |
| labels.kube_tag | Body |String | コントロールプレーンKubernetesバージョン |
| labels.availability_zone | Body | String | 基本ワーカーノードグループ適用：アベイラビリティゾーン |
| labels.node_image | Body | UUID | 基本ワーカーノードグループ適用：ベースイメージUUID |
| labels.external_network_id | Body | String | インターネットゲートウェイに接続されたVPC network UUID |
| labels.external_subnet_id_list | Body | String | インターネットゲートウェイに接続されたサブネットUUIDリスト(コロンで区切る) |
| labels.cert_manager_api | Body | String | CSR(Certificate Signing Request)機能を有効にするかどうか。必ず"True"に設定 |
| labels.master_lb_floating_ip_enabled | Body | String | Kubernetes APIエンドポイントに公認ドメインアドレスを作成するかどうか("True" / "False") |
| labels.strict_sg_rules | Body | String | ワーカーノードセキュリティグループに必須セキュリティルールのみ作成("True" / "False"), (2024.02.27. 以降に作成されたクラスタで確認可能) |
| labels.additional_network_id_list | Body | String | 基本ワーカーノードグループ適用：追加ネットワークのVPCネットワークUUIDリスト(コロンで区切る) |
| labels.additional_subnet_id_list | Body | String | 基本ワーカーノードグループ適用:追加ネットワークのVPCサブネットUUIDリスト(コロンで区切る) |
| labels.cni_driver | Body | String | クラスタCNI(2023.03.31. 以降に作成されたクラスタで確認可能) |
| labels.service_cluster_ip_range | Body | String | K8sサービスネットワーク、クラスタでサービス作成時にClusterIPに割り当てられるIP帯域(2023.05.30. 以降に作成されたクラスタで確認可能) |
| labels.pods_network_cidr | Body | String | クラスタPodネットワーク(2023.05.30. 以降に作成されたクラスタで確認可能) |
| labels.pods_network_subnet | Body | String | クラスタPodサブネットサイズ(2023.05.30. 以降に作成されたクラスタで確認可能) |
| labels.ncr_sgw | Body | String | NCRタイプのサービスゲートウェイUUID |
| labels.obs_sgw | Body | String | OBSタイプのサービスゲートウェイUUID |
| labels.term_of_validity | Body | String | 証明書の有効期間 |
| labels.certificate_expiry | Body | String | 証明書の有効期限 | 
| labels.platform_version | Body | String | プラットフォームバージョン |

<details><summary>例</summary>
<p>

```json
{
    "api_address": "https://2b778d83-alp-kr2-k8s.container.cloud.toast.com:6443",
    "cluster_template_id": "b4503d97-6012-499d-a31a-5200f94a7890",
    "coe_version": "v1.23.3",
    "container_version": "1.12.6",
    "create_timeout": 60,
    "created_at": "2021-08-05T01:48:39+00:00",
    "docker_volume_size": null,
    "fixed_network": "eb212079-b6ec-430c-ba57-14280a457bcb",
    "fixed_subnet": "4fdf5b80-3d35-43f5-a5c1-010a3b6c8e90",
    "flavor_id": "6ef27f21-c774-4c0e-84ff-7dd4a762571f",
    "floating_ip_enabled": false,
    "health_status": "HEALTHY",
    "health_status_reason": {
        {"test-k8s-default-w-bnga636xulqk-node-0.Ready": "True", "api": "ok"}
    },
    "keypair": "test-keypair",
    "labels": {
        "availability_zone": "kr2-pub-b",
        "cert_manager_api": "True",
        "clusterautoscale": "nodegroupfeature",
        "etcd_volume_size": "10",
        "external_network_id": "751b8227-7b45-440a-9349-dbf829d0aba5",
        "external_subnet_id_list": "59ddc195-76b1-431d-9693-f09880747dc6",
        "flavor_type": "core",
        "hypervisor_type": "qemu",
        "kube_tag": "v1.23.3",
        "kube_version_status": "NEED_UPGRADE",
        "platform_version": "1.202511.0",        
        "login_username": "centos",
        "master_lb_floating_ip_enabled": "true",
        "strict_sg_rules": "True",
        "node_image": "f462a2a5-ba24-46d6-b7a1-9a9febcd3cfc",
        "os_arch": "amd64",
        "os_distro": "CentOS",
        "os_type": "linux",
        "os_version": "7.8",
        "project_domain": "NORMAL",
        "server_group_meta": "k8s_2b778d83-8b67-45b1-920e-b0c5ad5c2f30_561c3f55-a23f-4e1a-b2fa-a5459b2c0575",
        "service_cluster_ip_range": "10.254.0.0/16",
        "pods_network_cidr" : "10.100.0.0/16",
        "pods_network_subnet" : "24"
    },
    "links": [
        {
            "href": "https://kr2-api-kubernetes-infrastructure.nhncloudservice/v1/clusters/2b778d83-8b67-45b1-920e-b0c5ad5c2f30",
            "rel": "self"
        },
        {
            "href": "https://kr2-api-kubernetes-infrastructure.nhncloudservice/clusters/2b778d83-8b67-45b1-920e-b0c5ad5c2f30",
            "rel": "bookmark"
        }
    ],
    "name": "test-k8s",
    "node_addresses": [
        "192.168.0.5"
    ],
    "node_count": 1,
    "project_id": "1ffeaca9bbf94ab1aa9cffdec29a258a",
    "stack_id": "7f497472-9729-4b89-9124-1c097335b856",
    "status": "CREATE_COMPLETE",
    "status_reason": null,
    "updated_at": "2021-08-05T04:39:49+00:00",
    "user_id": "12ba32bebc414c4992a2c9be3952a64c",
    "uuid": "2b778d83-8b67-45b1-920e-b0c5ad5c2f30"
}
```

</p>
</details>

---

### 作業履歴リストの表示

クラスタの作業履歴リストを照会します。

```
GET /v1/clusters/{CLUSTER_UUID}/events
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| CLUSTER_UUID | URL | UUID | O | クラスタUUID |


#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| events | Body | Array | 作業履歴オブジェクトリスト |
| events.id | Body | Integer | 作業ID |
| events.uuid | Body | UUID | 作業UUID |
| events.project_id | Body | String | プロジェクト(テナント) ID |
| events.cluster_uuid | Body | String | クラスタUUID |
| events.cluster_name | Body | String | クラスタ名 |
| events.resource_uuid | Body | String | 作業対象UUID |
| events.resource_name | Body | String | 作業対象名 |
| events.resource_type | Body | String | 作業対象の種類("cluster" / "nodegroup") |
| events.type | Body | String | 作業の種類 |
| events.state | Body | String | 作業状態("SUCCESS" / "FAIL" / "IN_PROGRESS") |
| events.contents | Body | String | 作業進行内容(成功時null) |
| events.details | Body | String | 作業リクエスト情報 | 
| events.created_at | Body | String | 作業開始時間(UTC) |
| events.updated_at | Body | String | 作業完了時間(UTC) |


<details><summary>例</summary>
<p>

```json
{
    "events": [
        {
            "id": 3683,
            "uuid": "868f218a-9446-4500-b6b3-8c1a95e3d7c3",
            "project_id": "5d8cc67593754d5581f7e8986badf358",
            "cluster_uuid": "388794e6-14dc-48ee-9a4f-9c40df5d97ec",
            "cluster_name": "nks-cluster",
            "resource_uuid": "388794e6-14dc-48ee-9a4f-9c40df5d97ec",
            "resource_name": "nks-cluster",
            "resource_type": "cluster",
            "type": "CLUSTER_CREATE",
            "state": "SUCCESS",
            "contents": null,
            "created_at": "2024-01-30T01:31:06+00:00",
            "updated_at": "2024-01-30T01:42:39+00:00"
        }
    ]
}
```

</p>
</details>

---

### 作業履歴の表示

クラスタの作業履歴を照会します。

```
GET /v1/clusters/{CLUSTER_UUID}/events/{EVENT_UUID}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| CLUSTER_UUID | URL | UUID | O | クラスタUUID |
| EVENT_UUID | URL | UUID | O | 作業UUID |


#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| id | Body | Integer | 作業ID |
| uuid | Body | UUID | 作業UUID |
| project_id | Body | String | プロジェクト(テナント) ID |
| cluster_uuid | Body | UUID | クラスタUUID |
| cluster_name | Body | String | クラスタ名 |
| resource_uuid | Body | UUID | 作業対象UUID |
| resource_name | Body | String | 作業対象名 |
| resource_type | Body | String | 作業対象の種類("cluster" / "nodegroup") |
| type | Body | String | 作業の種類 |
| state | Body | String | 作業状態("SUCCESS" / "FAIL" / "IN_PROGRESS") |
| contents | Body | String | 作業進行内容(成功時null) |
| events.details | Body | String | 作業リクエスト情報 | 
| created_at | Body | String | 作業開始時間(UTC) |
| updated_at | Body | String | 作業完了時間(UTC) |


<details><summary>例</summary>
<p>

```json
{
    "id": 3683,
    "uuid": "868f218a-9446-4500-b6b3-8c1a95e3d7c3",
    "project_id": "5d8cc67593754d5581f7e8986badf358",
    "cluster_uuid": "388794e6-14dc-48ee-9a4f-9c40df5d97ec",
    "cluster_name": "nks-cluster",
    "resource_uuid": "388794e6-14dc-48ee-9a4f-9c40df5d97ec",
    "resource_name": "nks-cluster",
    "resource_type": "cluster",
    "type": "CLUSTER_CREATE",
    "state": "SUCCESS",
    "contents": null,
    "created_at": "2024-01-30T01:31:06+00:00",
    "updated_at": "2024-01-30T01:42:39+00:00"
}
```

</p>
</details>

---

### クラスタ作成

クラスタを作成します。

```
POST /v1/clusters
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| keypair | Body | String | O | 基本ワーカーノードグループに適用するキーペアUUID |
| name | Body | String | O | クラスタ名 |
| cluster_template_id | Body | String | O | クラスタテンプレートID。必ず"iaas_console"に設定 |
| node_count | Body | String | O | 基本ワーカーノードグループに適用するノード数 |
| labels | Body | Object | O | クラスタ作成情報オブジェクト |
| labels.availability_zone | Body | String | O | 基本ワーカーノードグループ適用：アベイラビリティゾーン |
| labels.node_image | Body | UUID | O | 基本ワーカーノードグループ適用：ベースイメージUUID |
| labels.boot_volume_type | Body | String | O | 基本ワーカーノードグループ適用：ブロックストレージの種類|
| labels.boot_volume_size | Body | String | O | 基本ワーカーノードグループ適用：ブロックストレージサイズ(GB) |
| labels.boot_volume_key_id | Body | String | X | (暗号化されたブロックストレージを使用する場合)暗号化されたブロックストレージに適用する対称鍵ID |
| labels.boot_volume_appkey | Body | String | X | (暗号化されたブロックストレージを使用する場合)暗号化されたブロックストレージに適用する対称鍵のアプリケーションキー |
| labels.external_network_id | Body | String | X | インターネットゲートウェイに接続されたVPCネットワークUUID<br>VPCサブネットが接続されているルーターがインターネットゲートウェイに接続されている場合は必ず設定 |
| labels.external_subnet_id_list | Body | String | X | インターネットゲートウェイに接続されたサブネットUUIDリスト(コロンで区切る)<br>VPCサブネットが接続されているルーターがインターネットゲートウェイに接続されている場合は必ず設定 |
| labels.cert_manager_api | Body | String | O | CSR(Certificate Signing Request)機能を有効にするかどうか。必ず"True"に設定 |
| labels.ca_enable | Body | String | O | 基本ワーカーノードグループ適用:クラスターオートスケーラー:機能有効かどうか("True" / "False") |
| labels.ca_pod_replicas | Body | String | X | 基本ワーカーノードグループ適用:クラスターオートスケーラー: Pod数 |
| labels.ca_max_node_count | Body | String | X | 基本ワーカーノードグループ適用:クラスターオートスケーラー:最大ノード数 |
| labels.ca_min_node_count | Body | String | X | 基本ワーカーノードグループ適用:クラスターオートスケーラー:最小ノード数 |
| labels.ca_scale_down_enable | Body | String | X | 基本ワーカーノードグループ適用:クラスターオートスケーラー:削減が有効かどうか("True" / "False") |
| labels.ca_scale_down_unneeded_time | Body | String | X | 基本ワーカーノードグループ適用:クラスターオートスケーラー:しきい値領域維持時間 |
| labels.ca_scale_down_util_thresh | Body | String | X | 基本ワーカーノードグループ適用:クラスターオートスケーラー:リソース使用量しきい値 |
| labels.ca_scale_down_delay_after_add | Body | String | X | 基本ワーカーノードグループ適用:クラスターオートスケーラー:増設後の削減遅延時間 |
| labels.mba_scale_out | Body | String | X | 基本ワーカーノードグループ適用:指標ベースのオートスケーラー増設ポリシー設定 |
| labels.mba_scale_in | Body | String | X | 基本ワーカーノードグループ適用:指標ベースのオートスケーラー削減ポリシー設定 |
| labels.kube_tag | Body | String | O | Kubernetesバージョン |
| labels.user_script | Body | String | X | ユーザースクリプト(old) |
| labels.user_script_v2 | Body | String | X | ユーザースクリプト |
| labels.master_lb_floating_ip_enabled | Body | String | O | Kubernetes APIエンドポイントに公認ドメインアドレスを作成するかどうか("True" / "False")<br>labels.external_network_idとexternal_subnet_id_listが設定されている場合にのみ"True"に設定可能 |
| labels.additional_network_id_list | Body | String | X | 基本ワーカーノードグループ適用：追加ネットワークのVPCネットワークUUIDリスト(コロン区切り) |
| labels.additional_subnet_id_list | Body | String | X | 基本ワーカーノードグループ適用：追加ネットワークのVPCサブネットUUIDリスト(コロン区切り) |
| labels.service_cluster_ip_range | Body | String  | X |  K8sサービスネットワーク、クラスタでサービス作作成時、ClusterIPに割り当てられるIP帯域。 fixed_subnet, pods_network_cidr, service_cluster_ip_range入力ルール参考 |
| labels.pods_network_cidr | Body | String |  X | クラスタPodネットワーク。 fixed_subnet, pods_network_cidr, service_cluster_ip_range入力ルール参考 |
| labels.pods_network_subnet | Body | Integer | X | クラスタPodサブネットサイズ。 pods_network_subnet入力ルール参考 |
| labels.ncr_sgw | Body | String | X | NCRタイプのサービスゲートウェイUUID<br>ただし、クラスタVPCと同じVPCに作成されたものに限る。 |
| labels.obs_sgw | Body | String | X | OBSタイプのサービスゲートウェイUUID<br>ただし、クラスタVPCと同じVPCに作成されたものに限る。 |
| labels.cni_driver | Body | String | X | CNI設定、選択可能CNIリスト: calico(基本), calico-ebpf<br>calico: Calico-VXLANで作成<br>calico-ebpf: Calico-eBPFで作成 |
| labels.extra_security_groups | Body | Array | X | 基本ワーカーノードグループ適用:追加セキュリティグループオブジェクトリスト |
| labels.extra_security_groups[].target_subnet | Body | String | X | 追加セキュリティグループ指定対象サブネットUUID |
| labels.extra_security_groups[].security_group_ids | Body | String | X | 追加セキュリティグループUUIDリスト(カンマ区切り) |
| labels.extra_volumes | Body | Array | X | 基本ワーカーノードグループ適用:追加ブロックストレージオブジェクトリスト |
| labels.extra_volumes[].volume_type | Body | String | X | 追加ブロックストレージの種類 |
| labels.extra_volumes[].volume_size | Body | Integer | X | 追加ブロックストレージサイズ(GB) |
| labels.extra_volumes[].volume_key_id | Body | String | X | (暗号化されたブロックストレージを使用する場合)暗号化されたブロックストレージに適用する対称鍵ID |
| labels.extra_volumes[].volume_appkey | Body | String | X | (暗号化されたブロックストレージを使用する場合)暗号化されたブロックストレージに適用する対称鍵のアプリキー |
| labels.extra_volumes[].volume_mount_path | Body | String | X | 追加ブロックストレージがマウントされるパス |
| labels.control_plane_log | Body | String | X | K8Sコントロールプレーンログ保存有効 |
| labels.fip_auto_bind_enable | Body | String | X | フローティングIP自動割り当て:機能有効かどうか(`True` / `False`) |
| labels.fip_bind_subnet | Body | String | X | フローティングIP自動割り当て:フローティングIPが接続されるネットワークインターフェースのサブネット |
| labels.fip_selector | Body | String | X | フローティングIP自動割り当て:ノードに割り当てるフローティングIPを選別するための識別子 |
| labels.fip_auto_bind_enable | Body | String | X | 基本ワーカーノードグループ適用:フローティングIP自動割り当て:機能有効かどうか(`True` / `False`) |
| labels.fip_bind_subnet | Body | String | X | 基本ワーカーノードグループ適用:フローティングIP自動割り当て:フローティングIPが接続されるネットワークインターフェースのサブネット |
| labels.fip_selector | Body | String | X | 基本ワーカーノードグループ適用:フローティングIP自動割り当て:ノードに割り当てるフローティングIPを選別するための識別子 |
| labels.k8s_node_labels | Body | String | 基本ワーカーノードグループ適用: Kubernetesラベル設定 |
| flavor_id | Body | UUID | O | 基本ワーカーノードグループ適用：ノードインスタンスタイプUUID |
| fixed_network | Body | UUID | O | VPC Network UUID |
| fixed_subnet | Body | UUID | O | VPCサブネットUUID. fixed_subnet, pods_network_cidr, service_cluster_ip_range入力ルール参照 |
| addons | Body | List of Object | X | インストールするアドオン情報リスト |
| addons.name | Body | String | O | アドオン名 |
| addons.version | Body | String | O | アドオンのバージョン |
| addons.options | Body | Object | X | アドオンごとのオプション |
> pods_network_cidr, service_cluster_ip_rangeのCIDRは以下のようなルールで入力する必要があります。
>  - CIDRはリンクローカルアドレス帯域(169.254.0.0/16)と重複することはできません。
>  - PodネットワークとK8sサービスネットワーク帯域は重複することができません。
>  - CIDRはNKS内部で使用しているIP帯域(198.18.0.0.0/19)と重複することはできません。.
>  - CIDRはNKSクラスタに接続されたVPCネットワークサブネットまたは追加ネットワークサブネットの帯域と重複することはできません。
>  - /24より大きいCIDRブロックは入力できません(次のようなCIDRブロックは使用できません。/26, /30)。
>  - v1.23.3以下クラスタの場合ドッカーBIP(bridged IP range)と重複できません(172.17.0.0/16)。
> pods_network_subnetは下記のようなルールで入力する必要があります。
> - 20-28(含む)範囲の値のみ入力可能です。
>  - pods_network_subnet値がpods_network_cidr prefixの値より最低2大きい必要があります。正常例(Podサブネットサイズ: 24, Podネットワーク: 10.100.0.0/22)


> [注意]
> fixed_subnet, pods_network_cidr, service_cluster_ip_rangeのCIDRは下記のようなルールで入力する必要があります。
>  - リンクローカルアドレス帯域(169.254.0.0/16)と重複することはできません。
>  - fixed_subnet, pods_network_cidr, service_cluster_ip_range帯域は重複することはできません。
>  - NKS内部で使用しているIP帯域(198.18.0.0/19)と重複することはできません。

<details><summary>例</summary>
<p>

```json
{
    "cluster_template_id": "iaas_console",
    "create_timeout": 60,
    "fixed_network": "eb212079-b6ec-430c-ba57-14280a457bcb",
    "fixed_subnet": "4fdf5b80-3d35-43f5-a5c1-010a3b6c8e90",
    "flavor_id": "6ef27f21-c774-4c0e-84ff-7dd4a762571f",
    "keypair": "test-keypair",
    "labels": {
        "availability_zone": "kr2-pub-b",
        "boot_volume_size": "20",
        "boot_volume_type": "General HDD",
        "ca_enable": "false",
        "ca_pod_replicas": "1",        
        "ca_max_node_count": "10",
        "ca_min_node_count": "1",
        "ca_scale_down_delay_after_add": "3",
        "ca_scale_down_enable": "true",
        "ca_scale_down_unneeded_time": "3",
        "ca_scale_down_util_thresh": "50",
        "cert_manager_api": "True",
        "clusterautoscale": "nodegroupfeature",
        "external_network_id": "751b8227-7b45-440a-9349-dbf829d0aba5",
        "external_subnet_id_list": "59ddc195-76b1-431d-9693-f09880747dc6",
        "kube_tag": "v1.23.3",
        "master_lb_floating_ip_enabled": "true",
        "strict_sg_rules": "True",
        "node_image": "f462a2a5-ba24-46d6-b7a1-9a9febcd3cfc",
        "user_script_v2": "",
        "extra_security_groups": [
            {
                "target_subnet": "4fdf5b80-3d35-43f5-a5c1-010a3b6c8e90",
                "security_group_ids": "8669cca4-7904-4dc6-b1be-db49661cedb6,fa69d78d-bd04-4ab0-9ce6-c92a84b899c2"
            }
        ],
        "extra_volumes": [
            {
                "volume_type": "General HDD",
                "volume_size": 100
            }
        ]
    },
    "name": "test-k8s",
    "node_count": 1,
    "addons": [
        {"name": "calico", "version": "v3.28.2-nks1", "options": {"mode": "vxlan"}},
        {"name": "coredns", "version": "1.8.4-nks1"}
    ]
}
```

</p>
</details>

#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| uuid | Body | UUID | クラスタUUID |

<details><summary>例</summary>
<p>

```json
{
    "uuid": "5801ef8a-3760-4858-b467-fc4c1201241d"
}
```

</p>
</details>

---

### クラスタ削除

クラスタを削除します。

```
DELETE /v1/clusters/{CLUSTER_ID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | クラスタUUIDまたはクラスタ名 | 


#### レスポンス

このAPIはレスポンス本文を返しません。

---

### リサイズ

クラスタのノード数を調整します。

```
POST /v1/clusters/{CLUSTER_ID_OR_NAME}/actions/resize
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | クラスタUUIDまたはクラスタ名 | 
| nodegroup | Body | UUID | O | 対象ワーカーノードグループ名 / UUID |
| node_count | Body | Integer | O | 変更したいワーカーノード数 |
| nodes_to_remove | Body | String List | X | 削除したいノードUUID |

* 注意事項
  * ノードを削減する場合(、すなわち一部ノード削除)削減するノードを指定するには**nodes_to_remove**を設定する必要があります。削減するノードを指定しなかった場合、削減対象ノードはランダムに選択されます。
  * node_count最小1、最大10(ただし最大値はquotaで調整可能)

<details><summary>増設例</summary>
<p>

```json
{
    "node_count": 3,
    "nodegroup": "default-worker"
}
```

</p>
</details>

<details><summary>削減例</summary>
<p>

```json
{
    "node_count": 1,
    "nodegroup": "default-worker",
    "nodes_to_remove": [
        "593e4aee-697f-4808-aa5b-d3c8703795ff",
        "ce2e2d2a-ddf5-41da-a338-72e7f5237088"
    ]
}
```

</p>
</details>



#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| uuid | Body | String | 対象クラスタUUID|

<details><summary>例</summary>
<p>

```json
{
    "uuid": "5bac7acd-58b7-4cf5-95f5-a25d67da13a2"
}

```

</p>
</details>

---

### クラスタkubeconfig照会

クラスタ設定ファイル(kubeconfig)を照会します。

```
GET /v1/clusters/{CLUSTER_ID_OR_NAME}/config
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | クラスタUUIDまたはクラスタ名 | 


#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| config | Body | String | kubeconfigファイル本文 |


<details><summary>例</summary>
<p>

```json
{
    "config": "apiVersion: v1\nclusters:\n- cluster:\n    certificate-authority-data: LS0tLS1CRU... \n    server: https://96742ac4-kr2-k8s.container.cloud.toast.com:6443\n  name: \"toast-robot-e2e-1-18\"\ncontexts:\n- context:\n    cluster: \"toast-robot-e2e-1-18\"\n    user: admin\n  name: default\ncurrent-context: default\nkind: Config\npreferences: {}\nusers:\n- name: admin\n  user:\n    client-certificate-data: LS0tLS1CRU...\n    client-key-data: LS0tLS1CRU...\n"
}
```

</p>
</details>


### クラスタAPIエンドポイントIPアクセス制御適用
クラスタAPIエンドポイントにIPアクセス制御を適用または解除できます。
IPアクセス制御機能の詳細については、 [IPアクセス制御](/Network/Load%20Balancer/ja/overview/#ip)文書を参照してください。
クラスタAPIエンドポイントへのIPアクセス制御ルールに関する詳細については、 [ユーザーガイド](/Container/NKS/ja/user-guide/#api_endpoint_ipacl)を参照してください。

```
POST /v1/clusters/{CLUSTER_ID_OR_NAME}/api_ep_ipacl
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | クラスタUUIDまたはクラスタ名 |
| enable | Body | String | O | 'true'、'false'のいずれかに設定可能。デフォルト値: 'false'<br>true:クラスタAPIエンドポイントにIPアクセス制御を適用<br>false:クラスタAPIエンドポイントにIPアクセス制御解除。falseを設定した場合、サブ設定はすべて無視されます。 |
| action | Body | String | O(enable設定がtrueの場合) | IPアクセス制御タイプ、 ALLOW、DENYのいずれかに設定可能 |
| ipacl_targets | Body | List of Object | O(enable設定がtrueの場合) | IPアクセス制御対象オブジェクト |
| ipacl_targets.cidr_address | Body | String | O(enable設定がtrueの場合) | IPアクセス制御対象。 IPアドレスまたはCIDR形式のIPアドレス範囲入力可能 |
| ipacl_targets.descripion | Body | String | X | IPアクセス制御対象の説明 |


<details><summary>例</summary>
<p>

```json
{
    "enable": "True",
    "action": "ALLOW",
    "ipacl_targets": [
        {
            "cidr_address" : "192.168.0.5"
        },
        {
            "cidr_address" : "10.10.22.3/24",
            "description": "Your Friends"
        }
    ]   
}
```

</p>
</details>


#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| uuid | Body | UUID | クラスタUUID |

<details><summary>例</summary>
<p>

```json
{
    "uuid": "0641db9f-5e71-4df9-9571-089c7964d82e"
}
```

</p>
</details>


### クラスタAPIエンドポイントのIPアクセス制御照会
クラスタAPIエンドポイントに適用されたIPアクセス制御情報を確認できます。

```
GET /v1/clusters/{CLUSTER_ID_OR_NAME}/api_ep_ipacl
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト
このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | クラスタUUIDまたはクラスタ名 | 


#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| cluster_uuid | Body | UUID | クラスタUUID |
| enable | Body | String | true:クラスタAPIエンドポイントにIPアクセス制御が適用されている。false:クラスタAPIエンドポイントにIPアクセス制御が解除されている | 
| action | Body | String | IPアクセス制御タイプALLOW、DENY確認可能 |
| ipacl_targets | Body | List of Object | IPアクセス制御対象オブジェクト |
| ipacl_targets.cidr_address | Body | String | IPアクセス制御対象。 IPアドレスまたはCIDR形式のIPアドレス範囲を入力可能 |
| ipacl_targets.descripion | Body | String | IPアクセス制御対象の説明 |

<details><summary>enable: true の場合の例</summary>
<p>

```json
{
    "cluster_uuid" : "8be87215-9db7-45ed-a03c-38e6db939915",
    "enable": "true",
    "action": "ALLOW",
    "ipacl_targets": [
        {
            "cidr_address" : "192.168.0.5",
            "description": "My Friend"
        },
        {
            "cidr_address" : "10.10.22.3/24"
        }
    ]   
}
```

</p>
</details>

<details><summary>enable: false の場合の例</summary>
<p>

```json
{
    "cluster_uuid" : "8be87215-9db7-45ed-a03c-38e6db939915",
    "enable": "false"
}
```

</p>
</details>

---
### クラスタ証明書の更新

クラスタの証明書を更新します。
```
PATCH /v1/certificates/{CLUSTER_ID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト
| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | クラスタUUIDまたはクラスタ名 | 
| term_of_validity | Body | Integer | O | 証明書の有効期間。年単位で入力可能。最小値: 1、最大値: 5 |

<details><summary>例</summary>
<p>

```json
{
    "term_of_validity": 5
}
```

</p>
</details>

#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| uuid | Body | String | 対象クラスタUUID|

<details><summary>例</summary>
<p>

```json
{
    "uuid": "5f6af7da-df9b-4edd-8284-02317b11e061"
}

```

</p>
</details>

---

### サービスゲートウェイを変更する

クラスタ作成時、サービスゲートウェイを設定した場合、他のサービスゲートウェイに変更できます。
```
POST /v1/clusters/{CLUSTER_ID_OR_NAME}/actions/update_sgw
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | クラスタUUIDまたはクラスタ名 | 
| ncr_sgw | Body | UUID | O | 変更したいNCRタイプのサービスゲートウェイUUID |
| obs_sgw | Body | UUID | O | 変更したいOBSタイプのサービスゲートウェイUUID |

<details><summary>サービスゲートウェイ変更例</summary>
<p>

```json
{
    "ncr_sgw": "48f4ff38-d14b-4f34-a40c-705524e6a755",
    "obs_sgw": "23c550cb-6a5b-4eaa-903a-711c13316d91"
}
```

</p>
</details>

#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| uuid | Body | String | 対象クラスタUUID|

<details><summary>例</summary>
<p>

```json
{
    "uuid": "5bac7acd-58b7-4cf5-95f5-a25d67da13a2"
}

```

</p>
</details>

---

### コントロールプレーンKubernetesコンポーネントログ保存
NHN Kubernetes Service(NKS)のコントロールプレーンで実行中の主要KubernetesコンポーネントのログをLog & Crash SearchまたはObject Storageに保存します。

```
PATCH /v1/clusters/{CLUSTER_ID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | クラスターUUIDまたはクラスター名 | 
| type | Body | String | O | `control_plane_log`に設定 |
| control_plane_log | Body | Object | O | control_plane_logオブジェクト |
| control_plane_log.enable | Body | bool | O | K8Sコントロールプレーンログ保存有効 |
| control_plane_log.type | Body | String | enable : trueの場合必須 | lncs : Log & Crash Searchにcontrol planeログ送信obs : Object storageにcontrol planeログ送信 |
| control_plane_log.sgw | Body | UUID | enable : trueの場合必須 | control_plane_log.typeによって区分される<br>lncs : Log and Crash Search Service Gateway UUID<br>obs : Object storage Search Service Gateway UUID |
| control_plane_log.upload_interval | Body | Integer | x | OBSにlog送信周期設定(分)<br>min : 1<br>max : 60<br>default : 10 |
| control_plane_log.lncs_appkey | Body | String | enable : true<br>control_plane_log.type = lncsの場合必須 | NKSと同じプロジェクト(テナント)のLog & Crash Search Appkey情報 |
| control_plane_log.obs_api_url | Body | String | enable : true<br>control_plane_log.type = obsの場合必須 | ユーザーのobsコンテナfull path<br>(OBSのストレージアドレス + OBSのコンテナ名 + 希望する保存パス) |
| control_plane_log.obs_store_as | Body | String | X | OBSログファイル提供方式(gzip, text) |


<details><summary>Log & Crash Searchログ送信有効化</summary>
<p>

```json
{
    "type": "control_plane_log",
    "control_plane_log" : {
        "enable": true,
        "type": "lncs",
        "sgw": "b6f68830-e688-4d89-ac0a-2f1a5594177a",
        "upload_interval" : 10,
        "lncs_appkey" : "3e4jP4LlMGXitafx",
    }
}
```

</p>
</details>


<details><summary>Object Storageログ送信有効化</summary>
<p>

```json
{
    "type": "control_plane_log",
    "control_plane_log" : {
        "enable": true,
        "type": "obs",
        "sgw": "b6f68830-e688-4d89-ac03-2f1155a4177a",
        "upload_interval" : 60,
        "obs_api_url" :"https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_d5b58ab0bb9340909bd7ff5a24f44313/iksoon-obs-container/testpath",
        "obs_store_as" : "gzip"
    }
}
```

</p>
</details>


<details><summary>ログ送信無効化</summary>
<p>

```json
{
    "type": "control_plane_log",
    "control_plane_log" : {
        "enable": false,
    }
}
```

</p>
</details>

#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| uuid | Body | UUID | クラスターUUID |

<details><summary>例</summary>
<p>

```json
{
    "uuid": "018b06c5-1293-4081-8242-167a1cb9f262"
}
```

</p>
</details>


## ノードグループ

### ノードグループリスト表示

ノードグループリストを照会します。

```
GET /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | クラスタUUIDまたはクラスタ名 | 


#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| nodegroups | Body | Array | ノードグループ情報オブジェクトリスト |
| nodegroups.uuid | Body | UUID | ノードグループUUID |
| nodegroups.flavor_id | Body | UUID | ノードグループインスタンスタイプUUID |
| nodegroups.image_id | Body | UUID | ノードグループベースイメージUUID |
| nodegroups.max_node_count | Body | Integer | ノードグループ最大ノード数 |
| nodegroups.min_node_count | Body | Integer | ノードグループ最小ノード数 |
| nodegroups.name | Body | String | ノードグループ名 |
| nodegroups.node_count | Body | Integer | ノードグループのノード数 |
| nodegroups.role | Body | String | ノードグループの役割 |
| nodegroups.stack_id | Body | UUID | ノードグループに接続されたheat stack UUID |
| nodegroups.status | Body | String | ノードグループの状態 |

<details><summary>例</summary>
<p>

```json
{
    "nodegroups": [
        {
            "flavor_id": "069bdcff-e9b6-42c8-83ce-4c743ea30394",
            "image_id": "96aff4ab-d221-4688-8364-2fcf02d50547",
            "is_default": false,
            "max_node_count": 10,
            "min_node_count": 1,
            "name": "default-worker",
            "node_count": 2,
            "role": "worker",
            "stack_id": "f04c157a-78e3-4bfc-a83e-fbe7c01ab616",
            "status": "UPDATE_COMPLETE",
            "uuid": "018b06c5-1293-4081-8242-167a1cb9f262"
        }
    ]
}
```

</p>
</details>

---

### ノードグループ表示

個別ノードグループ情報を照会します。

```
GET /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | クラスタUUIDまたはクラスタ名 | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | ノードグループUUIDまたはノードグループ名 | 

#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| uuid | Body | UUID | ノードグループUUID |
| name | Body | String | ノードグループ名 |
| cluster_id | Body  | UUID | ノードグループが属すクラスタUUID |
| flavor_id | Body | UUID | ノードで使用するインスタンスタイプUUID |
| image_id | Body | UUID | ノードで使用するベースイメージUUID |
| labels | Body | Object | ノードグループ作成情報オブジェクト |
| labels.availability_zone | Body | String | ワーカーノードグループ適用：アベイラビリティゾーン |
| labels.node_image | Body | UUID | ワーカーノードグループ適用：ベースイメージUUID |
| labels.boot_volume_type | Body | String | ワーカーノードグループ適用：ブロックストレージの種類|
| labels.boot_volume_size | Body | String | ワーカーノードグループ適用：ブロックストレージサイズ(GB) |
| labels.boot_volume_key_id | Body | String | (暗号化されたブロックストレージを使用する場合)ブロックストレージに適用した対称鍵ID |
| labels.boot_volume_appkey | Body | String | (暗号化されたブロックストレージを使用する場合)ブロックストレージに適用した対称鍵のアプリケーションキー |
| labels.external_network_id | Body | String | インターネットゲートウェイに接続されたVPC network UUID |
| labels.external_subnet_id_list | Body | String | インターネットゲートウェイに接続されたサブネットUUIDリスト(コロンで区切る) |
| labels.cert_manager_api | Body | String | CSR(Certificate Signing Request)機能を有効にするかどうか。必ず"True"に設定 |
| labels.ca_enable | Body | String | ワーカーノードグループ適用:クラスターオートスケーラー:機能有効かどうか("True" / "False") |
| labels.ca_pod_replicas | Body | String | ワーカーノードグループ適用:クラスターオートスケーラー: Pod数 |
| labels.ca_max_node_count | Body | String | ワーカーノードグループ適用:クラスターオートスケーラー:最大ノード数 |
| labels.ca_min_node_count | Body | String | ワーカーノードグループ適用:クラスターオートスケーラー:最小ノード数 |
| labels.ca_scale_down_enable | Body | String | ワーカーノードグループ適用:クラスターオートスケーラー:削減が有効かどうか("True" / "False") |
| labels.ca_scale_down_unneeded_time | Body | String | ワーカーノードグループ適用:クラスターオートスケーラー:しきい値領域維持時間 |
| labels.ca_scale_down_util_thresh | Body | String | ワーカーノードグループ適用:クラスターオートスケーラー:リソース使用量しきい値 |
| labels.ca_scale_down_delay_after_add | Body | String | ワーカーノードグループ適用:クラスターオートスケーラー:増設後の削減遅延時間 |
| labels.mba_scale_out | Body | String | ワーカーノードグループ適用:指標ベースのオートスケーラー増設ポリシー設定 |
| labels.mba_scale_in | Body | String | ワーカーノードグループ適用:指標ベースのオートスケーラー削減ポリシー設定 |
| labels.kube_tag | Body | String | ワーカーノードグループKubernetesバージョン |
| labels.user_script | Body | String | ユーザースクリプト(old) |
| labels.user_script_v2 | Body | String | ユーザースクリプト |
| labels.additional_network_id_list | Body | String | ワーカーノードグループ適用：追加ネットワークのVPCネットワークUUIDリスト(コロン区切り) |
| labels.additional_subnet_id_list | Body | String | ワーカーノードグループ適用：追加ネットワークのVPCサブネットUUIDリスト(コロン区切り) |
| labels.strict_sg_rules | Body | String | ワーカーノードセキュリティグループに必須セキュリティルールのみ作成("True" / "False"), (2024.02.27. 以降に作成されたクラスタで確認可能) |
| labels.platform_version | Body | String | プラットフォームバージョン |
| max_node_count | Body | Integer | 最大ノード数 |
| min_node_count | Body | Integer | 最小ノード数 |
| node_addresses | Body | String list | ノードIPアドレスリスト |
| node_count | Body | Integer | ノード数 |
| project_id | Body | String | プロジェクト(テナント) ID |
| role | Body | String | ノードグループの役割 |
| stack_id | Body | UUID | ノードグループに接続されたheat stack UUID |
| status | Body | String | ノードグループの状態 |
| status_reason | Body | String | ノードグループの状態理由(null可能) |
| created_at | Body | String | 作成時間(UTC) |
| updated_at | Body | String | 最終更新日(UTC) |

<details><summary>例</summary>
<p>

```json
{
    "cluster_id": "96742ac4-02e7-4b1d-a242-02876c0bd3f8",
    "created_at": "2021-10-23T10:06:19+00:00",
    "docker_volume_size": null,
    "flavor_id": "069bdcff-e9b6-42c8-83ce-4c743ea30394",
    "id": 2697,
    "image_id": "96aff4ab-d221-4688-8364-2fcf02d50547",
    "is_default": false,
    "labels": {
        "availability_zone": "",
        "boot_volume_size": "20",
        "boot_volume_type": "General HDD",
        "ca_enable": "True",
        "ca_pod_replicas": "1",        
        "ca_max_node_count": "10",
        "ca_min_node_count": "2",
        "ca_scale_down_delay_after_add": "10",
        "ca_scale_down_enable": "True",
        "ca_scale_down_unneeded_time": "10",
        "ca_scale_down_util_thresh": "50",
        "cert_manager_api": "true",
        "clusterautoscale": "nodegroupfeature",
        "external_network_id": "751b8227-7b45-440a-9349-dbf829d0aba5",
        "external_subnet_id_list": "59ddc195-76b1-431d-9693-f09880747dc6",
        "flavor_type": "memory",
        "hypervisor_type": "qemu",
        "kube_tag": "v1.19.13",
        "kube_version_status": "LATEST",
        "platform_version": "1.202511.0",        
        "login_username": "centos",
        "master_lb_floating_ip_enabled": "true",
        "strict_sg_rules": "True",
        "node_image": "96aff4ab-d221-4688-8364-2fcf02d50547",
        "os_arch": "amd64",
        "os_distro": "CentOS",
        "os_type": "linux",
        "os_version": "7.8",
        "project_domain": "NORMAL",
        "server_group_meta": "k8s_96742ac4-02e7-4b1d-a242-02876c0bd3f8_018b06c5-1293-4081-8242-167a1cb9f262"
    },
    "links": [
        {
            "href": "https://kr2-api-kubernetes-infrastructure.nhncloudservice/v1/clusters/96742ac4-02e7-4b1d-a242-02876c0bd3f8/nodegroups/018b06c5-1293-4081-8242-167a1cb9f262",
            "rel": "self"
        },
        {
            "href": "https://kr2-api-kubernetes-infrastructure.nhncloudservice/clusters/96742ac4-02e7-4b1d-a242-02876c0bd3f8/nodegroups/018b06c5-1293-4081-8242-167a1cb9f262",
            "rel": "bookmark"
        }
    ],
    "max_node_count": 10,
    "min_node_count": 2,
    "name": "default-worker",
    "node_addresses": [
        "192.168.0.40",
        "192.168.0.19"
    ],
    "node_count": 2,
    "project_id": "1ffeaca9bbf94ab1aa9cffdec29a258a",
    "role": "worker",
    "stack_id": "f04c157a-78e3-4bfc-a83e-fbe7c01ab616",
    "status": "UPDATE_COMPLETE",
    "status_reason": "Stack UPDATE completed successfully",
    "updated_at": "2021-10-28T02:13:15+00:00",
    "uuid": "018b06c5-1293-4081-8242-167a1cb9f262",
    "version": null
}
```

</p>
</details>

---

### ノードグループ作成

ノードグループを作成します。

```
POST /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | クラスタUUIDまたはクラスタ名 | 
| flavor_id | Body | UUID | O | ノードで使用するインスタンスタイプUUID |
| image_id | Body | UUID | O | ノードで使用するベースイメージUUID |
| labels | Body | Object | O | ノードグループ作成情報オブジェクト |
| labels.availability_zone | Body | String | O | 基本ワーカーノードグループ適用：アベイラビリティゾーン |
| labels.boot_volume_type | Body | String | O | 基本ワーカーノードグループ適用：ブロックストレージの種類|
| labels.boot_volume_size | Body | String | O | 基本ワーカーノードグループ適用：ブロックストレージサイズ(GB) |
| labels.boot_volume_key_id | Body | String | X | (暗号化されたブロックストレージを使用する場合)ブロックストレージに適用する対称鍵ID |
| labels.boot_volume_appkey | Body | String | X | (暗号化されたブロックストレージを使用する場合)ブロックストレージに適用する対称鍵のアプリケーションキー |
| labels.ca_enable | Body | String | O | 基本ワーカーノードグループ適用:クラスターオートスケーラー:機能有効かどうか("True" / "False") |
| labels.ca_pod_replicas | Body | String | X | 基本ワーカーノードグループ適用:クラスターオートスケーラー: Pod数 |
| labels.ca_max_node_count | Body | String | X | 基本ワーカーノードグループ適用:クラスターオートスケーラー:最大ノード数 |
| labels.ca_min_node_count | Body | String | X | 基本ワーカーノードグループ適用:クラスターオートスケーラー:最小ノード数 |
| labels.ca_scale_down_enable | Body | String | X | 基本ワーカーノードグループ適用:クラスターオートスケーラー:削減が有効かどうか("True" / "False") |
| labels.ca_scale_down_unneeded_time | Body | String | X | 基本ワーカーノードグループ適用:クラスターオートスケーラー:しきい値領域維持時間 |
| labels.ca_scale_down_util_thresh | Body | String | X | 基本ワーカーノードグループ適用:クラスターオートスケーラー:リソース使用量しきい値 |
| labels.ca_scale_down_delay_after_add | Body | String | X | 基本ワーカーノードグループ適用:クラスターオートスケーラー:増設後の削減遅延時間 |
| labels.mba_scale_out | Body | String | X | 基本ワーカーノードグループ適用:指標ベースのオートスケーラー増設ポリシー設定 |
| labels.mba_scale_in | Body | String | X | 基本ワーカーノードグループ適用:指標ベースのオートスケーラー削減ポリシー設定 |
| labels.user_script | Body | String | X | ユーザースクリプト(old) |
| labels.user_script_v2 | Body | String | X | ユーザースクリプト |
| labels.additional_network_id_list | Body | String | X | ワーカーノードグループ適用：追加ネットワークのVPCネットワークUUIDリスト(コロン区切り) |
| labels.additional_subnet_id_list | Body | String | X | ワーカーノードグループ適用：追加ネットワークのVPCサブネットUUIDリスト(コロン区切り) |
| labels.extra_security_groups | Body | Array | X | 追加セキュリティグループオブジェクトリスト |
| labels.extra_security_groups[].target_subnet | Body | String | X | 追加セキュリティグループ指定対象サブネットUUID |
| labels.extra_security_groups[].security_group_ids | Body | String | X | 追加セキュリティグループUUIDリスト(カンマ区切り) |
| labels.extra_volumes | Body | Array | X | 追加ブロックストレージオブジェクトリスト |
| labels.extra_volumes[].volume_type | Body | String | X | 追加ブロックストレージの種類 |
| labels.extra_volumes[].volume_size | Body | Integer | X | 追加ブロックストレージサイズ(GB) |
| labels.extra_volumes[].volume_key_id | Body | String | X | (暗号化されたブロックストレージを使用する場合)暗号化されたブロックストレージに適用する対称鍵ID |
| labels.extra_volumes[].volume_appkey | Body | String | X | (暗号化されたブロックストレージを使用する場合)暗号化されたブロックストレージに適用する対称鍵のアプリキー |
| labels.extra_volumes[].volume_mount_path | Body | String | X | 追加ブロックストレージがマウントされるパス |
| labels.fip_auto_bind_enable | Body | String | X | フローティングIP自動割り当て:機能有効かどうか(`True` / `False`) |
| labels.fip_bind_subnet | Body | String | X | フローティングIP自動割り当て:フローティングIPが接続されるネットワークインターフェースのサブネット |
| labels.fip_selector | Body | String | X | フローティングIP自動割り当て:ノードに割り当てるフローティングIPを選別するための識別子 |
| labels.k8s_node_labels | Body | String | Kubernetesラベル設定 |
| name | BODY | String | O | ノードグループ名 |
| node_count | Body | Integer | X | ノード数(デフォルト値: 1) |


<details><summary>例</summary>
<p>

```json
{
    "name": "added-nodegroup",
    "node_count": 1,
    "flavor_id": "6ef27f21-c774-4c0e-84ff-7dd4a762571f",
    "image_id": "f462a2a5-ba24-46d6-b7a1-9a9febcd3cfc",
    "labels": {
        "availability_zone": "kr2-pub-b",
        "boot_volume_size": "20",
        "boot_volume_type": "General HDD",
        "ca_enable": "false"
    }
}
```

</p>
</details>

#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| uuid | Body | UUID | ノードグループUUID |
| cluster_id | Body  | UUID | ノードグループが属すクラスタUUID |
| flavor_id | Body | UUID | ノードで使用するインスタンスタイプUUID |
| image_id | Body | UUID | ノードで使用するベースイメージUUID |
| labels | Body | Object | ノードグループ作成情報オブジェクト |
| labels.availability_zone | Body | String | 基本ワーカーノードグループ適用：アベイラビリティゾーン |
| labels.boot_volume_type | Body | String | 基本ワーカーノードグループ適用：ブロックストレージの種類|
| labels.boot_volume_size | Body | String | 基本ワーカーノードグループ適用：ブロックストレージサイズ(GB) |
| labels.ca_enable | Body | String | 基本ワーカーノードグループ適用:クラスターオートスケーラー:機能有効かどうか("True" / "False") |
| labels.ca_pod_replicas | Body | String | 基本ワーカーノードグループ適用:クラスターオートスケーラー: Pod数 |
| labels.ca_max_node_count | Body | String | 基本ワーカーノードグループ適用:クラスターオートスケーラー:最大ノード数 |
| labels.ca_min_node_count | Body | String | 基本ワーカーノードグループ適用:クラスターオートスケーラー:最小ノード数 |
| labels.ca_scale_down_enable | Body | String | 基本ワーカーノードグループ適用:クラスターオートスケーラー:削減が有効かどうか("True" / "False") |
| labels.ca_scale_down_unneeded_time | Body | String | 基本ワーカーノードグループ適用:クラスターオートスケーラー:しきい値領域維持時間 |
| labels.ca_scale_down_util_thresh | Body | String | 基本ワーカーノードグループ適用:クラスターオートスケーラー:リソース使用量しきい値 |
| labels.ca_scale_down_delay_after_add | Body | String | 基本ワーカーノードグループ適用:クラスターオートスケーラー:増設後の削減遅延時間 |
| labels.mba_scale_out | Body | String | 基本ノードグループ適用:指標ベースのオートスケーラー増設ポリシー設定 |
| labels.mba_scale_in | Body | String | 基本ノードグループ適用:指標ベースのオートスケーラー削減ポリシー設定 |
| labels.user_script | Body | String | ユーザースクリプト(old) |
| labels.user_script_v2 | Body | String | ユーザースクリプト |
| labels.additional_network_id_list | Body | String | ワーカーノードグループ適用：追加ネットワークのVPCネットワークUUIDリスト(コロン区切り) |
| labels.additional_subnet_id_list | Body | String | ワーカーノードグループ適用：追加ネットワークのVPCサブネットUUIDリスト(コロン区切り) |
| max_node_count | Body | Integer | 最大ノード数 |
| min_node_count | Body | Integer | 最小ノード数 |
| name | BODY | String | ノードグループ名 |
| node_count | Body | Integer | ノード数(デフォルト値: 1) |
| project_id | Body | String | プロジェクト(テナント) ID |
| role | Body | String | ノードグループの役割 |

<details><summary>例</summary>
<p>

```json
{
    "cluster_id": "96742ac4-02e7-4b1d-a242-02876c0bd3f8",
    "flavor_id": "6ef27f21-c774-4c0e-84ff-7dd4a762571f",
    "image_id": "f462a2a5-ba24-46d6-b7a1-9a9febcd3cfc",
    "labels": {
        "availability_zone": "kr2-pub-b",
        "boot_volume_size": "20",
        "boot_volume_type": "General HDD",
        "ca_enable": "false",
        "ca_pod_replicas": "1",        
        "ca_max_node_count": "10",
        "ca_min_node_count": "1",
        "ca_scale_down_enable": "true",
        "ca_scale_down_unneeded_time": "10",
        "ca_scale_down_util_thresh": "50",
        "clusterautoscale": "nodegroupfeature",
        "user_script_v2": ""
    },
    "links": [
        {
            "href": "https://kr2-api-kubernetes-infrastructure.nhncloudservice/v1/clusters/96742ac4-02e7-4b1d-a242-02876c0bd3f8/nodegroups/a3366f2f-a1f3-45ef-8390-10536e8060ff",
            "rel": "self"
        },
        {
            "href": "https://kr2-api-kubernetes-infrastructure.nhncloudservice/clusters/96742ac4-02e7-4b1d-a242-02876c0bd3f8/nodegroups/a3366f2f-a1f3-45ef-8390-10536e8060ff",
            "rel": "bookmark"
        }
    ],
    "max_node_count": null,
    "min_node_count": 1,
    "name": "added-nodegroup",
    "node_count": 1,
    "project_id": "1ffeaca9bbf94ab1aa9cffdec29a258a",
    "role": "worker",
    "uuid": "a3366f2f-a1f3-45ef-8390-10536e8060ff"
}
```

</p>
</details>

---

### ノードグループ削除

指定したノードグループを削除します。
```
DELETE /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | クラスタUUIDまたはクラスタ名 | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | ノードグループUUIDまたはノードグループ名 | 

#### レスポンス

このAPIはレスポンス本文を返しません。

---


### ノードを停止する

指定したノードリストを停止させます。

```
POST /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}/stop_node
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | クラスタUUIDまたはクラスタ名 | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | ノードグループUUIDまたはノードグループ名 | 
| node_list | Body | String | O | コロン(`:`)で区切られたノードインスタンスUUIDリスト |

<details><summary>例</summary>
<p>

```json
{
    "node_list": "bdaa560c-7a30-4249-9438-2df27fa1e9d38:68ff49ee-4111-4212-8e9e-88835cb0ebaa"
}
```

</p>
</details>


#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| uuid | Body | UUID | ノードグループUUID |

<details><summary>例</summary>
<p>

```json
{
    "uuid": "439aa682-398f-4061-a4d1-116da6b1154e"
}
```

</p>
</details>

---

### ノードを起動する

指定したノードリストを起動させます。

```
POST /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}/start_node
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | クラスタUUIDまたはクラスタ名 | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | ノードグループUUIDまたはノードグループ名 | 
| node_list | Body | String | O | コロン(`:`)で区切られたノードインスタンスUUIDリスト |

<details><summary>例</summary>
<p>

```json
{
    "node_list": "bdaa560c-7a30-4249-9438-2df27fa1e9d38"
}
```

</p>
</details>

#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| uuid | Body | UUID | ノードグループUUID |

<details><summary>例</summary>
<p>

```json
{
    "uuid": "439aa682-398f-4061-a4d1-116da6b1154e"
}
```

</p>
</details>

---

### ノードグループのクラスターオートスケーラー設定表示

ノードグループのクラスターオートスケーラー設定を照会します。

```
GET /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}/autoscale
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | クラスタUUIDまたはクラスタ名 | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | ノードグループUUIDまたはノードグループ名 | 


#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| ca_enable | Body | String | 機能を有効にするかどうか("True" / "False") |
| ca_pod_replicas | Body | String | Pod数 |
| ca_max_node_count | Body | String | 最大ノード数 |
| ca_min_node_count | Body | String | 最小ノード数 |
| ca_scale_down_enable | Body | String | 削減が有効かどうか("True" / "False") |
| ca_scale_down_unneeded_time | Body | String | しきい値領域維持時間 |
| ca_scale_down_util_thresh | Body | String | リソース使用量しきい値 |
| ca_scale_down_delay_after_add | Body | String | 増設後の削減遅延時間 |

<details><summary>例</summary>
<p>

```json
{
    "ca_enable": true,
    "ca_pod_replicas": 1,
    "ca_max_node_count": 10,
    "ca_min_node_count": 2,
    "ca_scale_down_delay_after_add": 10,
    "ca_scale_down_enable": true,
    "ca_scale_down_unneeded_time": 10,
    "ca_scale_down_util_thresh": 50,
    "clusterautoscale": "nodegroupfeature"
}
```

</p>
</details>

---

### ノードグループのクラスターオートスケーラー設定の変更

ノードグループのクラスターオートスケーラー設定を変更します。

```
POST /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}/autoscale
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | クラスタUUIDまたはクラスタ名 | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | ノードグループUUIDまたはノードグループ名 | 
| ca_enable | Body | String | O | 機能を有効にするかどうか("True" / "False") |
| ca_pod_replicas | Body | String | X | Pod数 |
| ca_max_node_count | Body |X|String | 最大ノード数 |
| ca_min_node_count | Body |X|String | 最小ノード数 |
| ca_scale_down_enable | Body |X|String | 削減が有効かどうか("True" / "False") |
| ca_scale_down_unneeded_time | Body |X|String | しきい値領域維持時間 |
| ca_scale_down_util_thresh | Body | String | X |リソース使用量しきい値 |
| ca_scale_down_delay_after_add | Body | String | X |増設後の削減遅延時間 |

<details><summary>例</summary>
<p>

```json
{
    "ca_enable": true,
    "ca_pod_replicas": 1,    
    "ca_max_node_count": 10,
    "ca_min_node_count": 1,
    "ca_scale_down_delay_after_add": 30,
    "ca_scale_down_enable": true,
    "ca_scale_down_unneeded_time": 20,
    "ca_scale_down_util_thresh": 40
}
```

</p>
</details>

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | クラスターUUIDまたはクラスター名 | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | ノードグループUUIDまたはノードグループ名 | 
| type | Body | String | O | metric_base_autoscale設定 |
| mba_scale_out | Body | Object | X | 指標ベースオートスケール増設ポリシー設定 |
| mba_scale_out.enable | Body | Boolean | X | 機能有効かどうか(`true` / `false`) |
| mba_scale_out.max_node_count | Body | Integer | X | ノードグループの最大ノード数 |
| mba_scale_out.rules_operator | Body | String | X | 増設ルール間に適用される演算子(`AND` / `OR`) |
| mba_scale_out.delay | Body | Integer | X | 増設待機時間(1～60分) |
| mba_scale_out.adjustment_count | Body | Integer | X | 増設ノード数(1～10台) |
| mba_scale_out.rules | Body | List of Object | mba_scale_out.enable設定がtrueの場合、1つ以上のルール設定必要 | 増設ルール設定 |
| mba_scale_out.rules.metric | Body | Integer | mba_scale_out.enable設定がtrueの場合必須 | 指標設定 |
| mba_scale_out.rules.threshold | Body | Integer | mba_scale_out.enable設定がtrueの場合必須 | しきい値設定 |
| mba_scale_out.rules.duration | Body | Integer | mba_scale_out.enable設定がtrueの場合必須 | しきい値領域維持時間(2～60分) |
| mba_scale_in | Body | Object | X | 指標ベースオートスケール削減ポリシー設定 |
| mba_scale_in.enable | Body | Boolean | X | 機能有効かどうか(`true` / `false`) |
| mba_scale_in.min_node_count | Body | Integer | X | ノードグループの最小ノード数 |
| mba_scale_in.rules_operator | Body | String | X | 削減ルール間に適用される演算子(`AND` / `OR`) |
| mba_scale_in.delay | Body | Integer | X | 削減待機時間(1～60分) |
| mba_scale_in.adjustment_count | Body | Integer | X | 削減ノード数(1～10台) |
| mba_scale_in.rules | Body | List of Object | mba_scale_in.enable設定がtrueの場合1つ以上のルール設定必要 | 削減ルール設定 |
| mba_scale_in.rules.metric | Body | Integer | mba_scale_in.enable設定がtrueの場合必須 | 指標設定 |
| mba_scale_in.rules.threshold | Body | Integer | mba_scale_in.enable設定がtrueの場合必須 | しきい値設定 |
| mba_scale_in.rules.duration | Body | Integer | mba_scale_in.enable設定がtrueの場合必須 | しきい値領域維持時間(2～60分) |

##### 指標設定リスト

| システムリソース | 設定値 |
| --- | --- |
| CPU使用率 | GROUP_CPU_USAGE |
| メモリ使用率 | GROUP_MEMORY_USAGE |
| ディスク転送率(読み取り) | GROUP_DISK_READ_BYTES |
| ディスク転送率(書き込み) | GROUP_DISK_WRITE_BYTES |
| ネットワーク転送率(送信) | GROUP_NETWORK_SENT_BYTES |
| ネットワーク転送率(受信) | GROUP_NETWORK_RECV_BYTES |


<details><summary>有効化例</summary>
<p>

```json
{
    "type": "metric_base_autoscale",
    "mba_scale_out": {
        "enable": "True",
        "max_node_count": 6,
        "rules_operator": "or",
        "delay": 10,
        "adjustment_count": 2,
        "rules": [
            {
                "metric": "GROUP_CPU_USAGE",
                "threshold": 80,
                "duration": 2
            }
        ]
    },
    "mba_scale_in": {
        "enable": "True",
        "min_node_count": 2,
        "rules_operator": "or",
        "delay": 5,
        "adjustment_count": 1,
        "rules": [
            {
                "metric": "GROUP_CPU_USAGE",
                "threshold": 50,
                "duration": 2
            }
        ]
    }
}
```

</p>
</details>


<details><summary>無効化例</summary>
<p>

```json
{
    "type": "metric_base_autoscale",
    "mba_scale_out": {
        "enable": false
    },
    "mba_scale_in": {
        "enable": false
    }
}
```

</p>
</details>


#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| uuid | Body | UUID | ノードグループUUID |

<details><summary>例</summary>
<p>

```json
{
    "uuid": "018b06c5-1293-4081-8242-167a1cb9f262"
}
```

</p>
</details>

---

### クラスタのアップグレード

ノードグループをアップグレードします。

```
POST /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}/upgrade
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | クラスタUUIDまたはクラスタ名 | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | ノードグループUUIDまたはノードグループ名<br>コントロールプレーンのアップグレード時には**default-master**と指定 |  | 
| version | Body | String | O | Kubernetesバージョン |
| num_buffer_nodes | Body | Integer | X | バッファノード数。最小値：0、最大値：(ワーカーノードグループ当たりの最大ノード数クォーター - 該当ワーカーノードグループの現在のノード数)、デフォルト値: 1 |
| num_max_unavailable_nodes | Body |  Integer | X | 最大サービス不可ノード数。最小値：1、最大値：該当ワーカーノードグループの現在ノード数、デフォルト値：1 |

クラスターをアップグレードするには、コントロールプレーンをアップグレードした後、ワーカーコンポーネントをアップグレードする必要があります。コントロールプレーンとワーカーコンポーネントのアップグレードはノードグループ単位で行われます。

* コントロールプレーンコンポーネントのアップグレード
    * ノードグループ名を**default-master**と指定します。

* ワーカーコンポーネントのアップグレード
    * アップグレードするノードグループ名を指定します。


<details><summary>例</summary>
<p>

```json
{
    "version": "v1.19.13"
}
```

</p>
</details>


#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| uuid | Body | UUID | ノードグループUUID |

<details><summary>例</summary>
<p>

```json
{
    "uuid": "018b06c5-1293-4081-8242-167a1cb9f262"
}
```

</p>
</details>

---

### ユーザースクリプトを変更する

ノードグループのユーザースクリプトを変更します。

```
POST /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}/userscript
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | クラスタUUIDまたはクラスタ名 | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | ノードグループUUIDまたはノードグループ名<br>コントロールプレーンコンポーネントのアップグレード時には**default-master**に指定 | 
| contents | Body | String | O | ユーザースクリプト内容 |


<details><summary>例</summary>
<p>

```json
{
    "contents": "user script contents here..."
}
```

</p>
</details>


#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| uuid | Body | UUID | ノードグループUUID |

<details><summary>例</summary>
<p>

```json
{
    "uuid": "018b06c5-1293-4081-8242-167a1cb9f262"
}
```

</p>
</details>

---

### インスタンスタイプを変更する

ノードグループのインスタンスタイプを変更します。

```
PATCH /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | クラスタUUIDまたはクラスタ名 | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | ノードグループUUIDまたはノードグループ名 | 
| type | Body | String | O | `flavor_id`に設定 |
| flavor_id | Body | String | O | インスタンスタイプUUID |
| num_buffer_nodes | Body | Integer | X | バッファノード数。最小値：0、最大値：(ワーカーノードグループあたりの最大ノード数クォーター - 該当ワーカーノードグループの現在ノード数)、デフォルト値：1 |
| num_max_unavailable_nodes | Body |  Integer | X | 最大サービス不可ノード数。最小値：1、最大値：該当ワーカーノードグループの現在ノード数、デフォルト値: 1 |


<details><summary>例</summary>
<p>

```json
{
    "type": "flavor_id",
    "flavor_id": "1d0d6983-8e9d-44dc-810e-d7689afa372c",
    "num_buffer_nodes": 1,
    "num_max_unavailable_nodes":1
}
```

</p>
</details>


#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| uuid | Body | UUID | ノードグループUUID |

<details><summary>例</summary>
<p>

```json
{
    "uuid": "018b06c5-1293-4081-8242-167a1cb9f262"
}
```

</p>
</details>

---

### ノードグループのフローティングIP自動割り当て設定の変更

ノードグループのフローティングIP自動割り当て設定を変更します。

```
PATCH /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト


| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | クラスターUUIDまたはクラスター名 | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | ノードグループUUIDまたはノードグループ名 | 
| type | Body | String | O | `fip_auto_bind`に設定 |
| fip_auto_bind_update_info | Body | Object | O | フローティングIP自動割り当て設定情報オブジェクト |
| fip_auto_bind_update_info.fip_auto_bind_enable | Body | Boolean | O | 機能有効かどうか(`true` / `false`) |
| fip_auto_bind_update_info.fip_bind_subnet | Body |  String | O(enable設定がtrueの場合) | フローティングIPが接続されるネットワークインターフェースのサブネット。 <br> 接続するサブネットは必ずクラスターの基本サブネットかノードグループの追加サブネットに含まれている必要があります。 |
| fip_auto_bind_update_info.fip_selector | Body | String | X | ノードに割り当てるフローティングIPを選別するための識別子 |


<details><summary>例</summary>
<p>

```json
{
    "type": "fip_auto_bind",
    "fip_auto_bind_update_info": {
        "fip_auto_bind_enable": true,
        "fip_selector": "nks-fip",
        "fip_bind_subnet": "7f3237f6-ce05-4e9c-bce8-bbaabd22e83a"
    }
}
```

</p>
</details>


#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| uuid | Body | UUID | ノードグループUUID |

<details><summary>例</summary>
<p>

```json
{
    "uuid": "018b06c5-1293-4081-8242-167a1cb9f262"
}
```

</p>
</details>

---

### ノードグループのKubernetesラベル設定変更

ノードグループのKubernetesラベル設定を変更します。

```
PATCH /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | クラスターUUIDまたはクラスター名 | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | ノードグループUUIDまたはノードグループ名 | 
| type | Body | String | O | `k8s_node_labels`に設定 |
| k8s_node_labels | Body | Object | O | Kubernetesラベルキー、値のペアで構成された設定情報オブジェクト。最大20個まで設定可能 |


<details><summary>例</summary>
<p>

```json
{
    "type": "k8s_node_labels",
    "k8s_node_labels": {
        "node_type": "production",
        "pod_type": "opt"
    }
}
```

</p>
</details>


#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| uuid | Body | UUID | ノードグループUUID |

<details><summary>例</summary>
<p>

```json
{
    "uuid": "018b06c5-1293-4081-8242-167a1cb9f262"
}
```

</p>
</details>

---

## アドオン管理機能

### NHN Cloudが提供するアドオンタイプ表示
NHN Cloudが提供するアドオンタイプを確認できます。

```
GET /v1/addon_types/${ADDON_TYPE_UUID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| ADDON_TYPE_UUID_OR_NAME | URL | UUID or String | O | アドオンタイプのUUIDまたは名前 |

#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| uuid | Body | UUID | アドオンタイプUUID |
| name | Body | String | アドオンタイプ名 |
| mandatory | Body | boolean | 必須かどうか |

<details><summary>例</summary>
<p>

```json
{
    "uuid": "123e4567-e89b-12d3-a456-426614174001",
    "name": "cni",
    "mandatory": true
}
```

</p>
</details>

---

### NHN Cloudが提供するアドオンタイプリスト表示
NHN Cloudが提供するアドオンタイプのリストを確認できます。

```
GET /v1/addon_types/
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト
| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |

#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| addon_types | Body | List of object | アドオンタイプ情報のリスト |

<details><summary>例</summary>
<p>

```json
{
    "addon_types": [
        {"uuid": "123e4567-e89b-12d3-a456-426614174001", "name": "cni", "mandatory": true},
        {"uuid": "123e4567-e89b-12d3-a456-426614174003", "name": "kube-dns", "mandatory": true}
    ]
}
```

</p>
</details>

---

### NHN Cloudが提供するアドオン表示
NHN Cloudが提供するアドオンを確認できます。

```
GET /v1/addons/{ADDON_UUID}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| ADDON_UUID | URL | UUID | O | アドオンUUID |
| tokenId | Header | String | O | トークンID |


#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| uuid | Body | UUID | アドオンタイプUUID |
| type | Body | String | アドオンタイプ名 |
| version | Body | String | アドオンのバージョン |
| name | Body | String | アドオン名 |
| deploy_target | Body | String | (内部使用用途)アドオン配布タイプ |
| k8s_min_version | Body | String | (内部使用用途)サポート最小k8sバージョン |
| k8s_max_version | Body | String | (内部使用用途)サポート最大k8sバージョン |
| description | Body | String | アドオンの説明 |
| option_schemas | Body | List of object | オプション定義リスト |
| option_schemas.name | Body | String | オプションの名前 |
| option_schemas.data_type | Body | String | オプションデータ型。 `STRING`, `INTEGER`, `SELECT`のいずれか |
| option_schemas.default | Body | String | オプションのデフォルト値 |
| option_schemas.updatable | Body | Boolean | オプション変更可否 |
| option_schemas.mandatory | Body | Boolean | 必須かどうか |
| option_schemas.choices | Body | List of String | 選択可能な値のリスト |


<details><summary>例</summary>
<p>

```json
{
    "uuid": "23454567-1234-12d3-a456-426614174001",
    "type": "cni",
    "version": "v3.28.2-nks1",
    "name": "calico",
    "option_schemas": [
        {
            "name": "mode",
            "data_type": "SELECT",
            "default": "vxlan",
            "updatable": false,
            "mandatory": false,
            "choices": ["vxlan", "ebpf"]
        }
    ],
    "k8s_min_version": "v1.26.0",
    "k8s_max_version": null,
    "description": "Calico is a CNI plugin for Kubernetes that provides networking and network security."
}
```

</p>
</details>

---

### NHN Cloudが提供するアドオンリスト表示
NHN Cloudが提供するアドオンリストを確認できます。

```
GET /v1/addons/
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |

#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| addons | Body | List of object | アドオン情報のリスト |


<details><summary>例</summary>
<p>

```json
{
    "addons": [
        {"uuid": "23454567-1234-12d3-a456-426614174001", "type": "cni", "version": "v3.28.2-nks1", "name": "calico", "option_schemas": [{"name": "mode", "data_type": "SELECT", "default": "vxlan", "updatable": false, "mandatory": false, "choices": ["vxlan", "ebpf"]}], "k8s_min_version": "v1.26.0", "k8s_max_version": null, "description": "Calico is a CNI plugin for Kubernetes that provides networking and network security."},
        {"uuid": "23454567-1234-12d3-a456-426614174005", "type": "kube-dns", "version": "1.8.4-nks1", "name": "coredns", "option_schemas": [], "k8s_min_version": "v1.26.0", "k8s_max_version": null, "description": "CoreDNS is the default DNS server for Kubernetes clusters."}
    ]
}


```

</p>
</details>

---

### クラスターにインストールされたアドオン表示
クラスターにインストールされたアドオンを確認できます。

```
GET /v1/clusters/{CLUSTER_ID_OR_NAME}/addons/{ADDON_UUID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | クラスターUUIDまたはクラスター名 |
| ADDON_UUID_OR_NAME | URL | UUID or String | O | アドオンUUIDまたはアドオン名 |


#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| uuid | Body | UUID | アドオンタイプUUID |
| project_id | Body | String | プロジェクトID |
| cluster_uuid | Body | UUID | クラスターUUID |
| cluster_name | Body | String | クラスター名 |
| type | Body | String | アドオンタイプ名 |
| version | Body | String | アドオンのバージョン |
| options | Body | Object | アドオン別オプション |
| name | Body | String | アドオン名 |
| status | Body | String | アドオンの状態 |
| status_reason | Body | String | アドオンの状態の理由 |
| scope | Body | String | 適用範囲 |
| target_uuid | Body | UUID | 適用対象UUID |
| created_at | Body | String | 作成時間(UTC) |
| updated_at | Body | String | 最終更新日(UTC) |

<details><summary>例</summary>
<p>

```json
{
    "uuid": "0b29e253-fb0d-4888-a8fe-d287c65ba76b",
    "project_id": "1ffeaca9bbf94ab1aa9cffdec29a258a",
    "cluster_uuid": "6c1284e2-8ead-46a7-ace9-c19d6eec76b3",
    "cluster_name": "tw-addon3",
    "type": "cni",
    "version": "v3.28.2-nks1",
    "options": {"mode": "vxlan"},
    "name": "calico",
    "status": "UPDATE_COMPLETE",
    "status_reason": null,
    "scope": "cluster",
    "target_uuid": "6c1284e2-8ead-46a7-ace9-c19d6eec76b3",
    "created_at": "2025-04-25T15:11:48+00:00",
    "updated_at": "2025-04-25T15:17:16+00:00"
}
```

</p>
</details>

---

### クラスターにインストールされたアドオンリスト表示
クラスターにインストールされたアドオンリストを確認できます。

```
GET /v1/clusters/{CLUSTER_ID_OR_NAME}/addons/
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | クラスターUUIDまたはクラスター名 |

#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| addons | Body | List of Object | インストールされたアドオン情報のリスト |

<details><summary>例</summary>
<p>

```json
{
    "addons": [
        {"uuid": "0b29e253-fb0d-4888-a8fe-d287c65ba76b", "project_id": "1ffeaca9bbf94ab1aa9cffdec29a258a", "cluster_uuid": "6c1284e2-8ead-46a7-ace9-c19d6eec76b3", "cluster_name": "tw-addon3", "type": "cni", "version": "v3.28.2-nks1", "options": {"mode": "vxlan"}, "name": "calico", "status": "UPDATE_COMPLETE", "status_reason": null, "scope": "cluster", "target_uuid": "6c1284e2-8ead-46a7-ace9-c19d6eec76b3", "created_at": "2025-04-25T15:11:48+00:00", "updated_at": "2025-04-25T15:17:16+00:00"},
        {"uuid": "be71a120-7596-4b25-bee5-d5317e5134ee", "project_id": "1ffeaca9bbf94ab1aa9cffdec29a258a", "cluster_uuid": "6c1284e2-8ead-46a7-ace9-c19d6eec76b3", "cluster_name": "tw-addon3", "type": "kube-dns", "version": "1.8.4-nks1", "options": {}, "name": "coredns", "status": "UPDATE_FAILED", "status_reason": null, "scope": "cluster", "target_uuid": "6c1284e2-8ead-46a7-ace9-c19d6eec76b3", "created_at": "2025-05-02T06:16:39+00:00", "updated_at": "2025-05-08T01:03:19+00:00"}
    ]
}
```

</p>
</details>

---

### クラスターにアドオンをインストール
クラスターにアドオンをインストールします。

```
POST /v1/clusters/{CLUSTER_ID_OR_NAME}/addons/
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | クラスターUUIDまたはクラスター名 |
| name | Body | String | O | アドオン名 |
| version | Body | String | O | アドオンのバージョン |
| resolve_conflicts | Body | String | O | 競合オプション。 `none`, `overwrite`, `preserve`のいずれか |


<details><summary>例</summary>
<p>

```json
{"version": "1.8.4-nks1", "name": "coredns", "resolve_conflicts": "overwrite"}
```

</p>
</details>


#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| uuid | Body | UUID | クラスターUUID |

<details><summary>例</summary>
<p>

```json
{
    "uuid": "6c1284e2-8ead-46a7-ace9-c19d6eec76b3"
}
```

</p>
</details>

---

### クラスターにアドオンを更新
クラスターにインストールされたアドオンを更新します。

```
PATCH /v1/clusters/{CLUSTER_ID_OR_NAME}/addons/{ADDON_UUID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | クラスターUUIDまたはクラスター名 |
| ADDON_UUID_OR_NAME | URL | UUID or String | O | アドオンUUIDまたはアドオン名 |
| version | Body | String | O | アドオンのバージョン |
| resolve_conflicts | Body | String | O | 競合オプション。 `none`, `overwrite`, `preserve`のいずれか |


<details><summary>例</summary>
<p>

```json
{"version": "1.8.4-nks1", "resolve_conflicts": "none"}
```

</p>
</details>


#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| uuid | Body | UUID | クラスターUUID |

<details><summary>例</summary>
<p>

```json
{
    "uuid": "6c1284e2-8ead-46a7-ace9-c19d6eec76b3"
}
```

</p>
</details>

---

### クラスターにインストールされたアドオンの削除
クラスターにインストールされたアドオンを削除します。

```
DELETE /v1/clusters/{CLUSTER_ID_OR_NAME}/addons/{ADDON_UUID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | クラスターUUIDまたはクラスター名 |
| ADDON_UUID_OR_NAME | URL | UUID or String | O | アドオンUUIDまたはアドオン名 |

#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| uuid | Body | UUID | クラスターUUID |

<details><summary>例</summary>
<p>

```json
{
    "uuid": "6c1284e2-8ead-46a7-ace9-c19d6eec76b3"
}
```

</p>
</details>



## その他機能

### サポートされるKubernetesバージョン及び作業の種類を表示

NHN Kubernetes Service(NKS)でサポートするKubernetesバージョン及び作業タイプを照会します。

```
GET /v1/supports
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### リクエスト

このAPIはリクエスト本文を要求しません。

| 名前 | 種類 | 形式 | 必須 | 説明 |
|---|---|---|---|---|
| tokenId | Header | String | O | トークンID |


#### レスポンス

| 名前 | 種類 | 形式 | 説明 |
|---|---|---|---|
| supported_k8s | Body | Object | サポートされるKubernetesバージョンオブジェクト |
| supported_k8s."バージョン名" | Body | String | Kubernetesバージョンの有効性(`True`/`False`) |
| supported_event_type."作業タイプ"| Body | Object | サポートされる作業タイプオブジェクト(`cluster_events`/`nodegroup_events`) |
| supported_event_type."作業タイプ"."作業名"| Body | Object | 作業タイプ及び説明 |

<details><summary>例</summary>
<p>

```json
{
    "supported_k8s": {
        "v1.17.6": false,
        "v1.18.19": false,
        "v1.19.13": false,
        "v1.20.12": false,
        "v1.21.6": false,
        "v1.22.3": false,
        "v1.23.3": false,
        "v1.24.3": false,
        "v1.25.4": false,
        "v1.26.3": false,
        "v1.27.3": false,
        "v1.28.3": false,
        "v1.29.3": true,
        "v1.30.3": true,
        "v1.31.4": true,
        "v1.32.3": true
    },
    "supported_event_type": {
        "CLUSTER_CREATE": "クラスター作成",
        "CLUSTER_DELETE": "クラスター削除",
        "CLUSTER_HANDOVER": "クラスターOWNER変更",
        "CLUSTER_UPDATE_VM_AUTH_KEY": "キーペア更新",
        "NODEGROUP_CREATE": "ノードグループ作成",
        "NODEGROUP_DELETE": "ノードグループ削除",
        "CLUSTER_RESIZE": "クラスターサイズ調整",
        "NODEGROUP_SCALE_OUT": "ノード増設",
        "NODEGROUP_SCALE_IN": "ノード削減",
        "NODEGROUP_UPDATE_FLAVOR": "インスタンスタイプ変更",
        "NODEGROUP_UPGRADE": "ノードグループのアップグレード",
        "NODEGROUP_USERSCRIPT_UPDATE": "ユーザースクリプト変更",
        "NODEGROUP_SET_CLUSTER_AUTOSCALER": "クラスターオートスケーラー設定変更",
        "NODEGROUP_SET_METRIC_BASE_AUTOSCALER": "指標ベースのオートスケーラー設定変更",
        "NODEGROUP_METRIC_BASE_AUTOSCALER_SCALE_OUT": "しきい値ベースのオートスケーラーによるノード増設",
        "NODEGROUP_METRIC_BASE_AUTOSCALER_SCALE_IN": "しきい値ベースのオートスケーラーによるノード削減",
        "CLUSTER_API_EP_IPACL_UPDATE": "クラスターAPIエンドポイントIPアクセス制御変更",
        "NODEGROUP_NODE_ACTION_START_NODE": "ワーカーノード開始",
        "NODEGROUP_NODE_ACTION_STOP_NODE": "ワーカーノード停止",
        "CLUSTER_UPDATE_SGW": "クラスターサービスゲートウェイ変更",
        "CLUSTER_ROTATE_CERTIFICATE": "クラスター証明書の更新",
        "CLUSTER_UPDATE_NKS_REGISTRY": "NKSレジストリ有効化",
        "NODEGROUP_UPDATE_EXTRA_VOLUME": "追加ブロックストレージ変更",
        "NODEGROUP_UPDATE_EXTRA_SECURITY_GROUP": "追加セキュリティグループ変更",
        "CLUSTER_UPDATE_K8S_ARGS": "Kubernetesコンポーネントオプション変更",
        "CLUSTER_UPDATE_OIDC_ARGS": "OIDC設定変更",
        "NODEGROUP_UPDATE_K8S_NODE_LABELS": "ノードグループKubernetesラベル設定変更",
        "CLUSTER_INSTALL_ADDON": "Addonのインストール",
        "CLUSTER_UNINSTALL_ADDON": "Addonの削除",
        "CLUSTER_UPDATE_ADDON": "Addonの更新",
        "CLUSTER_UPDATE_CONTROL_PLANE_LOG": "コントロールプレーンログ収集の更新",
        "NODEGROUP_UPDATE_FIP_AUTO_BIND": "ノードグループフローティングIP自動割り当て設定変更",
        "K8S_API_NOT_WORKING": "kube-apiserver停止",
        "ALL_NODES_NOT_READY": "全てのノード停止状態",
        "AUTO_HEALING": "オートヒーリング"
    }
}
```

</p>
</details>
