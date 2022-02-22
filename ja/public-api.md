## Container > NHN Kubernetes Service(NKS) > API v2ガイド

Kubernetesクラスタを構成するためのAPIを記述します。
APIを使用するにはAPIエンドポイントとトークンなどが必要です。 [API使用準備](/Compute/Compute/ko/identity-api/)を参照してAPIの使用に必要な情報を準備します。

すべてのAPIは`kubernetes`タイプエンドポイントを利用して呼び出します。

| タイプ | リージョン | エンドポイント |
|---|---|---|
| kubernetes | 韓国(パンギョ)リージョン<br>韓国(坪村)リージョン | https://kr1-api-kubernetes.infrastructure.cloud.toast.com <br>https://kr2-api-kubernetes.infrastructure.cloud.toast.com |


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

インターネットゲートウェイに接続されたVPCネットワークに接続されたサブネットUUIDを入力します。複数のサブネットが検索された場合はコロン(`:`)でつなげて力します。サブネットリスト照会APIの詳細については、[サブネットリスト表示](/Network/VPC/ko/public-api/#_6)を参照してください。


### VPCネットワークUUID

ノードと接続する内部VPCネットワークUUIDを入力します。ネットワークリスト照会APIの詳細については[ネットワークリスト表示](/Network/VPC/ko/public-api/#_2)を参照してください。

### VPCサブネットUUID

ノードと接続する内部VPCネットワークに接続されたサブネットUUIDを入力します。サブネットリスト照会APIの詳細については[サブネットリスト表示](/Network/VPC/ko/public-api/#_6)を参照してください。

### アベイラビリティゾーンUUID

ノードを作成するアベイラビリティゾーンUUIDを入力します。アベイラビリティゾーンリスト照会APIの詳細については[可用性リスト表示](/Compute/Instance/ko/public-api/#_9)を参照してください。

### キーペアUUID

ノード接続時に使用するキーペアを入力します。キーペアリスト照会APIの詳細については[キーペアリスト表示](/Compute/Instance/ko/public-api/#_13)を参照してください。

### ベースイメージUUID

ノードの作成に使用するベースイメージUUIDを入力します。各リージョンのベースイメージUUIDは次のとおりです。

| リージョン | ベースイメージUUID |
|---|---|
| 韓国(パンギョ)リージョン | 2b03f75e-c583-4198-8821-6eba31ab621e |
| 韓国(坪村)リージョン | a3c175ce-6477-4de0-b8d1-168dc9235fef |

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
| clusters.labels.boot_volume_type | Body | String | 基本ワーカーノードグループ適用：ブロックストレージの種類|
| clusters.labels.boot_volume_size | Body | String | 基本ワーカーノードグループ適用：ブロックストレージサイズ(GB) |
| clusters.labels.external_network_id | Body | String | インターネットゲートウェイに接続されたVPC network UUID |
| clusters.labels.external_subnet_id_list | Body | String | インターネットゲートウェイに接続されたサブネットUUIDリスト(コロンで区切る) |
| clusters.labels.cert_manager_api | Body | String | CSR(Certificate Signing Request)機能を有効にするかどうか。必ず"True"に設定 |
| clusters.labels.ca_enable | Body | String | 基本ワーカーノードグループ適用：オートスケーラー：機能を有効にするかどうか("True" / "False") |
| clusters.labels.ca_max_node_count | Body | String | 基本ワーカーノードグループ適用：オートスケーラー：最大ノード数 |
| clusters.labels.ca_min_node_count | Body | String | 基本ワーカーノードグループ適用：オートスケーラー：最小ノード数 |
| clusters.labels.ca_scale_down_enable | Body | String | 基本ワーカーノードグループ適用：オートスケーラー：縮小が有効かどうか("True" / "False") |
| clusters.labels.ca_scale_down_unneeded_time | Body | String | 基本ワーカーノードグループ適用：オートスケーラー：しきい値領域維持時間 |
| clusters.labels.ca_scale_down_util_thresh | Body | String | 基本ワーカーノードグループ適用：オートスケーラー：リソース使用量しきい値 |
| clusters.labels.ca_scale_down_delay_after_add | Body | String | 基本ワーカーノードグループ適用：オートスケーラー：増設後の縮小遅延時間 |
| clusters.labels.user_script | Body | String | 予約スクリプト |
| clusters.labels.master_lb_floating_ip_enabled | Body | String | Kubernetes APIエンドポイントに公認ドメインアドレスを作成するかどうか("True" / "False") |


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
                "etcd_volume_size": "10",
                "external_network_id": "751b8227-7b45-440a-9349-dbf829d0aba5",
                "external_subnet_id_list": "59ddc195-76b1-431d-9693-f09880747dc6",
                "flavor_type": "core",
                "hypervisor_type": "qemu",
                "kube_tag": "v1.17.6",
                "kube_version_status": "NEED_UPGRADE",
                "login_username": "centos",
                "master_lb_floating_ip_enabled": "true",
                "node_image": "f462a2a5-ba24-46d6-b7a1-9a9febcd3cfc",
                "os_arch": "amd64",
                "os_distro": "CentOS",
                "os_type": "linux",
                "os_version": "7.8",
                "project_domain": "NORMAL",
                "server_group_meta": "k8s_2b778d83-8b67-45b1-920e-b0c5ad5c2f30_561c3f55-a23f-4e1a-b2fa-a5459b2c0575",
                "user_script": ""
            },
            "links": [
                {
                    "href": "https://kr2-api-kubernetes.infrastructure.cloud.toast.com/clusters/f0af4484-0a16-433a-a15c-295d9ba6537d",
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
| labels.boot_volume_type | Body | String | 基本ワーカーノードグループ適用：ブロックストレージの種類|
| labels.boot_volume_size | Body | String | 基本ワーカーノードグループ適用：ブロックストレージサイズ(GB) |
| labels.external_network_id | Body | String | インターネットゲートウェイに接続されたVPC network UUID |
| labels.external_subnet_id_list | Body | String | インターネットゲートウェイに接続されたサブネットUUIDリスト(コロンで区切る) |
| labels.cert_manager_api | Body | String | CSR(Certificate Signing Request)機能を有効にするかどうか。必ず"True"に設定 |
| labels.ca_enable | Body | String | 基本ワーカーノードグループ適用：オートスケーラー：機能を有効にするかどうか("True" / "False") |
| labels.ca_max_node_count | Body | String | 基本ワーカーノードグループ適用：オートスケーラー：最大ノード数 |
| labels.ca_min_node_count | Body | String | 基本ワーカーノードグループ適用：オートスケーラー：最小ノード数 |
| labels.ca_scale_down_enable | Body | String | 基本ワーカーノードグループ適用：オートスケーラー：縮小が有効かどうか("True" / "False") |
| labels.ca_scale_down_unneeded_time | Body | String | 基本ワーカーノードグループ適用：オートスケーラー：しきい値領域維持時間 |
| labels.ca_scale_down_util_thresh | Body | String | 基本ワーカーノードグループ適用：オートスケーラー：リソース使用量しきい値 |
| labels.ca_scale_down_delay_after_add | Body | String | 基本ワーカーノードグループ適用：オートスケーラー：増設後の縮小遅延時間 |
| labels.user_script | Body | String | 予約スクリプト |
| labels.master_lb_floating_ip_enabled | Body | String | Kubernetes APIエンドポイントに公認ドメインアドレスを作成するかどうか("True" / "False") |

<details><summary>例</summary>
<p>

```json
{
    "api_address": "https://2b778d83-alp-kr2-k8s.container.cloud.toast.com:6443",
    "cluster_template_id": "b4503d97-6012-499d-a31a-5200f94a7890",
    "coe_version": "v1.17.6",
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
        "etcd_volume_size": "10",
        "external_network_id": "751b8227-7b45-440a-9349-dbf829d0aba5",
        "external_subnet_id_list": "59ddc195-76b1-431d-9693-f09880747dc6",
        "flavor_type": "core",
        "hypervisor_type": "qemu",
        "kube_tag": "v1.17.6",
        "kube_version_status": "NEED_UPGRADE",
        "login_username": "centos",
        "master_lb_floating_ip_enabled": "true",
        "node_image": "f462a2a5-ba24-46d6-b7a1-9a9febcd3cfc",
        "os_arch": "amd64",
        "os_distro": "CentOS",
        "os_type": "linux",
        "os_version": "7.8",
        "project_domain": "NORMAL",
        "server_group_meta": "k8s_2b778d83-8b67-45b1-920e-b0c5ad5c2f30_561c3f55-a23f-4e1a-b2fa-a5459b2c0575",
        "user_script": ""
    },
    "links": [
        {
            "href": "https://kr2-api-kubernetes.infrastructure.cloud.toast.com/v1/clusters/2b778d83-8b67-45b1-920e-b0c5ad5c2f30",
            "rel": "self"
        },
        {
            "href": "https://kr2-api-kubernetes.infrastructure.cloud.toast.com/clusters/2b778d83-8b67-45b1-920e-b0c5ad5c2f30",
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
| labels.user_script | Body | String | X | 予約スクリプト |
| labels.master_lb_floating_ip_enabled | Body | String | O | Kubernetes APIエンドポイントに公認ドメインアドレスを作成するかどうか("True" / "False")<br>labels.external_network_idとexternal_subnet_id_listが設定されている場合にのみ"True"に設定可能 |
| flavor_id | Body | UUID | O | 基本ワーカーノードグループ適用：ノードインスタンスタイプUUID |
| fixed_network | Body | UUID | O | VPC Network UUID |
| fixed_subnet | Body | UUID | O | VPC Subnet UUID |

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
        "kube_tag": "v1.17.6",
        "master_lb_floating_ip_enabled": "true",
        "node_image": "f462a2a5-ba24-46d6-b7a1-9a9febcd3cfc",
        "user_script": ""
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
| labels.user_script | Body | String | 予約スクリプト |
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
            "href": "https://kr2-api-kubernetes.infrastructure.cloud.toast.com/v1/clusters/96742ac4-02e7-4b1d-a242-02876c0bd3f8/nodegroups/018b06c5-1293-4081-8242-167a1cb9f262",
            "rel": "self"
        },
        {
            "href": "https://kr2-api-kubernetes.infrastructure.cloud.toast.com/clusters/96742ac4-02e7-4b1d-a242-02876c0bd3f8/nodegroups/018b06c5-1293-4081-8242-167a1cb9f262",
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
| labels.ca_enable | Body | String | O | 基本ワーカーノードグループ適用：オートスケーラー：機能を有効にするかどうか("True" / "False") |
| labels.ca_max_node_count | Body | String | X | 基本ワーカーノードグループ適用：オートスケーラー：最大ノード数 |
| labels.ca_min_node_count | Body | String | X | 基本ワーカーノードグループ適用：オートスケーラー：最小ノード数 |
| labels.ca_scale_down_enable | Body | String | X | 基本ワーカーノードグループ適用：オートスケーラー：縮小が有効かどうか("True" / "False") |
| labels.ca_scale_down_unneeded_time | Body | String | X | 基本ワーカーノードグループ適用：オートスケーラー：しきい値領域維持時間 |
| labels.ca_scale_down_util_thresh | Body | String | X | 基本ワーカーノードグループ適用：オートスケーラー：リソース使用量しきい値 |
| labels.ca_scale_down_delay_after_add | Body | String | X | 基本ワーカーノードグループ適用：オートスケーラー：増設後の縮小遅延時間 |
| labels.user_script | Body | String | X | 予約スクリプト |
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
| labels.user_script | Body | String | 予約スクリプト |
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
        "user_script": ""
    },
    "links": [
        {
            "href": "https://kr2-api-kubernetes.infrastructure.cloud.toast.com/v1/clusters/96742ac4-02e7-4b1d-a242-02876c0bd3f8/nodegroups/a3366f2f-a1f3-45ef-8390-10536e8060ff",
            "rel": "self"
        },
        {
            "href": "https://kr2-api-kubernetes.infrastructure.cloud.toast.com/clusters/96742ac4-02e7-4b1d-a242-02876c0bd3f8/nodegroups/a3366f2f-a1f3-45ef-8390-10536e8060ff",
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

## その他機能

### サポートされるKubernetesバージョン表示

NHN Cloud NHN Kubernetes Service(NKS)でサポートするKubernetesバージョンを照会します。

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

<details><summary>例</summary>
<p>

```json
{
    "supported_k8s": {
        "v1.17.6": false,
        "v1.18.19": false,
        "v1.19.13": true,
        "v1.20.12": true,
        "v1.21.6": true,
        "v1.22.3": true
    }
}
```

</p>
</details>
