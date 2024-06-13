## Container > NHN Kubernetes Service(NKS) > API v2ガイド

Kubernetesクラスタを構成するためのAPIを記述します。
APIを使用するにはAPIエンドポイントとトークンなどが必要です。 [API使用準備](/Compute/Compute/ko/identity-api/)を参照してAPIの使用に必要な情報を準備します。

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

ネットワークリスト照会APIの詳細については、[ネットワークリスト表示](/Network/VPC/ko/public-api/#_2)を参照してください。


### インターネットゲートウェイに接続されたサブネットUUIDリスト

インターネットゲートウェイに接続されたVPCネットワークに接続されたサブネットUUIDを入力します。複数のサブネットが検索された場合はコロン(`:`)でつなげて入力します。サブネットリスト照会APIの詳細については、[サブネットリスト表示](/Network/VPC/ko/public-api/#vpc_7)を参照してください。


### VPCネットワークUUID

ノードと接続する内部VPCネットワークUUIDを入力します。ネットワークリスト照会APIの詳細については[ネットワークリスト表示](/Network/VPC/ko/public-api/#vpc_1)を参照してください。

### VPCサブネットUUID

ノードと接続する内部VPCネットワークに接続されたサブネットUUIDを入力します。サブネットリスト照会APIの詳細については[サブネットリスト表示](/Network/VPC/ko/public-api/#vpc_7)を参照してください。

### アベイラビリティゾーンUUID

ノードを作成するアベイラビリティゾーンUUIDを入力します。アベイラビリティゾーンリスト照会APIの詳細については[可用性リスト表示](/Compute/Instance/ko/public-api/#_9)を参照してください。

### キーペアUUID

ノード接続時に使用するキーペアを入力します。キーペアリスト照会APIの詳細については[キーペアリスト表示](/Compute/Instance/ko/public-api/#_13)を参照してください。

### ベースイメージUUID

ノードの作成に使用するベースイメージUUIDを入力します。 NKSノードの作成に使用されるベースイメージだけをフィルタリングするため、API呼び出し時にクエリストリングパラメータに`nhncloud_product=container&visibility=public`値を入力します。ベースイメージリスト照会APIの詳細については[イメージリスト照会](/Compute/Image/ko/public-api/#_2)を参照してください。

### ブロックストレージの種類

ノードに使用するブロックストレージUUIDを入力します。ブロックストレージタイプリスト照会APIの詳細については[ボリュームタイプリスト表示](/Storage/Block%20Storage/ko/public-api/#_2)を参照してください。

### インスタンスタイプUUID

作成するノードのインスタンスタイプUUIDを入力します。インスタンスタイプリスト照会APIの詳細については[インスタンスタイプリスト表示](/Compute/Instance/ko/public-api/#_2)を参照してください。



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
| clusters.stack_id | Body | UUID | マスターノードグループと接続されたheat stack UUID |
| clusters.status | Body | String | クラスタの状態 |
| clusters.labels | Body | Object | クラスタのラベル |
| clusters.labels.kube_tag | Body |String | マスターノードグループのKubernetesバージョン |
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
| stack_id | Body | UUID | マスターノードグループと接続されたheat stack UUID |
| status | Body | String | クラスタの状態 |
| status_reason | Body | String | クラスタ状態理由(null可能) |
| api_address | Body | String | Kubernetes APIエンドポイント |
| project_id | Body | String | プロジェクト(テナント) ID |
| fixed_network | Body | UUID | VPC UUID|
| fixed_subnet | Body | UUID | VPC Subnet UUID |
| node_addresses | Body | String List | ワーカーノードIPアドレスリスト |
| created_at | Body | String | 作成時間(UTC) |
| updated_at | Body | String | 最終更新日(UTC) |
| labels | Body | Object | クラスタラベル |
| labels.kube_tag | Body |String | マスターノードグループKubernetesバージョン |
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
| labels.ca_enable | Body | String | O | 基本ワーカーノードグループ適用：オートスケーラー：機能を有効にするかどうか("True" / "False") |
| labels.ca_max_node_count | Body | String | X | 基本ワーカーノードグループ適用：オートスケーラー：最大ノード数 |
| labels.ca_min_node_count | Body | String | X | 基本ワーカーノードグループ適用：オートスケーラー：最小ノード数 |
| labels.ca_scale_down_enable | Body | String | X | 基本ワーカーノードグループ適用：オートスケーラー：縮小が有効かどうか("True" / "False") |
| labels.ca_scale_down_unneeded_time | Body | String | X | 基本ワーカーノードグループ適用：オートスケーラー：しきい値領域維持時間 |
| labels.ca_scale_down_util_thresh | Body | String | X | 基本ワーカーノードグループ適用：オートスケーラー：リソース使用量しきい値 |
| labels.ca_scale_down_delay_after_add | Body | String | X | 基本ワーカーノードグループ適用：オートスケーラー：増設後の縮小遅延時間 |
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
| labels.obs_sgw | Body | String | X | OBSタイプのサービスゲートウェイUUID<br>ただし、クラスタVPCと同じVPCに生成されたものに限る。 |
| flavor_id | Body | UUID | O | 基本ワーカーノードグループ適用：ノードインスタンスタイプUUID |
| fixed_network | Body | UUID | O | VPC Network UUID |
| fixed_subnet | Body | UUID | O | VPCサブネットUUID. fixed_subnet, pods_network_cidr, service_cluster_ip_range入力ルール参照 |
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
        "user_script_v2": ""
    },
    "name": "test-k8s",
    "node_count": 1
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
  * ノードを縮小する場合(すなわち一部ノード削除)削除するノードを指定するには**nodes_to_remove**を設定する必要があります。削除するノードを指定しなかった場合、削除対象ノードはランダムに選択されます。
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

<details><summary>縮小例</summary>
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

### クラスタCNIの変更
クラスタCNI(container network interface)を変更します。Flannel CNIを他のCNIに変更できます。変更できるCNIの種類と変更可能条件については[ユーザーガイド](/Container/NKS/ko/user-guide/#cni)を参照してください。

```
POST /v1/clusters/{CLUSTER_ID_OR_NAME}/actions/cni_update
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
| cni | Body | String | O | 変更するCNIを設定(選択可能CNIリスト：calico) | 
| num_buffer_nodes | Body | Integer | X | バッファノード数。デフォルト値：1、最小値：0、最大値：各ワーカーノードグループにて追加で作成可能なノード数(ワーカーノードグループあたりの最大ノード数クォーター - 該当ワーカーノードグループの現在のノード数)うち最小値。 |
| num_max_unavailable_nodes | Body |  Integer | X | 最大サービス不可ノード数。最小値：1、最大値：該当clusterの現在ノード数、デフォルト値：1 |
| pod_cidr | Body | String | O | calico pod cidr設定、 pod_cidr入力ルール参考 |
| pod_subnet | Body | String | O | calico pod cidr subnet設定、デフォルト値: 24、 pod_subnet入力ルール参考 |

pod_cidrは、以下のようなルールで入力する必要があります。
* CIDRはリンクローカルアドレス帯域(169.254.0.0/16)と重複できません。
* CIDRはNKSクラスタに使用されたサービスIP帯域(K8sサービスネットワーク)と重複できません。
* CIDRはNKS内部で使用しているIP帯域(198.18.0.0/19)と重複できません。
* CIDRはNKSクラスタに接続されたVPCネットワークサブネットまたは追加ネットワークサブネットの帯域と重複できません。
* CIDRは現在NKSクラスタに使用されているpodネットワーク帯域値と重複できません。
* /24より大きいCIDRブロックは入力できません。 (次のようなCIDRブロックは使用できません。 /26, /30)

pod_subnetは以下のようなルールで入力する必要があります。
* 20-28(含む)範囲の値のみ入力可能です。
* pod_subnetの値がpod_cidrのprefixの値より最低2大きい必要があります。正常例(Podサブネットサイズ: 24、Podネットワーク: 10.100.0.0/22)



<details><summary>例</summary>
<p>

```json
{
    "cni": "calico",
    "num_max_unavailable_nodes": 1,
    "num_buffer_nodes": 1,
    "pod_cidr": "10.200.0.0/16"
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
---

### クラスタAPIエンドポイントIPアクセス制御適用
クラスタAPIエンドポイントにIPアクセス制御を適用または解除できます。
IPアクセス制御機能の詳細については、 [IPアクセス制御](/Network/Load%20Balancer/ko/overview/#ip)文書を参照してください。
クラスタAPIエンドポイントへのIPアクセス制御ルールに関する詳細については、 [ユーザーガイド](/Container/NKS/ko/user-guide/#_67)を参照してください。

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
| term_of_validity | Body | Integer | O | 証明書の有効期間。年単位で力可能。最小値: 1、最大値: 5 |

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
| labels.ca_enable | Body | String | ワーカーノードグループ適用：オートスケーラー：機能を有効にするかどうか("True" / "False") |
| labels.ca_max_node_count | Body | String | ワーカーノードグループ適用：オートスケーラー：最大ノード数 |
| labels.ca_min_node_count | Body | String | ワーカーノードグループ適用：オートスケーラー：最小ノード数 |
| labels.ca_scale_down_enable | Body | String | ワーカーノードグループ適用：オートスケーラー：縮小が有効かどうか("True" / "False") |
| labels.ca_scale_down_unneeded_time | Body | String | ワーカーノードグループ適用：オートスケーラー：しきい値領域維持時間 |
| labels.ca_scale_down_util_thresh | Body | String | ワーカーノードグループ適用：オートスケーラー：リソース使用量しきい値 |
| labels.ca_scale_down_delay_after_add | Body | String | ワーカーノードグループ適用：オートスケーラー：増設後の縮小遅延時間 |
| labels.kube_tag | Body | String | ワーカーノードグループKubernetesバージョン |
| labels.user_script | Body | String | ユーザースクリプト(old) |
| labels.user_script_v2 | Body | String | ユーザースクリプト |
| labels.additional_network_id_list | Body | String | ワーカーノードグループ適用：追加ネットワークのVPCネットワークUUIDリスト(コロン区切り) |
| labels.additional_subnet_id_list | Body | String | ワーカーノードグループ適用：追加ネットワークのVPCサブネットUUIDリスト(コロン区切り) |
| labels.strict_sg_rules | Body | String | ワーカーノードセキュリティグループに必須セキュリティルールのみ作成("True" / "False"), (2024.02.27. 以降に作成されたクラスタで確認可能) |
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
        "ca_image": "k8s.gcr.io/autoscaling/cluster-autoscaler:v1.19.0",
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
| labels.ca_enable | Body | String | O | 基本ワーカーノードグループ適用：オートスケーラー：機能を有効にするかどうか("True" / "False") |
| labels.ca_max_node_count | Body | String | X | 基本ワーカーノードグループ適用：オートスケーラー：最大ノード数 |
| labels.ca_min_node_count | Body | String | X | 基本ワーカーノードグループ適用：オートスケーラー：最小ノード数 |
| labels.ca_scale_down_enable | Body | String | X | 基本ワーカーノードグループ適用：オートスケーラー：縮小が有効かどうか("True" / "False") |
| labels.ca_scale_down_unneeded_time | Body | String | X | 基本ワーカーノードグループ適用：オートスケーラー：しきい値領域維持時間 |
| labels.ca_scale_down_util_thresh | Body | String | X | 基本ワーカーノードグループ適用：オートスケーラー：リソース使用量しきい値 |
| labels.ca_scale_down_delay_after_add | Body | String | X | 基本ワーカーノードグループ適用：オートスケーラー：増設後の縮小遅延時間 |
| labels.user_script | Body | String | X | ユーザースクリプト(old) |
| labels.user_script_v2 | Body | String | X | ユーザースクリプト |
| labels.additional_network_id_list | Body | String | X | ワーカーノードグループ適用：追加ネットワークのVPCネットワークUUIDリスト(コロン区切り) |
| labels.additional_subnet_id_list | Body | String | X | ワーカーノードグループ適用：追加ネットワークのVPCサブネットUUIDリスト(コロン区切り) |
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
| labels.ca_enable | Body | String | 基本ワーカーノードグループ適用：オートスケーラー：機能を有効にするかどうか("True" / "False") |
| labels.ca_max_node_count | Body | String | 基本ワーカーノードグループ適用：オートスケーラー：最大ノード数 |
| labels.ca_min_node_count | Body | String | 基本ワーカーノードグループ適用：オートスケーラー：最小ノード数 |
| labels.ca_scale_down_enable | Body | String | 基本ワーカーノードグループ適用：オートスケーラー：縮小が有効かどうか("True" / "False") |
| labels.ca_scale_down_unneeded_time | Body | String | 基本ワーカーノードグループ適用：オートスケーラー：しきい値領域維持時間 |
| labels.ca_scale_down_util_thresh | Body | String | 基本ワーカーノードグループ適用：オートスケーラー：リソース使用量しきい値 |
| labels.ca_scale_down_delay_after_add | Body | String | 基本ワーカーノードグループ適用：オートスケーラー：増設後の縮小遅延時間 |
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

### ノードグループのオートスケーラー設定表示

ノードグループのオートスケーラー設定を照会します。

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
| ca_max_node_count | Body | String | 最大ノード数 |
| ca_min_node_count | Body | String | 最小ノード数 |
| ca_scale_down_enable | Body | String | 縮小が有効かどうか("True" / "False") |
| ca_scale_down_unneeded_time | Body | String | しきい値領域維持時間 |
| ca_scale_down_util_thresh | Body | String | リソース使用量しきい値 |
| ca_scale_down_delay_after_add | Body | String | 増設後の縮小遅延時間 |

<details><summary>例</summary>
<p>

```json
{
    "ca_enable": true,
    "ca_image": "k8s.gcr.io/autoscaling/cluster-autoscaler:v1.19.0",
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

### ノードグループのオートスケーラー設定変更

ノードグループのオートスケーラー設定を変更します。

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
| ca_max_node_count | Body |X|String | 最大ノード数 |
| ca_min_node_count | Body |X|String | 最小ノード数 |
| ca_scale_down_enable | Body |X|String | 縮小が有効かどうか("True" / "False") |
| ca_scale_down_unneeded_time | Body |X|String | しきい値領域維持時間 |
| ca_scale_down_util_thresh | Body | String | X |リソース使用量しきい値 |
| ca_scale_down_delay_after_add | Body | String | X |増設後の縮小遅延時間 |

<details><summary>例</summary>
<p>

```json
{
    "ca_enable": true,
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
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | ノードグループUUIDまたはノードグループ名<br>マスターコンポーネントのアップグレード時には**default-master**と指定 |  | 
| version | Body | String | O | Kubernetesバージョン |
| num_buffer_nodes | Body | Integer | X | バッファノード数。最小値：0、最大値：(ワーカーノードグループ当たりの最大ノード数クォーター - 該当ワーカーノードグループの現在のノード数)、デフォルト値: 1 |
| num_max_unavailable_nodes | Body |  Integer | X | 最大サービス不可ノード数。最小値：1、最大値：該当ワーカーノードグループの現在ノード数、デフォルト値：1 |

クラスタをアップグレードするには、マスターコンポーネントをアップグレードした後、ワーカーコンポーネントをアップグレードする必要があります。マスターとワーカーコンポーネントのアップグレードはノードグループ単位で行われます。

* マスターコンポーネントのアップグレード
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
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | ノードグループUUIDまたはノードグループ名<br>マスターコンポーネントのアップグレード時には**default-master**に指定 | 
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
| supported_k8s."バージョン名" | Body | String | Kubernetesバージョンの有効性(True/False) |
| supported_event_type."作業タイプ"| Body | Object | サポートされる作業タイプオブジェクト(cluster_events/nodegroup_events) |
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
        "v1.26.3": true,
        "v1.27.3": true,
        "v1.28.3": true,
        "v1.29.3": true
    },
    "supported_event_type": {
        "cluster_events": {
            "CLUSTER_CREATE": "クラスタの作成",
            "CLUSTER_DELETE": "クラスタの削除",
            "CLUSTER_HANDOVER": "クラスタOWNERの変更",
            "CLUSTER_CNI_UPDATE": "CNI変更"
        },
        "nodegroup_events": {
            "NODEGROUP_CREATE": "ノードグループの作成",
            "NODEGROUP_DELETE": "ノードグループの削除",
            "CLUSTER_RESIZE": "クラスタサイズの調整",
            "NODEGROUP_UPDATE_FLAVOR": "インスタンスタイプの変更",
            "NODEGROUP_UPGRADE": "ノードグループのアップグレード",
            "NODEGROUP_USERSCRIPT_UPDATE": "ユーザースクリプトの変更",
            "NODEGROUP_SET_CLUSTER_AUTOSCALER": "オートスケーラーの設定変更",
            "NODEGROUP_NODE_ACTION_NODE_START": "ワーカーノードの起動",
            "NODEGROUP_NODE_ACTION_NODE_STOP": "ワーカーノードの停止"
        }
    }
}
```

</p>
</details>
