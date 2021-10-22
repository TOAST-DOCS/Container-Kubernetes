## Container > Kubernetes > API v2 가이드

Kubernetes 클러스터를 구성하는 API를 기술합니다.
API를 사용하려면 API 엔드포인트와 토큰 등이 필요합니다. [API 사용 준비](/Compute/Compute/ko/identity-api/)를 참고하여 API 사용에 필요한 정보를 준비합니다.

모든 API는 `kubernetes` 타입 엔드포인트를 이용해호출합니다.

| 타입 | 리전 | 엔드포인트 |
|---|---|---|
| kubernetes | 한국(판교) 리전<br>한국(평촌) 리전 | https://kr1-api-kubernetes.infrastructure.cloud.toast.com<br>kr2-api-kubernetes.infrastructure.cloud.toast.com |


API 응답에 가이드에 명시되지 않은 필드가 나타날 수 있습니다. 이런 필드는 NHN Cloud 내부 용도로 사용되며 사전 공지 없이 변경될 수 있으므로 사용하지 않습니다.

## 클러스터

### 클러스터 목록 보기

```
GET /v1/clusters
X-Auth-Token: {tokenId}
```

#### 요청
이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |

#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| clusters | Body | Array | 클러스터 정보 객체 목록 |
| clusters.uuid | Body | | 클러스터 UUID |
| clusters.name | Body | | 클러스터 이름 |
| clusters.node_count | Body | | 전체 워커 노드 수 |
| clusters.master_count | Body | | 마스터 노드 수 |
| clusters.labels | Body | Object | 클러스터 레이블 |
| clusters.labels.kube_tag | Body | | 마스터 노드 그룹 k8s 버전 |
| clusters.stack_id | Body | | 마스터 노드 그룹과 연결된 heat stack UUID |
| clusters.status | Body | | 클러스터 상태 |

<details><summary>예시</summary>
```json
{"clusters": [{"uuid": "c94ca30d-cd2d-43c3-8edb-cbb2c106b460", "name": "tw-202010", "cluster_template_id": "bac4c127-8f51-4951-8ff4-1f1e118536ae", "keypair": "tw-kr2-alpha", "node_count": 1, "master_count": 3, "docker_volume_size": null, "labels": {"availability_zone": "kr2-pub-b", "node_image": "cd299425-b2fd-46d4-a054-50b62356277c", "boot_volume_type": "General HDD", "boot_volume_size": "20", "master_lb_floating_ip_enabled": "true", "external_network_id": "751b8227-7b45-440a-9349-dbf829d0aba5", "external_subnet_id_list": "59ddc195-76b1-431d-9693-f09880747dc6", "cert_manager_api": "False", "clusterautoscale": "nodegroupfeature", "ca_scale_down_unneeded_time": "3", "ca_enable": "false", "ca_max_node_count": "10", "ca_scale_down_util_thresh": "50", "ca_scale_down_enable": "true", "ca_min_node_count": "1", "ca_scale_down_delay_after_add": "3", "kube_tag": "v1.17.6", "etcd_volume_size": "10", "flavor_type": "core", "os_distro": "CentOS", "hypervisor_type": "qemu", "os_arch": "amd64", "os_version": "7.8", "os_type": "linux", "login_username": "centos", "project_domain": "NORMAL", "server_group_meta": "k8s_c94ca30d-cd2d-43c3-8edb-cbb2c106b460_d9d742ae-ff99-4c15-a974-42f53f7f6645", "kube_version_status": "NEED_UPGRADE"}, "master_flavor_id": null, "flavor_id": "6ef27f21-c774-4c0e-84ff-7dd4a762571f", "create_timeout": 60, "links": [{"href": "http://10.162.148.141:9511/v1/clusters/c94ca30d-cd2d-43c3-8edb-cbb2c106b460", "rel": "self"}, {"href": "http://10.162.148.141:9511/clusters/c94ca30d-cd2d-43c3-8edb-cbb2c106b460", "rel": "bookmark"}], "stack_id": "653e9367-f1e6-4917-a5e5-456e2effe043", "status": "DELETE_FAILED", "health_status": null}, {"uuid": "2b778d83-8b67-45b1-920e-b0c5ad5c2f30", "name": "tw-cli", "cluster_template_id": "b4503d97-6012-499d-a31a-5200f94a7890", "keypair": "tw-kr2-alpha", "node_count": 1, "master_count": 3, "docker_volume_size": null, "labels": {"availability_zone": "kr2-pub-b", "node_image": "f462a2a5-ba24-46d6-b7a1-9a9febcd3cfc", "boot_volume_type": "General HDD", "boot_volume_size": "20", "master_lb_floating_ip_enabled": "true", "external_network_id": "751b8227-7b45-440a-9349-dbf829d0aba5", "external_subnet_id_list": "59ddc195-76b1-431d-9693-f09880747dc6", "cert_manager_api": "True", "clusterautoscale": "nodegroupfeature", "ca_scale_down_unneeded_time": "3", "ca_enable": "false", "ca_max_node_count": "10", "ca_scale_down_util_thresh": "50", "ca_scale_down_enable": "true", "ca_min_node_count": "1", "ca_scale_down_delay_after_add": "3", "kube_tag": "v1.17.6", "user_script": "#!/bin/python3\n\nimport os\n\nprint(\"haha this is python. cwd is {}\".format(os.getcwd()))", "etcd_volume_size": "10", "flavor_type": "core", "os_distro": "CentOS", "hypervisor_type": "qemu", "os_arch": "amd64", "os_version": "7.8", "os_type": "linux", "login_username": "centos", "project_domain": "NORMAL", "server_group_meta": "k8s_2b778d83-8b67-45b1-920e-b0c5ad5c2f30_561c3f55-a23f-4e1a-b2fa-a5459b2c0575", "kube_version_status": "NEED_UPGRADE"}, "master_flavor_id": null, "flavor_id": "6ef27f21-c774-4c0e-84ff-7dd4a762571f", "create_timeout": 60, "links": [{"href": "http://10.162.148.141:9511/v1/clusters/2b778d83-8b67-45b1-920e-b0c5ad5c2f30", "rel": "self"}, {"href": "http://10.162.148.141:9511/clusters/2b778d83-8b67-45b1-920e-b0c5ad5c2f30", "rel": "bookmark"}], "stack_id": "7f497472-9729-4b89-9124-1c097335b856", "status": "CREATE_COMPLETE", "health_status": null}]}
```
</details>


### 클러스터 보기

```
GET /v1/clusters/{CLUSTER_ID_OR_NAME}
X-Auth-Token: {tokenId}
```

#### 요청
이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| CLUSTER_ID_OR_NAME| URL | UUID or String | O | 클러스터 id 또는 클러스터 name |

#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| uuid | Body | UUID | 클러스터 UUID |
| name | Body | String | 클러스터 이름 |
| node_count | Body | Integer | 전체 워커 노드 수 |
| master_count | Body | Integer | 마스터 노드 수 |
| labels | Body | Object | 클러스터 레이블 |
| labels.kube_tag  | Body | String | 마스터 노드 그룹 k8s 버전 |
| stack_id | Body | UUID | 마스터 노드 그룹과 연결된 heat stack UUID |
| status | Body | String | 클러스터 상태 |
| status_reason | Body | String | 클러스터 상태 이유(null 가능) |
| discovery_url | Body | String | ETCD discovery 시 사용 가능한 URL |
| api_address | Body | String | Kubernetes API 엔드포인트 |
| project_id | Body | String | 프로젝트(=테넌트) ID |
| node_addresses | Body | String List | 워커 노드 IP 주소 목록 |
| master_addresses | Body | String List | 마스터 노드 IP 주소 목록 |
| fixed_network | Body | UUID | VPC UUID|
| fixed_subnet | Body | UUID | VPC Subnet UUID |
| created_at | Body | String | 생성 시간(UTC) |
| updated_at | Body | String | 최근 업데이트 시간(UTC) |

<details><summary>예시</summary>
```json
{"uuid": "2b778d83-8b67-45b1-920e-b0c5ad5c2f30", "name": "tw-cli", "cluster_template_id": "b4503d97-6012-499d-a31a-5200f94a7890", "keypair": "tw-kr2-alpha", "node_count": 1, "master_count": 3, "docker_volume_size": null, "labels": {"availability_zone": "kr2-pub-b", "node_image": "f462a2a5-ba24-46d6-b7a1-9a9febcd3cfc", "boot_volume_type": "General HDD", "boot_volume_size": "20", "master_lb_floating_ip_enabled": "true", "external_network_id": "751b8227-7b45-440a-9349-dbf829d0aba5", "external_subnet_id_list": "59ddc195-76b1-431d-9693-f09880747dc6", "cert_manager_api": "True", "clusterautoscale": "nodegroupfeature", "ca_scale_down_unneeded_time": "3", "ca_enable": "false", "ca_max_node_count": "10", "ca_scale_down_util_thresh": "50", "ca_scale_down_enable": "true", "ca_min_node_count": "1", "ca_scale_down_delay_after_add": "3", "kube_tag": "v1.17.6", "user_script": "#!/bin/python3\n\nimport os\n\nprint(\"haha this is python. cwd is {}\".format(os.getcwd()))", "etcd_volume_size": "10", "flavor_type": "core", "os_distro": "CentOS", "hypervisor_type": "qemu", "os_arch": "amd64", "os_version": "7.8", "os_type": "linux", "login_username": "centos", "project_domain": "NORMAL", "server_group_meta": "k8s_2b778d83-8b67-45b1-920e-b0c5ad5c2f30_561c3f55-a23f-4e1a-b2fa-a5459b2c0575", "kube_version_status": "NEED_UPGRADE"}, "master_flavor_id": null, "flavor_id": "6ef27f21-c774-4c0e-84ff-7dd4a762571f", "create_timeout": 60, "links": [{"href": "http://10.162.148.141:9511/v1/clusters/2b778d83-8b67-45b1-920e-b0c5ad5c2f30", "rel": "self"}, {"href": "http://10.162.148.141:9511/clusters/2b778d83-8b67-45b1-920e-b0c5ad5c2f30", "rel": "bookmark"}], "stack_id": "7f497472-9729-4b89-9124-1c097335b856", "status": "CREATE_COMPLETE", "status_reason": null, "health_status": null, "health_status_reason": {"api": "Networking Error...Check network reachability from magnum-conductor host to the cluster api server with 'telnet 2b778d83-alp-kr2-k8s.container.cloud.toast.com 6443'"}, "discovery_url": "http://169.254.169.248/1b3a4e306aa9ec896aef230c799d14c1", "api_address": "https://2b778d83-alp-kr2-k8s.container.cloud.toast.com:6443", "coe_version": "v1.17.6", "container_version": "1.12.6", "project_id": "1ffeaca9bbf94ab1aa9cffdec29a258a", "user_id": "12ba32bebc414c4992a2c9be3952a64c", "node_addresses": ["192.168.0.5"], "master_addresses": ["192.168.0.22", "192.168.0.12", "192.168.0.10"], "fixed_network": "eb212079-b6ec-430c-ba57-14280a457bcb", "fixed_subnet": "4fdf5b80-3d35-43f5-a5c1-010a3b6c8e90", "floating_ip_enabled": false, "created_at": "2021-08-05T01:48:39+00:00", "updated_at": "2021-08-05T04:39:49+00:00"}
```
</details>
---

### 클러스터 생성하기

```
POST /v1/clusters
X-Auth-Token: {tokenId}
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| cluster_template_id | Body | String | O | 반드시 "iaas_console" 로 설정되어야 함 |
| create_timeout | Body | Integer | O | 생성 타임아웃, 반드시 60으로 설정 |
| discovery_url | Body | String | O | ETCD discoverty URL, 반드시 null로 설정 |
| keypair | Body | String | O | 기본 워커 노드 그룹에 적용할 keypair |
| master_count | Body | String | O | 마스터 노드 수, 이 값을 따르지 않으므로 아무 값(예:1)으로 설정 |
| name | Body | String | O | 클러스터 이름 |
| node_count | Body | String | O | 기본 워커 노드 그룹에 적용할 노드 수 |
| labels | Body | Object | O | 클러스터 생성 정보 개체 |
| labels.availability_zone | Body | String | O | 기본 워커 노드 그룹 적용 : 가용성 영역 |
| labels.node_image | Body | UUID | O | 기본 워커 노드 그룹 적용 : 베이스 이미지 uuid |
| labels.boot_volume_type | Body | String | O | 기본 워커 노드 그룹 적용 : 블록 스토리지 종류|
| labels.boot_volume_size | Body | String | O | 기본 워커 노드 그룹 적용 : 블록 스토리지 사이즈(GB) |
| labels.external_network_id | Body | String | O | 인터넷 게이트웨이쪽 VPC network UUID |
| labels.external_subnet_id_list | Body | String | O | 인터넷 게이트웨이쪽 subnet UUID |
| labels.cert_manager_api | Body | String | O | CSR(Certificate Signing Request) 기능 활성화 여부. 반드시 "True" 로 설정 |
| labels.clusterautoscale | Body | String | O | 반드시 "nodegroupfeature"로 설정 
| labels.ca_enable | Body | String | O | 기본 워커 노드 그룹 적용 : 오토스케일러: 기능 활성화 여부 ("True" / "False") |
| labels.ca_max_node_count | Body | String | O | 기본 워커 노드 그룹 적용 : 오토스케일러: 최대 노드 수 |
| labels.ca_min_node_count | Body | String | O | 기본 워커 노드 그룹 적용 : 오토스케일러: 최소 노드 수 |
| labels.ca_scale_down_enable | Body | String | O | 기본 워커 노드 그룹 적용 : 오토스케일러: 감축 활성 여부 ("True" / "False") |
| labels.ca_scale_down_unneeded_time | Body | String | O | 기본 워커 노드 그룹 적용 : 오토스케일러: 임계 영역 유지 시간 |
| labels.ca_scale_down_util_thresh | Body | String | O | 기본 워커 노드 그룹 적용 : 오토스케일러: 리소스 사용량 임계치  |
| labels.ca_scale_down_delay_after_add | Body | String | O | 기본 워커 노드 그룹 적용 : 오토스케일러: 증설 후 감축 지연 시간 |
| labels.kube_tag | Body | String | O | 클러스터 버전 |
| labels.user_script | Body | String | X | 예약 스크립트 |
| flavor_id | Body | UUID | O | 기본 워커 노드 그룹 적용: 노드 flavor UUID |
| fixed_network | Body | UUID | O | VPC Network UUID |
| fixed_subnet | Body | UUID | O | VPC Subnet UUID |


<details><summary>예시</summary>

```json
{"cluster_template_id": "iaas_console", "create_timeout": 60, "discovery_url": null, "keypair": "tw-kr2-alpha", "master_count": 1, "name": "tw-cli-test", "node_count": 1, "labels": {"availability_zone": "kr2-pub-b", "node_image": "f462a2a5-ba24-46d6-b7a1-9a9febcd3cfc", "boot_volume_type": "General HDD", "boot_volume_size": "20", "master_lb_floating_ip_enabled": "true", "external_network_id": "751b8227-7b45-440a-9349-dbf829d0aba5", "external_subnet_id_list": "59ddc195-76b1-431d-9693-f09880747dc6", "cert_manager_api": "True", "clusterautoscale": "nodegroupfeature", "ca_scale_down_unneeded_time": "3", "ca_enable": "false", "ca_max_node_count": "10", "ca_scale_down_util_thresh": "50", "ca_scale_down_enable": "true", "ca_min_node_count": "1", "ca_scale_down_delay_after_add": "3", "kube_tag": "v1.17.6", "user_script": "#!/bin/python3\n\nimport os\n\nprint(\"haha this is python. cwd is {}\".format(os.getcwd()))"}, "flavor_id": "6ef27f21-c774-4c0e-84ff-7dd4a762571f", "fixed_network": "eb212079-b6ec-430c-ba57-14280a457bcb", "fixed_subnet": "4fdf5b80-3d35-43f5-a5c1-010a3b6c8e90"}
```
</details>

#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| uuid | Body | UUID | 클러스터 ID |

<details><summary>예시</summary>

```json
{"uuid": "5801ef8a-3760-4858-b467-fc4c1201241d"}

```
</details>

---
### 클러스터 삭제하기

```
DELETE /v1/clusters/{CLUSTER_ID_OR_NAME}
X-Auth-Token: {tokenId}
```

#### 요청
이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |


#### 응답
이 API는 응답 본문을 반환하지 않습니다.

### 리사이즈
```
POST /v1/clusters/{CLUSTER_ID_OR_NAME}/actions/resize
X-Auth-Token: {tokenId}
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| nodegroup | Body | UUID | O | 대상 워커 노드 그룹 이름 / UUID |
| node_count | Body | Integer | O | 변경하고자 하는 워커 노드 수 |
| nodes_to_remove | Body | String List | X | 삭제하고자 하는 노드 UUID |

* 주의 사항
  * 노드를 감축하는 경우(즉, 일부 노드 삭제) 삭제할 노드를 지정하는 경우 `nodes_to_remove`를 설정해야 한다. 삭제할 노드를 지정하지 않는 경우 삭제 대상 노드는 무작위로 선택된다. 
  * node_count 최소 1, 최대 10(단, 최대값은 quota로 조정 가능)

<details><summary>증설예시</summary>
<p>

```json
{"node_count": 3, "nodegroup": "default-worker"}
```

</p>
</details>

<details><summary>감축예시</summary>
<p>

```json
{"node_count": 1, "nodes_to_remove": ["593e4aee-697f-4808-aa5b-d3c8703795ff", "ce2e2d2a-ddf5-41da-a338-72e7f5237088"], "nodegroup": "default-worker"}
```

</p>
</details>



#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| uuid | Body | String | 대상 클러스터 UUID|

<details><summary>예시</summary>
<p>

```json
{"uuid": "5bac7acd-58b7-4cf5-95f5-a25d67da13a2"}

```

</p>
</details>


## 노드 그룹
### 노드 그룹 목록 보기

```
GET /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups
X-Auth-Token: {tokenId}
```

#### 요청
이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | - | 클러스터 ID 또는 클러스터 이름 | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | - | 노드그룹 ID 또는 노드그룹 이름 | 


#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| nodegroups | Body | Array | 노드그룹 정보 객체 목록 |
| nodegroups.id | Body | UUID | 노드그룹 ID |

<details><summary>예시</summary>
<p>

```json
```

</p>
</details>


### 노드그룹 보기

```
GET /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}
X-Auth-Token: {tokenId}
```

#### 요청
이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | - | 클러스터 ID 또는 클러스터 이름 | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | - | 노드그룹 ID 또는 노드그룹 이름 | 


#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| nodegroup | Body | Object | 노드그룹 정보 객체 |
| nodegroup.default_pool_id | Body | UUID | 노드그룹에 등록된 풀 ID |

<details><summary>예시</summary>
<p>

```json
```

</p>
</details>



---
### 노드그룹 생성하기

```
POST /v1/clusters/nodegroups
X-Auth-Token: {tokenId}
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| nodegroup | Body | Object | O | 노드그룹 정보 객체 |
| nodegroup.protocol | Body | Enum | O | 노드그룹 프로토콜<br>`TCP`, `HTTP`,`HTTPS`, `TERMINATED_HTTPS` 중 하나 |
| nodegroup.description | Body | String | - | 노드그룹 설명 |
| nodegroup.name | Body | String | - | 노드그룹 이름 |
| nodegroup.cluster_id | Body | UUID | O | 클러스터 ID |
| nodegroup.admin_state_up | Body | Boolean | - | 관리자 제어 상태 |
| nodegroup.connection_limit | Body |  Integer | - | 노드그룹의 connection limit |
| nodegroup.keepalive_timeout | Body | Integer | - | 노드그룹의 keepalive timeout |
| nodegroup.default_tls_container_ref | Body | String | - | key-manager에 등록된 TLS 인증서 경로 |
| nodegroup.sni_container_refs | Body | Array | - | key-manager에 등록된 SNI 인증서 경로 목록 |
| nodegroup.protocol_port | Body | Integer | O | 노드그룹 포트 |


<details><summary>예시</summary>
<p>

```json
```
</p>
</details>

#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| nodegroup | Body | Object | 노드그룹 정보 객체 |
| nodegroup.default_pool_id | Body | UUID | 노드그룹에 등록된 풀 ID |
| nodegroup.protocol | Body | Enum | 노드그룹의 프로토콜<br>`TCP`, `HTTP`,`HTTPS`, `TERMINATED_HTTPS` 중 하나 |
| nodegroup.description | Body | String | 노드그룹 설명 |
| nodegroup.name | Body | String | 노드그룹 이름 |
| nodegroup.clusters | Body | Array | 노드그룹가 등록된 클러스터 객체 목록 |
| nodegroup.clusters.id | Body | UUID | 클러스터 ID |
| nodegroup.project_id | Body | String | 테넌트 ID |
| nodegroup.admin_state_up | Body | Boolean | 관리자 제어 상태 |
| nodegroup.connection_limit | Body | Integer | 노드그룹의 connection limit |
| nodegroup.keepalive_timeout | Body | Integer | 노드그룹의 keepalive timeout |
| nodegroup.default_tls_container_ref | Body | String | key-manager에 등록된 TLS 인증서 경로 |
| nodegroup.sni_container_refs | Body | Array | key-manager에 등록된 SNI 인증서 경로 목록 |
| nodegroup.protocol_port | Body | Integer | 노드그룹 포트 |
| nodegroup.id | Body | UUID | 노드그룹 ID |


<details><summary>예시</summary>
<p>

```json
```
</p>
</details>

---
### 노드그룹 삭제하기
지정한 노드그룹를 삭제합니다.
```
DELETE /v1/clusters/nodegroups/{nodegroupId}
X-Auth-Token: {tokenId}
```

#### 요청
이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| nodegroupId | URL | UUID | O | 노드그룹 ID |

#### 응답

이 API는 응답 본문을 반환하지 않습니다.




### 노드그룹 클러스터 오토 스케일러 설정 변경

### 노드그룹 업그레이드


