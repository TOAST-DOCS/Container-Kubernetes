## Container > NHN Kubernetes Service(NKS) > API v2 가이드

Kubernetes 클러스터를 구성하기 위한 API를 기술합니다.
API를 사용하려면 API 엔드포인트와 토큰 등이 필요합니다. [API 사용 준비](/Compute/Compute/ko/identity-api-gov/)를 참고하여 API 사용에 필요한 정보를 준비합니다.

모든 API는 `kubernetes` 타입 엔드포인트를 이용해 호출합니다.

| 타입 | 리전 | 엔드포인트 |
|---|---|---|
| kubernetes | 한국(판교) 리전| https://kr1-api-kubernetes-infrastructure.gov-nhncloudservice.com |
| kubernetes | 한국(평촌) 리전| https://kr2-api-kubernetes-infrastructure.gov-nhncloudservice.com |


API 응답에 가이드에 명시되지 않은 필드가 나타날 수 있습니다. 이런 필드는 NHN Cloud 내부 용도로 사용되며 사전 공지 없이 변경될 수 있으므로 사용하지 않습니다.

## API에 사용되는 리소스 정보 확인

NHN Kubernetes Service(NKS) API는 클러스터 및 노드 그룹 구성을 위해 여러 가지 리소스를 사용합니다. 리소스별 정보 확인 방법은 다음과 같습니다.

### 인터넷 게이트웨이에 연결된 VPC 네트워크 UUID

인터넷 게이트웨이에 연결된 VPC 네트워크는 VPC 네트워크 목록 조회 API에 **router:external=True** 쿼리 파라미터를 이용해 조회할 수 있습니다.

```
GET /v2.0/networks?router:external=True
```

네트워크 목록 조회 API에 대한 좀 더 자세한 내용은 [네트워크 목록 보기](/Network/VPC/ko/public-api-gov/#_2)를 참고하세요.


### 인터넷 게이트웨이에 연결된 서브넷 UUID 목록

인터넷 게이트웨이에 연결된 VPC 네트워크와 연결된 서브넷 UUID를 입력합니다. 여러 서브넷이 조회됐다면 콜론(`:`)으로 연결해 입력합니다. 서브넷 목록 조회 API에 대한 좀 더 자세한 내용은 [서브넷 목록 보기](/Network/VPC/ko/public-api-gov/#_6)를 참고하세요.


### VPC 네트워크 UUID

노드와 연결할 내부 VPC 네트워크 UUID를 입력합니다. 네트워크 목록 조회 API에 대한 좀 더 자세한 내용은 [네트워크 목록 보기](/Network/VPC/ko/public-api-gov/#_2)를 참고하세요.

### VPC 서브넷 UUID

노드와 연결할 내부 VPC 네트워크와 연결된 서브넷 UUID를 입력합니다. 서브넷 목록 조회 API에 대한 좀 더 자세한 내용은 [서브넷 목록 보기](/Network/VPC/ko/public-api-gov/#_6)를 참고하세요.

### 가용성 영역 UUID

노드를 생성할 가용성 영역 UUID를 입력합니다. 가용성 영역 목록 조회 API에 대한 좀 더 자세한 내용은 [가용성 목록 보기](/Compute/Instance/ko/public-api-gov/#_9)를 참고하세요.

### 키페어 UUID

노드 접속 시 사용할 키페어를 입력합니다. 키페어 목록 조회 API에 대한 좀 더 자세한 내용은 [키페어 목록 보기](/Compute/Instance/ko/public-api-gov/#_13)를 참고하세요.

### 베이스 이미지 UUID

노드 생성에 사용할 베이스 이미지 UUID를 입력합니다. NKS 노드 생성에 사용되는 베이스 이미지만을 필터링하기 위해 API 호출 시 쿼리 스트링 파라미터에 `nhncloud_allow_nks_cpu_flavor=true&visibility=public` 값을 입력합니다. 베이스 이미지 목록 조회 API에 대한 좀 더 자세한 내용은 [이미지 목록 조회](/Compute/Image/ko/public-api-gov/#_2)를 참고하세요.

### 블록 스토리지 종류

노드에 사용할 블록 스토리지 UUID를 입력합니다. 블록 스토리지 타입 목록 조회 API에 대한 좀 더 자세한 내용은 [볼륨 타입 목록 보기](/Storage/Block%20Storage/ko/public-api-gov/#_2)를 참고하세요.

### 인스턴스 타입 UUID

생성할 노드의 인스턴스 타입 UUID를 입력합니다. 인스턴스 타입 목록 조회 API에 대한 좀 더 자세한 내용은 [인스턴스 타입 목록 보기](/Compute/Instance/ko/public-api-gov/#_2)를 참고하세요.



## 클러스터

### 클러스터 목록 보기

클러스터 목록을 조회합니다.

```
GET /v1/clusters
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
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
| clusters.uuid | Body | UUID | 클러스터 UUID |
| clusters.name | Body | String | 클러스터 이름 |
| clusters.flavor_id | Body | UUID | 기본 워커 노드의 인스턴스 타입 UUID|
| clusters.keypair | Body | UUID | 기본 워커 노드 그룹에 적용된 키페어 UUID |
| clusters.node_count | Body | Integer| 전체 워커 노드 수 |
| clusters.stack_id | Body | UUID | 컨트롤 플레인과 연결된 heat stack UUID |
| clusters.status | Body | String | 클러스터 작업 상태 |
| clusters.status_reason | Body | String | 클러스터 작업 상태 이유(null 가능) |
| clusters.health_status | Body | String | 클러스터의 k8s API 및 k8s 노드 상태 정보의 유효성. <br>* `FRESH`: k8s API 및 k8s 노드 상태 정보가 유효함 <br>* `STALE`: 일정 시간 동안 k8s API 및 노드 상태 정보가 업데이트되지 않아 정보의 유효성이 떨어짐 <br>* `ROTTEN`: 오랜 시간 동안 k8s API 및 노드 상태 정보가 업데이트되지 않아 정보를 신뢰할 수 없음 |
| clusters.health_status_reason | Body | Object | 클러스터의 k8s API 및 워커 노드 그룹별 k8s 노드 상태의 상세 정보가 담긴 객체 |
| clusters.health_status_reason.timestamp | Body | String | 클러스터 k8s API 및 k8s 노드 상태 정보의 갱신 시각(UTC) |
| clusters.health_status_reason.cluster.api_status | Body | String | k8s API의 상태 정보의 통계. <br>* `NORMAL`: k8s API 상태가 정상 <br>* `STALED_DATA`: 일정 시간 동안 k8s API 상태 정보가 업데이트되지 않아 정보의 유효성이 떨어짐 <br>* `ROTTEN_DATA`: 오랜 시간 동안 k8s API 상태 정보가 업데이트되지 않아 정보를 신뢰할 수 없음 <br>* `K8S_API_NOT_WORKING`: k8s API 상태가 비정상 |
| clusters.health_status_reason.api | Body | String | k8s API의 상태 정보. <br>* `OK`: k8s API 상태가 정상 <br>* `NOT_OK`: k8s API 상태가 비정상 |
| clusters.health_status_reason.cluster.node_status | Body | String | 모든 워커 노드 그룹의 k8s 노드 상태 정보의 통계. <br>* `NORMAL`: 모든 k8s 노드가 Ready 상태 <br>* `STALED_DATA`: 일정 시간 동안 k8s 노드 상태 정보가 업데이트되지 않아 정보의 유효성이 떨어짐 <br>* `ROTTEN_DATA`: 오랜 시간 동안 k8s API 상태 정보가 업데이트되지 않아 정보를 신뢰할 수 없음 <br>* `NOT_READY_NODE_EXIST`: 클러스터에 Not Ready 상태의 k8s 노드가 존재 <br>* `ALL_NODES_NOT_READY`: 클러스터의 모든 k8s 노드 상태가 Not Ready |
| clusters.health_status_reason.nodegroup.node_status.{WORKER_NODEGROUP_NAME} | Body | String | 특정 워커 노드 그룹의 k8s 노드 상태 정보의 통계. <br>* `NORMAL`: 해당 워커 노드 그룹의 모든 k8s 노드가 Ready 상태 <br>* `STALED_DATA`: 일정 시간 동안 k8s 노드 상태 정보가 업데이트되지 않아 정보의 유효성이 떨어짐 <br>* `ROTTEN_DATA`: 오랜 시간 동안 k8s API 상태 정보가 업데이트되지 않아 정보를 신뢰할 수 없음 <br>* `NOT_READY_NODE_EXIST`: 해당 워커 노드 그룹에 Not Ready 상태의 k8s 노드가 존재 <br>* `ALL_NODES_NOT_READY`: 해당 워커 노드 그룹의 모든 k8s 노드 상태가 Not Ready |
| clusters.health_status_reason.nodegroup-stats.{WORKER_NODEGROUP_NAME} | Body | String | 특정 워커 노드 그룹의 k8s 노드 상태 정보. {Ready Node의 수}:{Not Ready Node의 수}를 의미 |
| clusters.labels | Body | Object | 클러스터 레이블 |
| clusters.labels.kube_tag | Body |String | 컨트롤 플레인 Kubernetes 버전 |
| clusters.labels.availability_zone | Body | String | 기본 워커 노드 그룹 적용 : 가용성 영역 |
| clusters.labels.node_image | Body | UUID | 기본 워커 노드 그룹 적용 : 베이스 이미지 UUID |
| clusters.labels.external_network_id | Body | String | 인터넷 게이트웨이에 연결된 VPC 네트워크 UUID |
| clusters.labels.external_subnet_id_list | Body | String | 인터넷 게이트웨이에 연결된 서브넷 UUID 목록(콜론으로 구분) |
| clusters.labels.cert_manager_api | Body | String | CSR(Certificate Signing Request) 기능 활성화 여부. 반드시 "True" 로 설정 |
| clusters.labels.master_lb_floating_ip_enabled | Body | String | Kubernetes API 엔드포인트에 공인 도메인 주소 생성 여부 ("True" / "False") |
| clusters.labels.strict_sg_rules | Body | String | 워커 노드 보안 그룹에 필수 보안 규칙만 생성("True" / "False"), (2024.03.05. 이후에 생성된 클러스터에서 확인 가능) |
| clusters.labels.additional_network_id_list | Body | String | 기본 워커 노드 그룹 적용: 추가 네트워크의 VPC 네트워크 UUID 목록(콜론으로 구분) |
| clusters.labels.additional_subnet_id_list | Body | String | 기본 워커 노드 그룹 적용: 추가 네트워크의 VPC 서브넷 UUID 목록(콜론으로 구분) |
| clusters.labels.cni_driver | Body | String | 클러스터 CNI(2023.03.31. 이후에 생성된 클러스터에서 확인 가능) |
| clusters.labels.service_cluster_ip_range | Body | String | K8s 서비스 네트워크, 클러스터에서 서비스 생성 시 ClusterIP에 할당되는 IP 대역(2023.05.30. 이후에 생성된 클러스터에서 확인 가능) |
| clusters.labels.pods_network_cidr | Body | String | 클러스터 파드 네트워크(2023.05.30. 이후에 생성된 클러스터에서 확인 가능) |
| clusters.labels.pods_network_subnet | Body | String | 클러스터 파드 서브넷 크기(2023.05.30. 이후에 생성된 클러스터에서 확인 가능) |
| clusters.labels.term_of_validity | Body | String | 인증서의 유효 기간 |
| clusters.labels.certificate_expiry | Body | String | 인증서 만료 일자 | 

<details><summary>예시</summary>
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
                    "href": "https://kr1-api-kubernetes-infrastructure.gov-nhncloudservice.com/v1/clusters/f0af4484-0a16-433a-a15c-295d9ba6537d",
                    "rel": "self"
                },
                {
                    "href": "https://kr1-api-kubernetes-infrastructure.gov-nhncloudservice.com/clusters/f0af4484-0a16-433a-a15c-295d9ba6537d",
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

### 클러스터 보기

개별 클러스터 정보를 조회합니다.

```
GET /v1/clusters/{CLUSTER_ID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| CLUSTER_ID_OR_NAME| URL | UUID or String | O | 클러스터 UUID 또는 클러스터 이름 |

#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| uuid | Body | UUID | 클러스터 UUID |
| name | Body | String | 클러스터 이름 |
| flavor_id | Body | UUID | 기본 워커 노드의 인스턴스 타입 UUID|
| keypair | Body | UUID | 기본 워커 노드 그룹에 적용된 키페어 UUID |
| node_count | Body | Integer| 전체 워커 노드 수 |
| stack_id | Body | UUID | 컨트롤 플레인과 연결된 heat stack UUID |
| status | Body | String | 클러스터 작업 상태 |
| status_reason | Body | String | 클러스터 작업 상태 이유(null 가능) |
| health_status | Body | String | 클러스터의 k8s API 및 k8s 노드 상태 정보의 유효성. <br> "FRESH": k8s API 및 k8s 노드 상태 정보가 유효함 <br> "STALE": 일정 시간동안 k8s API 및 노드 상태 정보 업데이트가 되지 않아 정보의 유효성이 떨어짐 <br> "ROTTEN": 오랜 시간동안 k8s API 및 노드 상태 정보 업데이트가 되지 않아 정보를 신뢰할 수 없음 |
| health_status_reason | Body | Object | 클러스터의 k8s API 및 워커 노드 그룹 별 k8s 노드 상태의 상세 정보가 담긴 객체 |
| health_status_reason.timestamp | Body | String | 클러스터 k8s API 및 k8s 노드 상태 정보의 갱신 시각(UTC) |
| health_status_reason.cluster.api_status | Body | String | k8s API의 상태 정보의 통계. <br> "NORMAL": k8s API 상태가 정상 <br> "STALED_DATA": 일정 시간동안 k8s API 상태 정보 업데이트가 되지 않아 정보의 유효성이 떨어짐 <br> "ROTTEN_DATA": 오랜 시간동안 k8s API 상태 정보 업데이트가 되지 않아 정보를 신뢰할 수 없음 <br> "K8S_API_NOT_WORKING": k8s API 상태가 비정상 |
| health_status_reason.api | Body | String | k8s API의 상태 정보. <br> "OK": k8s API 상태가 정상 <br> "NOT_OK": k8s API 상태가 비정상 |
| health_status_reason.cluster.node_status | Body | String | 모든 워커 노드 그룹의 k8s 노드 상태 정보의 통계. <br> "NORMAL": 모든 k8s 노드가 Ready 상태 <br> "STALED_DATA": 일정 시간동안 k8s 노드 상태 정보 업데이트가 되지 않아 정보의 유효성이 떨어짐 <br> "ROTTEN_DATA": 오랜 시간동안 k8s API 상태 정보 업데이트가 되지 않아 정보를 신뢰할 수 없음 <br> "NOT_READY_NODE_EXIST": 클러스터에 Not Ready 상태의 k8s 노드가 존재 <br> "ALL_NODES_NOT_READY": 클러스터의 모든 k8s 노드 상태가 Not Ready  |
| health_status_reason.nodegroup.node_status.{WORKER_NODEGROUP_NAME} | Body | String | 특정 워커 노드 그룹의 k8s 노드 상태 정보의 통계. <br> "NORMAL": 해당 워커 노드 그룹의 모든 k8s 노드가 Ready 상태 <br> "STALED_DATA": 일정 시간동안 k8s 노드 상태 정보 업데이트가 되지 않아 정보의 유효성이 떨어짐 <br> "ROTTEN_DATA": 오랜 시간동안 k8s API 상태 정보 업데이트가 되지 않아 정보를 신뢰할 수 없음 <br> "NOT_READY_NODE_EXIST": 해당 워커 노드 그룹에 Not Ready 상태의 k8s 노드가 존재 <br> "ALL_NODES_NOT_READY": 해당 워커 노드 그룹의 모든 k8s 노드 상태가 Not Ready |
| health_status_reason.nodegroup-stats.{WORKER_NODEGROUP_NAME} | Body | String | 특정 워커 노드 그룹의 k8s 노드 상태 정보. {Ready Node의 수}:{Not Ready Node의 수}를 의미 |
| api_address | Body | String | Kubernetes API 엔드포인트 |
| project_id | Body | String | 프로젝트(테넌트) ID |
| fixed_network | Body | UUID | VPC UUID|
| fixed_subnet | Body | UUID | VPC 서브넷 UUID |
| node_addresses | Body | String List | 워커 노드 IP 주소 목록 |
| created_at | Body | String | 생성 시간(UTC) |
| updated_at | Body | String | 최근 업데이트 시간(UTC) |
| labels | Body | Object | 클러스터 레이블 |
| labels.kube_tag | Body |String | 컨트롤 플레인 Kubernetes 버전 |
| labels.availability_zone | Body | String | 기본 워커 노드 그룹 적용 : 가용성 영역 |
| labels.node_image | Body | UUID | 기본 워커 노드 그룹 적용 : 베이스 이미지 UUID |
| labels.external_network_id | Body | String | 인터넷 게이트웨이에 연결된 VPC 네트워크 UUID |
| labels.external_subnet_id_list | Body | String | 인터넷 게이트웨이에 연결된 서브넷 UUID 목록(콜론으로 구분) |
| labels.cert_manager_api | Body | String | CSR(Certificate Signing Request) 기능 활성화 여부. 반드시 "True" 로 설정 |
| labels.master_lb_floating_ip_enabled | Body | String | Kubernetes API 엔드포인트에 공인 도메인 주소 생성 여부 ("True" / "False") |
| labels.strict_sg_rules | Body | String | 워커 노드 보안 그룹에 필수 보안 규칙만 생성("True" / "False"), (2024.03.05. 이후에 생성된 클러스터에서 확인 가능) |
| labels.additional_network_id_list | Body | String | 기본 워커 노드 그룹 적용: 추가 네트워크의 VPC 네트워크 UUID 목록(콜론으로 구분) |
| labels.additional_subnet_id_list | Body | String | 기본 워커 노드 그룹 적용: 추가 네트워크의 VPC 서브넷 UUID 목록(콜론으로 구분) |
| labels.cni_driver | Body | String | 클러스터 CNI(2023.03.31. 이후에 생성된 클러스터에서 확인 가능) |
| labels.service_cluster_ip_range | Body | String | K8s 서비스 네트워크, 클러스터에서 서비스 생성 시 ClusterIP에 할당되는 IP 대역(2023.05.30. 이후에 생성된 클러스터에서 확인 가능) |
| labels.pods_network_cidr | Body | String | 클러스터 파드 네트워크(2023.05.30. 이후에 생성된 클러스터에서 확인 가능) |
| labels.pods_network_subnet | Body | String | 클러스터 파드 서브넷 크기(2023.05.30. 이후에 생성된 클러스터에서 확인 가능) |
| labels.term_of_validity | Body | String | 인증서의 유효 기간 |
| labels.certificate_expiry | Body | String | 인증서 만료 일자 | 

<details><summary>예시</summary>
<p>

```json
{
    "api_address": "https://2b778d83-kr2-k8s.container.cloud.toast.com:6443",
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
            "href": "https://kr1-api-kubernetes-infrastructure.gov-nhncloudservice.com/v1/clusters/2b778d83-8b67-45b1-920e-b0c5ad5c2f30",
            "rel": "self"
        },
        {
            "href": "https://kr1-api-kubernetes-infrastructure.gov-nhncloudservice.com/clusters/2b778d83-8b67-45b1-920e-b0c5ad5c2f30",
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

### 작업 이력 목록 보기

클러스터의 작업 이력 목록을 조회합니다.

```
GET /v1/clusters/{CLUSTER_UUID}/events
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| CLUSTER_UUID | URL | UUID | O | 클러스터 UUID |


#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| events | Body | Array | 작업 이력 객체 목록 |
| events.id | Body | Integer | 작업 ID |
| events.uuid | Body | UUID | 작업 UUID |
| events.project_id | Body | String | 프로젝트(테넌트) ID |
| events.cluster_uuid | Body | UUID | 클러스터 UUID |
| events.cluster_name | Body | String | 클러스터 이름 |
| events.resource_uuid | Body | UUID | 작업 대상 UUID |
| events.resource_name | Body | String | 작업 대상 이름 |
| events.resource_type | Body | String | 작업 대상 종류("cluster" / "nodegroup") |
| events.type | Body | String | 작업 종류 |
| events.state | Body | String | 작업 상태("SUCCESS" / "FAIL" / "IN_PROGRESS") |
| events.contents | Body | String | 작업 진행 내용(성공 시 null) |
| events.details | Body | String | 작업 요청 정보 | 
| events.created_at | Body | String | 작업 시작 시간(UTC) |
| events.updated_at | Body | String | 작업 완료 시간(UTC) |


<details><summary>예시</summary>
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

### 작업 이력 보기

클러스터의 작업 이력을 조회합니다.

```
GET /v1/clusters/{CLUSTER_UUID}/events/{EVENT_UUID}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| CLUSTER_UUID | URL | UUID | O | 클러스터 UUID |
| EVENT_UUID | URL | UUID | O | 작업 UUID |


#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| id | Body | Integer | 작업 ID |
| uuid | Body | UUID | 작업 UUID |
| project_id | Body | String | 프로젝트(테넌트) ID |
| cluster_uuid | Body | UUID | 클러스터 UUID |
| cluster_name | Body | String | 클러스터 이름 |
| resource_uuid | Body | UUID | 작업 대상 UUID |
| resource_name | Body | String | 작업 대상 이름 |
| resource_type | Body | String | 작업 대상 종류("cluster" / "nodegroup") |
| type | Body | String | 작업 종류 |
| state | Body | String | 작업 상태("SUCCESS" / "FAIL" / "IN_PROGRESS") |
| contents | Body | String | 작업 진행 내용(성공 시 null) |
| events.details | Body | String | 작업 요청 정보 | 
| created_at | Body | String | 작업 시작 시간(UTC) |
| updated_at | Body | String | 작업 완료 시간(UTC) |


<details><summary>예시</summary>
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

### 클러스터 생성하기

클러스터를 생성합니다.

```
POST /v1/clusters
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| keypair | Body | String | O | 기본 워커 노드 그룹에 적용할 키페어 UUID |
| name | Body | String | O | 클러스터 이름 |
| cluster_template_id | Body | String | O | 클러스터 템플릿 ID. 반드시 "iaas_console"로 설정 |
| node_count | Body | String | O | 기본 워커 노드 그룹에 적용할 노드 수 |
| labels | Body | Object | O | 클러스터 생성 정보 개체 |
| labels.availability_zone | Body | String | O | 기본 워커 노드 그룹 적용 : 가용성 영역 |
| labels.node_image | Body | UUID | O | 기본 워커 노드 그룹 적용 : 베이스 이미지 UUID |
| labels.boot_volume_type | Body | String | O | 기본 워커 노드 그룹 적용 : 블록 스토리지 종류|
| labels.boot_volume_size | Body | String | O | 기본 워커 노드 그룹 적용 : 블록 스토리지 사이즈(GB) |
| labels.external_network_id | Body | String | X | 인터넷 게이트웨이에 연결된 VPC 네트워크 UUID<br>VPC 서브넷이 연동된 라우터가 인터넷 게이트웨이에 연결된 경우 반드시 설정 |
| labels.external_subnet_id_list | Body | String | X | 인터넷 게이트웨이에 연결된 서브넷 UUID 목록(콜론으로 구분)<br>VPC 서브넷이 연동된 라우터가 인터넷 게이트웨이에 연결된 경우 반드시 설정 |
| labels.cert_manager_api | Body | String | O | CSR(Certificate Signing Request) 기능 활성화 여부. 반드시 "True" 로 설정 |
| labels.ca_enable | Body | String | O | 기본 워커 노드 그룹 적용 : 클러스터 오토스케일러: 기능 활성화 여부 ("True" / "False") |
| labels.ca_pod_replicas | Body | String | X | 기본 워커 노드 그룹 적용 : 클러스터 오토스케일러: 파드 수 |
| labels.ca_max_node_count | Body | String | X | 기본 워커 노드 그룹 적용 : 클러스터 오토스케일러: 최대 노드 수 |
| labels.ca_min_node_count | Body | String | X | 기본 워커 노드 그룹 적용 : 클러스터 오토스케일러: 최소 노드 수 |
| labels.ca_scale_down_enable | Body | String | X | 기본 워커 노드 그룹 적용 : 클러스터 오토스케일러: 감축 활성 여부 ("True" / "False") |
| labels.ca_scale_down_unneeded_time | Body | String | X | 기본 워커 노드 그룹 적용 : 클러스터 오토스케일러: 임계 영역 유지 시간 |
| labels.ca_scale_down_util_thresh | Body | String | X | 기본 워커 노드 그룹 적용 : 클러스터 오토스케일러: 리소스 사용량 임계치  |
| labels.ca_scale_down_delay_after_add | Body | String | X | 기본 워커 노드 그룹 적용 : 클러스터 오토스케일러: 증설 후 감축 지연 시간 |
| labels.mba_scale_out | Body | String | X | 기본 노드 그룹 적용 : 지표 기반 오토스케일러 증설 정책 설정 |
| labels.mba_scale_in | Body | String | X |  기본 노드 그룹 적용 : 지표 기반 오토스케일러 감축 정책 설정 |
| labels.kube_tag | Body | String | O | Kubernetes 버전 |
| labels.user_script | Body | String | X | 사용자 스크립트(old) |
| labels.user_script_v2 | Body | String | X | 사용자 스크립트 |
| labels.master_lb_floating_ip_enabled | Body | String | O | Kubernetes API 엔드포인트에 공인 도메인 주소 생성 여부 ("True" / "False")<br>labels.external_network_id와 external_subnet_id_list가 설정된 경우에만 "True"로 설정 가능 |
| labels.strict_sg_rules | Body | String | X | 워커 노드 보안 그룹에 필수 보안 규칙만 생성("True" / "False"), 기본값: "False" |
| labels.additional_network_id_list | Body | String | X |  기본 워커 노드 그룹 적용: 추가 네트워크의 VPC 네트워크 UUID 목록(콜론으로 구분) |
| labels.additional_subnet_id_list | Body | String | X |  기본 워커 노드 그룹 적용: 추가 네트워크의 VPC 서브넷 UUID 목록(콜론으로 구분) |
| labels.service_cluster_ip_range | Body | String  | X |  K8s 서비스 네트워크, 클러스터에서 서비스 생성 시 ClusterIP에 할당되는 IP 대역. fixed_subnet, pods_network_cidr, service_cluster_ip_range 입력 규칙 참고 |
| labels.pods_network_cidr | Body | String |  X |  클러스터 파드 네트워크. fixed_subnet, pods_network_cidr, service_cluster_ip_range 입력 규칙 참고 |
| labels.pods_network_subnet | Body | Integer | X |  클러스터 파드 서브넷 크기. pods_network_subnet 입력 규칙 참고 |
| labels.extra_security_groups | Body | Array | X | 기본 워커 노드 그룹 적용: 추가 보안 그룹 객체 목록 |
| labels.extra_security_groups[].target_subnet | Body | String | X | 추가 보안 그룹 지정 대상 서브넷 UUID |
| labels.extra_security_groups[].security_group_ids | Body | String | X | 추가 보안 그룹 UUID 목록(쉼표로 구분) |
| labels.extra_volumes | Body | Array | X | 기본 워커 노드 그룹 적용: 추가 블록 스토리지 객체 목록 |
| labels.extra_volumes[].volume_type | Body | String | X | 추가 블록 스토리지 종류 |
| labels.extra_volumes[].volume_size | Body | Integer | X | 추가 블록 스토리지 사이즈(GB) |
| labels.extra_volumes[].volume_key_id | Body | String | X | (암호화된 블록 스토리지를 사용하는 경우) 암호화된 블록 스토리지에 적용할 대칭 키 ID |
| labels.extra_volumes[].volume_appkey | Body | String | X | (암호화된 블록 스토리지를 사용하는 경우) 암호화된 블록 스토리지에 적용할 대칭 키의 앱키 |
| labels.extra_volumes[].volume_mount_path | Body | String | X | 추가 블록 스토리지가 마운트될 경로 |
| labels.control_plane_log | Body | String | X | K8S 컨트롤 플레인 로그 저장 활성화 |
| labels.fip_auto_bind_enable | Body | String | X | 플로팅 IP 자동 할당: 기능 활성화 여부 ("True" / "False") |
| labels.fip_bind_subnet | Body | String | X | 플로팅 IP 자동 할당: 플로팅 IP가 연결되는 네트워크 인터페이스의 서브넷 |
| labels.fip_selector | Body | String | X | 플로팅 IP 자동 할당: 노드에 할당할 플로팅 IP를 선별하기 위한 식별자 |
| labels.fip_auto_bind_enable | Body | String | X | 기본 워커 노드 그룹 적용: 플로팅 IP 자동 할당: 기능 활성화 여부 ("True" / "False") |
| labels.fip_bind_subnet | Body | String | X | 기본 워커 노드 그룹 적용: 플로팅 IP 자동 할당: 플로팅 IP가 연결되는 네트워크 인터페이스의 서브넷 |
| labels.fip_selector | Body | String | X | 기본 워커 노드 그룹 적용: 플로팅 IP 자동 할당: 노드에 할당할 플로팅 IP를 선별하기 위한 식별자 |
| labels.k8s_node_labels | Body | String | 기본 워커 노드 그룹 적용: Kubernetes 레이블 설정 |
| flavor_id | Body | UUID | O | 기본 워커 노드 그룹 적용: 노드 인스턴스 타입 UUID |
| fixed_network | Body | UUID | O | VPC 네트워크 UUID |
| fixed_subnet | Body | UUID | O | VPC 서브넷 UUID. fixed_subnet, pods_network_cidr, service_cluster_ip_range 입력 규칙 참고 |
| addons | Body | List of Object | X | 설치할 애드온 정보 목록 |
| addons.name | Body | String | O | 애드온 이름 |
| addons.version | Body | String | O | 애드온 버전 |
| addons.options | Body | Object | X | 애드온 별 옵션 |

> [주의]
> fixed_subnet, pods_network_cidr, service_cluster_ip_range의 CIDR은 아래와 같은 규칙으로 입력되어야 합니다.
>  - 링크 로컬 주소 대역(169.254.0.0/16)과 중첩될 수 없습니다.
>  - fixed_subnet, pods_network_cidr, service_cluster_ip_range 대역은 중첩될 수 없습니다.
>  - NKS 내부에서 사용하고 있는 IP 대역(198.18.0.0/19)과 중첩될 수 없습니다.
>  - /24보다 큰 CIDR 블록은 입력할 수 없습니다(다음과 같은 CIDR 블록은 사용할 수 없습니다. /26, /30).
>  - v1.23.3 이하 클러스터의 경우 도커 BIP(bridged IP range)와 중첩될 수 없습니다(172.17.0.0/16).
> pods_network_subnet은 아래와 같은 규칙으로 입력되어야 합니다.
>  - 20-28(포함) 범위의 값만 입력 가능합니다.
>  - pods_network_subnet 값이 pods_network_cidr prefix 값보다 최소 2 커야 합니다. 정상 예시(파드 서브넷 크기: 24, 파드 네트워크: 10.100.0.0/22)


<details><summary>예시</summary>
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

#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| uuid | Body | UUID | 클러스터 UUID |

<details><summary>예시</summary>
<p>

```json
{
    "uuid": "5801ef8a-3760-4858-b467-fc4c1201241d"
}
```

</p>
</details>

---

### 클러스터 삭제하기

클러스터를 삭제합니다.

```
DELETE /v1/clusters/{CLUSTER_ID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | 클러스터 UUID 또는 클러스터 이름 | 


#### 응답

이 API는 응답 본문을 반환하지 않습니다.

---

### 리사이즈

클러스터의 노드 수를 조정합니다.

```
POST /v1/clusters/{CLUSTER_ID_OR_NAME}/actions/resize
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | 클러스터 UUID 또는 클러스터 이름 | 
| nodegroup | Body | UUID | O | 대상 워커 노드 그룹 이름 / UUID |
| node_count | Body | Integer | O | 변경하고자 하는 워커 노드 수 |
| nodes_to_remove | Body | String List | X | 삭제하고자 하는 노드 UUID |

* 주의 사항
    * 노드를 감축하는 경우(즉, 일부 노드 삭제) 삭제할 노드를 지정하려면 **nodes_to_remove**를 설정해야 합니다. 삭제할 노드를 지정하지 않으면 삭제 대상 노드는 무작위로 선택됩니다.
    * node_count 최소 1, 최대 10(단, 최대값은 quota로 조정 가능)

<details><summary>증설 예시</summary>
<p>

```json
{
    "node_count": 3,
    "nodegroup": "default-worker"
}
```

</p>
</details>

<details><summary>감축 예시</summary>
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



#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| uuid | Body | String | 대상 클러스터 UUID|

<details><summary>예시</summary>
<p>

```json
{
    "uuid": "5bac7acd-58b7-4cf5-95f5-a25d67da13a2"
}

```

</p>
</details>

---

### 클러스터 kubeconfig 조회

클러스터 설정 파일(kubeconfig)을 조회합니다.

```
GET /v1/clusters/{CLUSTER_ID_OR_NAME}/config
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | 클러스터 UUID 또는 클러스터 이름 | 


#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| config | Body | String | kubeconfig 파일 본문 |


<details><summary>예시</summary>
<p>

```json
{
    "config": "apiVersion: v1\nclusters:\n- cluster:\n    certificate-authority-data: LS0tLS1CRU... \n    server: https://96742ac4-kr2-k8s.container.cloud.toast.com:6443\n  name: \"toast-robot-e2e-1-18\"\ncontexts:\n- context:\n    cluster: \"toast-robot-e2e-1-18\"\n    user: admin\n  name: default\ncurrent-context: default\nkind: Config\npreferences: {}\nusers:\n- name: admin\n  user:\n    client-certificate-data: LS0tLS1CRU...\n    client-key-data: LS0tLS1CRU...\n"
}
```

</p>
</details>


### 클러스터 API 엔드포인트 IP 접근 제어 적용
클러스터 API 엔드포인트에 IP 접근 제어를 적용하거나 해제할 수 있습니다.
IP 접근 제어 기능에 대한 자세한 사항은 [IP 접근제어](/Network/Load%20Balancer/ko/overview-gov/#ip) 문서를 참고하세요.
클러스터 API 엔드포인트에 IP 접근 제어 규칙에 대한 자세한 사항은 [사용자 가이드](/Container/NKS/ko/user-guide-gov/#api_endpoint_ipacl)를 참고하세요.

```
POST /v1/clusters/{CLUSTER_ID_OR_NAME}/api_ep_ipacl
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | 클러스터 UUID 또는 클러스터 이름 |
| enable | Body | String | O | 'true', 'false'중 하나로 설정 가능. 기본값: 'false'<br>true:클러스터 API 엔드포인트에 IP 접근 제어 적용<br>false: 클러스터 API 엔드포인트에 IP 접근 제어 해제, false 설정 시 하위 설정은 모두 무시됨 |
| action | Body | String | O(enable 설정이 true인 경우) | IP 접근 제어 타입, ALLOW, DENY 중 하나로 설정 가능 |
| ipacl_targets | Body | List of Object | O(enable 설정이 true인 경우) | IP 접근 제어 대상 객체 |
| ipacl_targets.cidr_address | Body | String | O(enable 설정이 true인 경우) | IP 접근 제어 대상. IP 주소 또는 CIDR 형식의 IP 주소 범위 입력 가능 |
| ipacl_targets.descripion | Body | String | X | IP 접근 제어 대상 설명 |


<details><summary>예시</summary>
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


#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| uuid | Body | UUID | 클러스터 UUID |

<details><summary>예시</summary>
<p>

```json
{
    "uuid": "0641db9f-5e71-4df9-9571-089c7964d82e"
}
```

</p>
</details>


### 클러스터 API 엔드포인트 IP 접근 제어 조회
클러스터 API 엔드포인트에 적용된 IP 접근 제어 정보를 확인할 수 있습니다.

```
GET /v1/clusters/{CLUSTER_ID_OR_NAME}/api_ep_ipacl
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청
이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | 클러스터 UUID 또는 클러스터 이름 | 


#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| cluster_uuid | Body | UUID | 클러스터 UUID |
| enable | Body | String | true: 클러스터 API 엔드포인트에 IP 접근 제어가 적용 되어있음, false: 클러스터 API 엔드포인트에 IP 접근 제어가 해제 되어있음 | 
| action | Body | String | IP 접근 제어 타입 ALLOW, DENY 확인 가능 |
| ipacl_targets | Body | List of Object | IP 접근 제어 대상 객체 |
| ipacl_targets.cidr_address | Body | String | IP 접근 제어 대상. IP 주소 또는 CIDR 형식의 IP 주소 범위 입력 가능 |
| ipacl_targets.descripion | Body | String | IP 접근 제어 대상 설명 |

<details><summary>enable: true 인 경우 예시</summary>
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

<details><summary>enable: false 인 경우 예시</summary>
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
### 클러스터 인증서 갱신

클러스터의 인증서를 갱신합니다.
```
PATCH /v1/certificates/{CLUSTER_ID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청
| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | 클러스터 UUID 또는 클러스터 이름 | 
| term_of_validity | Body | Integer | O | 인증서의 유효 기간. 년 단위로 입력 가능. 최솟값: 1, 최댓값: 5 |

<details><summary>예시</summary>
<p>

```json
{
    "term_of_validity": 5
}
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
{
    "uuid": "5f6af7da-df9b-4edd-8284-02317b11e061"
}

```

</p>
</details>

---
### 컨트롤 플레인 Kubernetes 컴포넌트 로그 저장
NHN Kubernetes Service(NKS)의 컨트롤 플레인에서 실행 중인 주요 Kubernetes 컴포넌트들의 로그를 Log & Crash Search 또는 또는 Object Storage에 저장합니다.
```
PATCH /v1/clusters/{CLUSTER_ID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | 클러스터 UUID 또는 클러스터 이름 | 
| type | Body | String | O | `control_plane_log`로 설정 |
| control_plane_log | Body | Object | O | control_plane_log 객체 |
| control_plane_log.enable | Body | bool | O | K8S 컨트롤 플레인 로그 저장 활성화 |
| control_plane_log.type | Body | String | enable : true인 경우 필수 | lncs : Log & Crash Search 로 control plane 로그 전송 obs : Object storage 로 control plane 로그 전송 |
| control_plane_log.sgw | Body | UUID | enable : true인 경우 필수 | control_plane_log.type 에 따라 구분됨<br>lncs : Log and Crash Search Service Gateway UUID<br>obs : Object storage Search Service Gateway UUID |
| control_plane_log.upload_interval | Body | Integer | x | OBS로 log 전송 주기 설정 (분)<br>min : 1<br>max : 60<br>default : 10 |
| control_plane_log.lncs_appkey | Body | String | enable : true<br>control_plane_log.type = lncs 인 경우 필수 | NKS와 동일한 프로젝트(테넌트)의 Log & Crash Search Appkey 정보 |
| control_plane_log.obs_api_url | Body | String | enable : true<br>control_plane_log.type = obs 인 경우 필수 | 사용자의 obs 컨테이너 full path<br>(OBS의 스토리지 주소 + OBS의 컨테이너 이름 + 희망하는 저장 경로) |
| control_plane_log.obs_store_as | Body | String | X | OBS 로그 파일 제공 방식(gzip, text) |


<details><summary>Log & Crash Search 로그 전송 활성화</summary>
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


<details><summary>Object Storage 로그 전송 활성화</summary>
<p>

```json
{
    "type": "control_plane_log",
    "control_plane_log" : {
        "enable": true,
        "type": "obs",
        "sgw": "b6f68830-e688-4d89-ac03-2f1155a4177a",
        "upload_interval" : 60,
        "obs_api_url" :"https://kr1-api-object-storage.gov-nhncloudservice.com/v1/AUTH_d5b58ab0bb9340909bd7ff5a24f44313/iksoon-obs-container/testpath",
        "obs_store_as" : "gzip"
    }
}
```

</p>
</details>


<details><summary>로그 전송 비활성화</summary>
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

#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| uuid | Body | UUID | 클러스터 UUID |

<details><summary>예시</summary>
<p>

```json
{
    "uuid": "018b06c5-1293-4081-8242-167a1cb9f262"
}
```

</p>
</details>


---

## 노드 그룹

### 노드 그룹 목록 보기

노드 그룹 목록을 조회합니다.

```
GET /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | 클러스터 UUID 또는 클러스터 이름 | 


#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| nodegroups | Body | Array | 노드 그룹 정보 객체 목록 |
| nodegroups.uuid | Body | UUID | 노드 그룹 UUID |
| nodegroups.flavor_id | Body | UUID | 노드 그룹 인스턴스 타입 UUID |
| nodegroups.image_id | Body | UUID | 노드 그룹 베이스 이미지 UUID |
| nodegroups.max_node_count | Body | Integer | 노드 그룹 최대 노드 수 |
| nodegroups.min_node_count | Body | Integer | 노드 그룹 최소 노드 수 |
| nodegroups.name | Body | String | 노드 그룹 이름 |
| nodegroups.node_count | Body | Integer | 노드 그룹 노드 수 |
| nodegroups.role | Body | String | 노드 그룹 역할 |
| nodegroups.stack_id | Body | UUID | 노드 그룹에 연결된 heat stack UUID |
| nodegroups.status | Body | String | 노드 그룹 상태 |

<details><summary>예시</summary>
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

### 노드 그룹 보기

개별 노드 그룹 정보를 조회합니다.

```
GET /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | 클러스터 UUID 또는 클러스터 이름 | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | 노드 그룹 UUID 또는 노드 그룹 이름 | 

#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| uuid | Body | UUID | 노드 그룹 UUID |
| name | Body | String | 노드 그룹 이름 |
| cluster_id | Body  | UUID | 노드 그룹이 속한 클러스터 UUID |
| flavor_id | Body | UUID | 노드에서 사용하는 인스턴스 타입 UUID |
| image_id | Body | UUID | 노드에서 사용하는 베이스 이미지 UUID |
| labels | Body | Object | 노드 그룹 생성 정보 개체 |
| labels.availability_zone | Body | String | 워커 노드 그룹 적용 : 가용성 영역 |
| labels.node_image | Body | UUID | 워커 노드 그룹 적용 : 베이스 이미지 UUID |
| labels.boot_volume_type | Body | String | 워커 노드 그룹 적용 : 블록 스토리지 종류|
| labels.boot_volume_size | Body | String | 워커 노드 그룹 적용 : 블록 스토리지 사이즈(GB) |
| labels.external_network_id | Body | String | 인터넷 게이트웨이에 연결된 VPC 네트워크 UUID |
| labels.external_subnet_id_list | Body | String | 인터넷 게이트웨이에 연결된 서브넷 UUID 목록(콜론으로 구분) |
| labels.cert_manager_api | Body | String | CSR(Certificate Signing Request) 기능 활성화 여부. 반드시 "True" 로 설정 |
| labels.ca_enable | Body | String | 워커 노드 그룹 적용 : 클러스터 오토스케일러: 기능 활성화 여부 ("True" / "False") |
| labels.ca_pod_replicas | Body | String | 워커 노드 그룹 적용 : 클러스터 오토스케일러: 파드 수 |
| labels.ca_max_node_count | Body | String | 워커 노드 그룹 적용 : 클러스터 오토스케일러: 최대 노드 수 |
| labels.ca_min_node_count | Body | String | 워커 노드 그룹 적용 : 클러스터 오토스케일러: 최소 노드 수 |
| labels.ca_scale_down_enable | Body | String | 워커 노드 그룹 적용 : 클러스터 오토스케일러: 감축 활성 여부 ("True" / "False") |
| labels.ca_scale_down_unneeded_time | Body | String | 워커 노드 그룹 적용 : 클러스터 오토스케일러: 임계 영역 유지 시간 |
| labels.ca_scale_down_util_thresh | Body | String | 워커 노드 그룹 적용 : 클러스터 오토스케일러: 리소스 사용량 임계치  |
| labels.ca_scale_down_delay_after_add | Body | String | 워커 노드 그룹 적용 : 클러스터 오토스케일러: 증설 후 감축 지연 시간 |
| labels.mba_scale_out | Body | String | 워커 노드 그룹 적용 : 지표 기반 오토스케일러 증설 정책 설정 |
| labels.mba_scale_in | Body | String |  워커 노드 그룹 적용 : 지표 기반 오토스케일러 감축 정책 설정 |
| labels.kube_tag | Body | String | 워커 노드 그룹 Kubernetes 버전 |
| labels.user_script | Body | String | 사용자 스크립트(old) |
| labels.user_script_v2 | Body | String | 사용자 스크립트 |
| labels.additional_network_id_list | Body | String | 워커 노드 그룹 적용: 추가 네트워크의 VPC 네트워크 UUID 목록(콜론으로 구분) |
| labels.additional_subnet_id_list | Body | String | 워커 노드 그룹 적용: 추가 네트워크의 VPC 서브넷 UUID 목록(콜론으로 구분) |
| labels.strict_sg_rules | Body | String | 워커 노드 보안 그룹에 필수 보안 규칙만 생성("True" / "False"), (2024.03.05. 이후에 생성된 클러스터에서 확인 가능) |
| max_node_count | Body | Integer | 최대 노드 수 |
| min_node_count | Body | Integer | 최소 노드 수 |
| node_addresses | Body | String list | 노드 IP 주소 목록 |
| node_count | Body | Integer | 노드 수 |
| project_id | Body | String | 프로젝트(테넌트) ID |
| role | Body | String | 노드 그룹 역할 |
| stack_id | Body | UUID | 노드 그룹에 연결된 heat stack UUID |
| status | Body | String | 노드 그룹 상태 |
| status_reason | Body | String | 노드 그룹 상태 이유(null 가능) |
| created_at | Body | String | 생성 시간(UTC) |
| updated_at | Body | String | 최근 업데이트 시간(UTC) |

<details><summary>예시</summary>
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
            "href": "https://kr1-api-kubernetes-infrastructure.gov-nhncloudservice.com/v1/clusters/96742ac4-02e7-4b1d-a242-02876c0bd3f8/nodegroups/018b06c5-1293-4081-8242-167a1cb9f262",
            "rel": "self"
        },
        {
            "href": "https://kr1-api-kubernetes-infrastructure.gov-nhncloudservice.com/clusters/96742ac4-02e7-4b1d-a242-02876c0bd3f8/nodegroups/018b06c5-1293-4081-8242-167a1cb9f262",
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

### 노드 그룹 생성하기

노드 그룹을 생성합니다.

```
POST /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | 클러스터 UUID 또는 클러스터 이름 | 
| flavor_id | Body | UUID | O |  노드에서 사용하는 인스턴스 타입 UUID |
| image_id | Body | UUID | O | 노드에서 사용하는 베이스 이미지 UUID |
| labels | Body | Object | O | 노드 그룹 생성 정보 개체 |
| labels.availability_zone | Body | String | O | 기본 워커 노드 그룹 적용 : 가용성 영역 |
| labels.boot_volume_type | Body | String | O | 기본 워커 노드 그룹 적용 : 블록 스토리지 종류|
| labels.boot_volume_size | Body | String | O | 기본 워커 노드 그룹 적용 : 블록 스토리지 사이즈(GB) |
| labels.ca_enable | Body | String | O | 기본 워커 노드 그룹 적용 : 클러스터 오토스케일러: 기능 활성화 여부 ("True" / "False") |
| labels.ca_pod_replicas | Body | String | X | 기본 워커 노드 그룹 적용 : 클러스터 오토스케일러: 파드 수 |
| labels.ca_max_node_count | Body | String | X | 기본 워커 노드 그룹 적용 : 클러스터 오토스케일러: 최대 노드 수 |
| labels.ca_min_node_count | Body | String | X | 기본 워커 노드 그룹 적용 : 클러스터 오토스케일러: 최소 노드 수 |
| labels.ca_scale_down_enable | Body | String | X | 기본 워커 노드 그룹 적용 : 클러스터 오토스케일러: 감축 활성 여부 ("True" / "False") |
| labels.ca_scale_down_unneeded_time | Body | String | X | 기본 워커 노드 그룹 적용 : 클러스터 오토스케일러: 임계 영역 유지 시간 |
| labels.ca_scale_down_util_thresh | Body | String | X | 기본 워커 노드 그룹 적용 : 클러스터 오토스케일러: 리소스 사용량 임계치  |
| labels.ca_scale_down_delay_after_add | Body | String | X | 기본 워커 노드 그룹 적용 : 클러스터 오토스케일러: 증설 후 감축 지연 시간 |
| labels.mba_scale_out | Body | String | X | 기본 노드 그룹 적용 : 지표 기반 오토스케일러 증설 정책 설정 |
| labels.mba_scale_in | Body | String | X | 기본 노드 그룹 적용 : 지표 기반 오토스케일러 감축 정책 설정 |
| labels.user_script | Body | String | X | 사용자 스크립트(old) |
| labels.user_script_v2 | Body | String | X | 사용자 스크립트 |
| labels.additional_network_id_list | Body | String | X | 워커 노드 그룹 적용: 추가 네트워크의 VPC 네트워크 UUID 목록(콜론으로 구분) |
| labels.additional_subnet_id_list | Body | String | X | 워커 노드 그룹 적용: 추가 네트워크의 VPC 서브넷 UUID 목록(콜론으로 구분) |
| labels.extra_security_groups | Body | Array | X | 추가 보안 그룹 객체 목록 |
| labels.extra_security_groups[].target_subnet | Body | String | X | 추가 보안 그룹 지정 대상 서브넷 UUID |
| labels.extra_security_groups[].security_group_ids | Body | String | X | 추가 보안 그룹 UUID 목록(쉼표로 구분) |
| labels.extra_volumes | Body | Array | X | 추가 블록 스토리지 객체 목록 |
| labels.extra_volumes[].volume_type | Body | String | X | 추가 블록 스토리지 종류 |
| labels.extra_volumes[].volume_size | Body | Integer | X | 추가 블록 스토리지 사이즈(GB) |
| labels.extra_volumes[].volume_key_id | Body | String | X | (암호화된 블록 스토리지를 사용하는 경우) 암호화된 블록 스토리지에 적용할 대칭 키 ID |
| labels.extra_volumes[].volume_appkey | Body | String | X | (암호화된 블록 스토리지를 사용하는 경우) 암호화된 블록 스토리지에 적용할 대칭 키의 앱키 |
| labels.extra_volumes[].volume_mount_path | Body | String | X | 추가 블록 스토리지가 마운트될 경로 |
| labels.fip_auto_bind_enable | Body | String | X | 플로팅 IP 자동 할당: 기능 활성화 여부 ("True" / "False") |
| labels.fip_bind_subnet | Body | String | X | 플로팅 IP 자동 할당: 플로팅 IP가 연결되는 네트워크 인터페이스의 서브넷 |
| labels.fip_selector | Body | String | X | 플로팅 IP 자동 할당: 노드에 할당할 플로팅 IP를 선별하기 위한 식별자 |
| labels.k8s_node_labels | Body | String | Kubernetes 레이블 설정 |
| name | BODY | String | O | 노드 그룹 이름 |
| node_count | Body | Integer | X | 노드 수(기본값: 1) |


<details><summary>예시</summary>
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

#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| uuid | Body | UUID | 노드 그룹 UUID |
| cluster_id | Body  | UUID | 노드 그룹이 속한 클러스터 UUID |
| flavor_id | Body | UUID |  노드에서 사용하는 인스턴스 타입 UUID |
| image_id | Body | UUID | 노드에서 사용하는 베이스 이미지 UUID |
| labels | Body | Object | 노드 그룹 생성 정보 개체 |
| labels.availability_zone | Body | String | 기본 워커 노드 그룹 적용 : 가용성 영역 |
| labels.boot_volume_type | Body | String | 기본 워커 노드 그룹 적용 : 블록 스토리지 종류|
| labels.boot_volume_size | Body | String | 기본 워커 노드 그룹 적용 : 블록 스토리지 사이즈(GB) |
| labels.ca_enable | Body | String | 기본 워커 노드 그룹 적용 : 클러스터 오토스케일러: 기능 활성화 여부 ("True" / "False") |
| labels.ca_pod_replicas | Body | String | 기본 워커 노드 그룹 적용 : 클러스터 오토스케일러: 파드 수 |
| labels.ca_max_node_count | Body | String | 기본 워커 노드 그룹 적용 : 클러스터 오토스케일러: 최대 노드 수 |
| labels.ca_min_node_count | Body | String | 기본 워커 노드 그룹 적용 : 클러스터 오토스케일러: 최소 노드 수 |
| labels.ca_scale_down_enable | Body | String | 기본 워커 노드 그룹 적용 : 클러스터 오토스케일러: 감축 활성 여부 ("True" / "False") |
| labels.ca_scale_down_unneeded_time | Body | String | 기본 워커 노드 그룹 적용 : 클러스터 오토스케일러: 임계 영역 유지 시간 |
| labels.ca_scale_down_util_thresh | Body | String | 기본 워커 노드 그룹 적용 : 클러스터 오토스케일러: 리소스 사용량 임계치  |
| labels.ca_scale_down_delay_after_add | Body | String | 기본 워커 노드 그룹 적용 : 클러스터 오토스케일러: 증설 후 감축 지연 시간 |
| labels.mba_scale_out | Body | String | 기본 노드 그룹 적용 : 지표 기반 오토스케일러 증설 정책 설정 |
| labels.mba_scale_in | Body | String | 기본 노드 그룹 적용 : 지표 기반 오토스케일러 감축 정책 설정 |
| labels.user_script | Body | String | 사용자 스크립트(old) |
| labels.user_script_v2 | Body | String | 사용자 스크립트 |
| labels.additional_network_id_list | Body | String | 워커 노드 그룹 적용: 추가 네트워크의 VPC 네트워크 UUID 목록(콜론으로 구분) |
| labels.additional_subnet_id_list | Body | String | 워커 노드 그룹 적용: 추가 네트워크의 VPC 서브넷 UUID 목록(콜론으로 구분) |
| max_node_count | Body | Integer | 최대 노드 수 |
| min_node_count | Body | Integer | 최소 노드 수 |
| name | BODY | String | 노드 그룹 이름 |
| node_count | Body | Integer | 노드 수(기본값: 1) |
| project_id | Body | String | 프로젝트(테넌트) ID |
| role | Body | String | 노드 그룹 역할 |

<details><summary>예시</summary>
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
            "href": "https://kr1-api-kubernetes-infrastructure.gov-nhncloudservice.com/v1/clusters/96742ac4-02e7-4b1d-a242-02876c0bd3f8/nodegroups/a3366f2f-a1f3-45ef-8390-10536e8060ff",
            "rel": "self"
        },
        {
            "href": "https://kr1-api-kubernetes-infrastructure.gov-nhncloudservice.com/clusters/96742ac4-02e7-4b1d-a242-02876c0bd3f8/nodegroups/a3366f2f-a1f3-45ef-8390-10536e8060ff",
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

### 노드 그룹 삭제하기

지정한 노드 그룹를 삭제합니다.
```
DELETE /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | 클러스터 UUID 또는 클러스터 이름 | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | 노드 그룹 UUID 또는 노드 그룹 이름 | 

#### 응답

이 API는 응답 본문을 반환하지 않습니다.

---

### 노드 중지하기

지정한 노드 목록을 중지시킵니다.

```
POST /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}/stop_node
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | 클러스터 UUID 또는 클러스터 이름 | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | 노드 그룹 UUID 또는 노드 그룹 이름 | 
| node_list | Body | String | O | 콜론(`:`)으로 구분된 노드 인스턴스 UUID 목록 |

<details><summary>예시</summary>
<p>

```json
{
    "node_list": "bdaa560c-7a30-4249-9438-2df27fa1e9d38:68ff49ee-4111-4212-8e9e-88835cb0ebaa"
}
```

</p>
</details>


#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| uuid | Body | UUID | 노드 그룹 UUID |

<details><summary>예시</summary>
<p>

```json
{
    "uuid": "439aa682-398f-4061-a4d1-116da6b1154e"
}
```

</p>
</details>

---

### 노드 시작하기

지정한 노드 목록을 시작시킵니다.

```
POST /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}/start_node
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | 클러스터 UUID 또는 클러스터 이름 | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | 노드 그룹 UUID 또는 노드 그룹 이름 | 
| node_list | Body | String | O | 콜론(`:`)으로 구분된 노드 인스턴스 UUID 목록 |

<details><summary>예시</summary>
<p>

```json
{
    "node_list": "bdaa560c-7a30-4249-9438-2df27fa1e9d38"
}
```

</p>
</details>

#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| uuid | Body | UUID | 노드 그룹 UUID |

<details><summary>예시</summary>
<p>

```json
{
    "uuid": "439aa682-398f-4061-a4d1-116da6b1154e"
}
```

</p>
</details>

---

### 노드 그룹의 클러스터 오토스케일러 설정 보기

노드 그룹의 클러스터 오토스케일러 설정을 조회합니다.

```
GET /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}/autoscale
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청

이 API는 요청 본문을 요구하지 않습니다.

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | 클러스터 UUID 또는 클러스터 이름 | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | 노드 그룹 UUID 또는 노드 그룹 이름 | 


#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| ca_enable | Body | String | 기능 활성화 여부 ("True" / "False") |
| ca_pod_replicas | Body | String | 파드 수 |
| ca_max_node_count | Body | String | 최대 노드 수 |
| ca_min_node_count | Body | String | 최소 노드 수 |
| ca_scale_down_enable | Body | String | 감축 활성 여부 ("True" / "False") |
| ca_scale_down_unneeded_time | Body | String | 임계 영역 유지 시간 |
| ca_scale_down_util_thresh | Body | String | 리소스 사용량 임계치  |
| ca_scale_down_delay_after_add | Body | String | 증설 후 감축 지연 시간 |

<details><summary>예시</summary>
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

### 노드 그룹의 클러스터 오토스케일러 설정 변경하기

노드 그룹의 클러스터 오토스케일러 설정을 변경합니다.

```
POST /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}/autoscale
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | 클러스터 UUID 또는 클러스터 이름 | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | 노드 그룹 UUID 또는 노드 그룹 이름 | 
| ca_enable | Body | String | O | 기능 활성화 여부 ("True" / "False") |
| ca_pod_replicas | Body | String | X | 파드 수 |
| ca_max_node_count | Body | String |X| 최대 노드 수 |
| ca_min_node_count | Body | String |X| 최소 노드 수 |
| ca_scale_down_enable | Body | String |X| 감축 활성 여부 ("True" / "False") |
| ca_scale_down_unneeded_time | Body | String |X| 임계 영역 유지 시간 |
| ca_scale_down_util_thresh | Body | String | X |리소스 사용량 임계치  |
| ca_scale_down_delay_after_add | Body | String | X |증설 후 감축 지연 시간 |

<details><summary>예시</summary>
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



#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| uuid | Body | UUID | 노드 그룹 UUID |

<details><summary>예시</summary>
<p>

```json
{
    "uuid": "018b06c5-1293-4081-8242-167a1cb9f262"
}
```

</p>
</details>

---

### 노드 그룹의 지표 기반 오토스케일러 설정 변경하기

노드 그룹의 지표 기반 오토스케일러 설정을 변경합니다.

```
PATCH /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | 클러스터 UUID 또는 클러스터 이름 | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | 노드 그룹 UUID 또는 노드 그룹 이름 | 
| type | Body | String | O | metric_base_autoscale 설정 |
| mba_scale_out | Body | Object | X | 지표 기반 오토스케일 증설 정책 설정 |
| mba_scale_out.enable | Body | Boolean | X | 기능 활성화 여부 (true / false) |
| mba_scale_out.max_node_count | Body | Integer | X | 노드 그룹의 최대 노드 수 |
| mba_scale_out.rules_operator | Body | String | X | 증설 규칙 사이에 적용되는 연산자 ("AND" / "OR") |
| mba_scale_out.delay | Body | Integer | X | 증설 대기 시간 (1 ~ 60)분 |
| mba_scale_out.adjustment_count | Body | Integer | X | 증설 노드 수 (1 ~ 10) |
| mba_scale_out.rules | Body | List of Object | mba_scale_out.enable 설정이 true인 경우 1개 이상의 규칙 설정 필요 | 증설 규칙 설정 |
| mba_scale_out.rules.metric | Body | Integer | mba_scale_out.enable 설정이 true인 경우 필수 | 지표 설정 |
| mba_scale_out.rules.threshold | Body | Integer | mba_scale_out.enable 설정이 true인 경우 필수 | 임계치 설정 |
| mba_scale_out.rules.duration | Body | Integer | mba_scale_out.enable 설정이 true인 경우 필수 | 임계 영역 유지 시간 (2 ~ 60)분 |
| mba_scale_in | Body | Object | X | 지표 기반 오토스케일 감축 정책 설정 |
| mba_scale_in.enable | Body | Boolean | X | 기능 활성화 여부 (true / false) |
| mba_scale_in.min_node_count | Body | Integer | X | 노드 그룹의 최소 노드 수 |
| mba_scale_in.rules_operator | Body | String | X | 감축 규칙 사이에 적용되는 연산자 ("AND" / "OR") |
| mba_scale_in.delay | Body | Integer | X | 감축 대기 시간 (1 ~ 60)분 |
| mba_scale_in.adjustment_count | Body | Integer | X | 감축 노드 수 (1 ~ 10)대 |
| mba_scale_in.rules | Body | List of Object | mba_scale_in.enable 설정이 true인 경우 1개 이상의 규칙 설정 필요 | 감축 규칙 설정 |
| mba_scale_in.rules.metric | Body | Integer | mba_scale_in.enable 설정이 true인 경우 필수 | 지표 설정 |
| mba_scale_in.rules.threshold | Body | Integer | mba_scale_in.enable 설정이 true인 경우 필수 | 임계치 설정 |
| mba_scale_in.rules.duration | Body | Integer | mba_scale_in.enable 설정이 true인 경우 필수 | 임계 영역 유지 시간 (2 ~ 60)분 |

##### 지표 설정 목록

| 시스템 자원 | 설정값 |
| --- | --- |
| CPU 사용률 | GROUP_CPU_USAGE |
| 메모리 사용률 | GROUP_MEMORY_USAGE |
| 디스크 전송률 (읽기) | GROUP_DISK_READ_BYTES |
| 디스크 전송률 (쓰기) | GROUP_DISK_WRITE_BYTES |
| 네트워크 전송률 (송신) | GROUP_NETWORK_SENT_BYTES |
| 네트워크 전송률 (수신) | GROUP_NETWORK_RECV_BYTES |


<details><summary>활성화 예시</summary>
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


<details><summary>비활성화 예시</summary>
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


#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| uuid | Body | UUID | 노드 그룹 UUID |

<details><summary>예시</summary>
<p>

```json
{
    "uuid": "018b06c5-1293-4081-8242-167a1cb9f262"
}
```

</p>
</details>

---

### 클러스터 업그레이드

클러스터를 업그레이드합니다.

```
POST /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}/upgrade
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | 클러스터 UUID 또는 클러스터 이름 | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | 노드 그룹 UUID 또는 노드 그룹 이름<br>컨트롤 플레인 업그레이드 시에는 **default-master**로 지정 | 
| version | Body | String | O | Kubernetes 버전 |
| num_buffer_nodes | Body | Integer | X | 버퍼 노드 수. 최솟값: 0, 최댓값: (워커 노드 그룹당 최대 노드 수 쿼터-해당 워커 노드 그룹의 현재 노드 수), 기본값: 1 |
| num_max_unavailable_nodes | Body |  Integer | X | 최대 서비스 불가 노드 수. 최솟값: 1, 최댓값: 해당 워커 노드 그룹의 현재 노드 수, 기본값: 1 |

클러스터를 업그레이드하기 위해서는 컨트롤 플레인을 업그레이드한 후 워커 구성 요소를 업그레이드해야 합니다. 컨트롤 플레인과 워커 구성 요소 업그레이드는 노드 그룹 단위로 이루어집니다. 

* 컨트롤 플레인 구성 요소 업그레이드
    * 노드 그룹 이름을 **default-master**로 지정합니다.

* 워커 구성 요소 업그레이드
    * 업그레이드할 노드 그룹 이름을 지정합니다.


<details><summary>예시</summary>
<p>

```json
{
    "version": "v1.19.13"
}
```

</p>
</details>


#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| uuid | Body | UUID | 노드 그룹 UUID |

<details><summary>예시</summary>
<p>

```json
{
    "uuid": "018b06c5-1293-4081-8242-167a1cb9f262"
}
```

</p>
</details>

---

### 사용자 스크립트 변경하기

노드 그룹의 사용자 스크립트를 변경합니다.

```
POST /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}/userscript
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | 클러스터 UUID 또는 클러스터 이름 | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | 노드 그룹 UUID 또는 노드 그룹 이름<br>컨트롤 플레인 구성 요소 업그레이드 시에는 **default-master**로 지정 | 
| contents | Body | String | O | 사용자 스크립트 내용 |


<details><summary>예시</summary>
<p>

```json
{
    "contents": "user script contents here..."
}
```

</p>
</details>


#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| uuid | Body | UUID | 노드 그룹 UUID |

<details><summary>예시</summary>
<p>

```json
{
    "uuid": "018b06c5-1293-4081-8242-167a1cb9f262"
}
```

</p>
</details>

---

### 인스턴스 타입 변경하기

노드 그룹의 인스턴스 타입을 변경합니다.

```
PATCH /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | 클러스터 UUID 또는 클러스터 이름 | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | 노드 그룹 UUID 또는 노드 그룹 이름 | 
| type | Body | String | O | `flavor_id`로 설정 |
| flavor_id | Body | String | O | 인스턴스 타입 UUID |
| num_buffer_nodes | Body | Integer | X | 버퍼 노드 수. 최솟값: 0, 최댓값: (워커 노드 그룹당 최대 노드 수 쿼터-해당 워커 노드 그룹의 현재 노드 수), 기본값: 1 |
| num_max_unavailable_nodes | Body |  Integer | X | 최대 서비스 불가 노드 수. 최솟값: 1, 최댓값: 해당 워커 노드 그룹의 현재 노드 수, 기본값: 1 |


<details><summary>예시</summary>
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


#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| uuid | Body | UUID | 노드 그룹 UUID |

<details><summary>예시</summary>
<p>

```json
{
    "uuid": "018b06c5-1293-4081-8242-167a1cb9f262"
}
```

</p>
</details>

---

### 노드 그룹의 플로팅 IP 자동 할당 설정 변경하기

노드 그룹의 플로팅 IP 자동 할당 설정을 변경합니다.

```
PATCH /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | 클러스터 UUID 또는 클러스터 이름 | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | 노드 그룹 UUID 또는 노드 그룹 이름 | 
| type | Body | String | O | `fip_auto_bind`로 설정 |
| fip_auto_bind_update_info | Body | Object | O | 플로팅 IP 자동 할당 설정 정보 객체 |
| fip_auto_bind_update_info.fip_auto_bind_enable | Body | Boolean | O | 기능 활성화 여부 (true/false) |
| fip_auto_bind_update_info.fip_bind_subnet | Body | String | X | 플로팅 IP가 연결되는 네트워크 인터페이스의 서브넷. <br> 연결할 서브넷은 반드시 클러스터의 기본 서브넷이거나 노드 그룹의 추가 서브넷에 포함되어 있어야 함 |
| fip_auto_bind_update_info.fip_selector | Body | String | X | 노드에 할당할 플로팅 IP를 선별하기 위한 식별자 |


<details><summary>예시</summary>
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


#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| uuid | Body | UUID | 노드 그룹 UUID |

<details><summary>예시</summary>
<p>

```json
{
    "uuid": "018b06c5-1293-4081-8242-167a1cb9f262"
}
```

</p>
</details>

---

### 노드 그룹의 Kubernetes 레이블 설정 변경하기

노드 그룹의 Kubernetes 레이블 설정을 변경합니다.

```
PATCH /v1/clusters/{CLUSTER_ID_OR_NAME}/nodegroups/{NODEGROUP_ID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | 클러스터 UUID 또는 클러스터 이름 | 
| NODEGROUP_ID_OR_NAME | URL | UUID or String | O | 노드 그룹 UUID 또는 노드 그룹 이름 | 
| type | Body | String | O | `k8s_node_labels`로 설정 |
| k8s_node_labels | Body | Object | O | Kubernetes 레이블 키,값 쌍으로 구성된 설정 정보 객체. 최대 20개까지 설정 가능 |


<details><summary>예시</summary>
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


#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| uuid | Body | UUID | 노드 그룹 UUID |

<details><summary>예시</summary>
<p>

```json
{
    "uuid": "018b06c5-1293-4081-8242-167a1cb9f262"
}
```

</p>
</details>

---

## 애드온 관리 기능

### NHN Cloud에서 제공하는 애드온 유형 보기
NHN Cloud에서 제공하는 애드온 유형을 확인할 수 있습니다.

```
GET /v1/addon_types/${ADDON_TYPE_UUID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| ADDON_TYPE_UUID_OR_NAME | URL | UUID or String | O | 애드온 유형의 UUID 혹은 이름 |

#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| uuid | Body | UUID | 애드온 유형 UUID |
| name | Body | String | 애드온 유형 이름 |
| mandatory | Body | boolean | 필수 여부 |

<details><summary>예시</summary>
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

### NHN Cloud에서 제공하는 애드온 유형 목록 보기
NHN Cloud에서 제공하는 애드온 유형의 목록을 확인할 수 있습니다.

```
GET /v1/addon_types/
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청
| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |

#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| addon_types | Body | List of object | 애드온 유형 정보의 목록 |

<details><summary>예시</summary>
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

### NHN Cloud에서 제공하는 애드온 보기
NHN Cloud에서 제공하는 애드온을 확인할 수 있습니다.

```
GET /v1/addons/{ADDON_UUID}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| ADDON_UUID | URL | UUID | O | 애드온 UUID |
| tokenId | Header | String | O | 토큰 ID |


#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| uuid | Body | UUID | 애드온 유형 UUID |
| type | Body | String | 애드온 유형 이름 |
| version | Body | String | 애드온 버전 |
| name | Body | String | 애드온 이름 |
| deploy_target | Body | String | (내부 사용 용도) 애드온 배포 유형 |
| k8s_min_version | Body | String | (내부 사용 용도) 지원 최소 k8s 버전 |
| k8s_max_version | Body | String | (내부 사용 용도) 지원 최대 k8s 버전 |
| description | Body | String | 애드온 설명 |
| option_schemas | Body | List of object | 옵션 정의 목록 |
| option_schemas.name | Body | String | 옵션의 이름 |
| option_schemas.data_type | Body | String | 옵션 데이터 타입. `STRING`, `INTEGER`, `SELECT` 중 하나 |
| option_schemas.default | Body | String | 옵션 기본값 |
| option_schemas.updatable | Body | Boolean | 옵션 변경 가능 여부 |
| option_schemas.mandatory | Body | Boolean | 필수 여부  |
| option_schemas.choices | Body | List of String | 선택 가능한 값의 목록 |


<details><summary>예시</summary>
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

### NHN Cloud에서 제공하는 애드온 목록 보기
NHN Cloud에서 제공하는 애드온 목록을 확인할 수 있습니다.

```
GET /v1/addons/
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |

#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| addons | Body | List of object | 애드온 정보의 목록 |


<details><summary>예시</summary>
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

### 클러스터에 설치된 애드온 보기
클러스터에 설치된 애드온을 확인할 수 있습니다.

```
GET /v1/clusters/{CLUSTER_ID_OR_NAME}/addons/{ADDON_UUID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | 클러스터 UUID 또는 클러스터 이름 |
| ADDON_UUID_OR_NAME | URL | UUID or String | O | 애드온 UUID 또는 애드온 이름 |


#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| uuid | Body | UUID | 애드온 유형 UUID |
| project_id | Body | String | 프로젝트 ID |
| cluster_uuid | Body | UUID | 클러스터 UUID |
| cluster_name | Body | String | 클러스터 이름 |
| type | Body | String | 애드온 유형 이름 |
| version | Body | String | 애드온 버전 |
| options | Body | Object | 애드온 별 옵션 |
| name | Body | String | 애드온 이름 |
| status | Body | String | 애드온 상태 |
| status_reason | Body | String | 애드온 상태 이유 |
| scope | Body | String | 적용 범위 |
| target_uuid | Body | UUID | 적용 대상 UUID |
| created_at | Body | String | 생성 시간(UTC) |
| updated_at | Body | String | 최근 업데이트 시간(UTC) |

<details><summary>예시</summary>
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

### 클러스터에 설치된 애드온 목록 보기
클러스터에 설치된 애드온 목록을 확인할 수 있습니다.

```
GET /v1/clusters/{CLUSTER_ID_OR_NAME}/addons/
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | 클러스터 UUID 또는 클러스터 이름 |

#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| addons | Body | List of Object | 설치된 애드온 정보의 목록 |

<details><summary>예시</summary>
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

### 클러스터에 애드온 설치
클러스터에 애드온을 설치합니다.

```
POST /v1/clusters/{CLUSTER_ID_OR_NAME}/addons/
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | 클러스터 UUID 또는 클러스터 이름 |
| name | Body | String | O | 애드온 이름 |
| version | Body | String | O | 애드온 버전 |
| resolve_conflicts | Body | String | O | 충돌 옵션. `none`, `overwrite`, `preserve` 중 하나 |


<details><summary>예시</summary>
<p>

```json
{"version": "1.8.4-nks1", "name": "coredns", "resolve_conflicts": "overwrite"}
```

</p>
</details>


#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| uuid | Body | UUID | 클러스터 UUID |

<details><summary>예시</summary>
<p>

```json
{
    "uuid": "6c1284e2-8ead-46a7-ace9-c19d6eec76b3"
}
```

</p>
</details>

---

### 클러스터에 애드온 업데이트
클러스터에 설치된 애드온을 업데이트합니다.

```
PATCH /v1/clusters/{CLUSTER_ID_OR_NAME}/addons/{ADDON_UUID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | 클러스터 UUID 또는 클러스터 이름 |
| ADDON_UUID_OR_NAME | URL | UUID or String | O | 애드온 UUID 또는 애드온 이름 |
| version | Body | String | O | 애드온 버전 |
| resolve_conflicts | Body | String | O | 충돌 옵션. `none`, `overwrite`, `preserve` 중 하나 |


<details><summary>예시</summary>
<p>

```json
{"version": "1.8.4-nks1", "resolve_conflicts": "none"}
```

</p>
</details>


#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| uuid | Body | UUID | 클러스터 UUID |

<details><summary>예시</summary>
<p>

```json
{
    "uuid": "6c1284e2-8ead-46a7-ace9-c19d6eec76b3"
}
```

</p>
</details>

---

### 클러스터에 애드온 제거
클러스터에 설치된 애드온을 제거합니다.

```
DELETE /v1/clusters/{CLUSTER_ID_OR_NAME}/addons/{ADDON_UUID_OR_NAME}
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
X-Auth-Token: {tokenId}
```

#### 요청

| 이름 | 종류 | 형식 | 필수 | 설명 |
|---|---|---|---|---|
| tokenId | Header | String | O | 토큰 ID |
| CLUSTER_ID_OR_NAME | URL | UUID or String | O | 클러스터 UUID 또는 클러스터 이름 |
| ADDON_UUID_OR_NAME | URL | UUID or String | O | 애드온 UUID 또는 애드온 이름 |

#### 응답

| 이름 | 종류 | 형식 | 설명 |
|---|---|---|---|
| uuid | Body | UUID | 클러스터 UUID |

<details><summary>예시</summary>
<p>

```json
{
    "uuid": "6c1284e2-8ead-46a7-ace9-c19d6eec76b3"
}
```

</p>
</details>



## 기타 기능

### 지원되는 Kubernetes 버전 보기

NHN Kubernetes Service(NKS)에서 지원하는 Kubernetes 버전을 조회합니다.

```
GET /v1/supports
Accept: application/json
Content-Type: application/json
OpenStack-API-Version: container-infra latest
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
| supported_k8s | Body | Object | 지원되는 Kubernetes 버전 객체 |
| supported_k8s."버전 이름" | Body | String | Kubernetes 버전의 유효성 여부(True/False) |
| supported_event_type."작업 타입"| Body | Object | 지원되는 작업 타입 객체(cluster_events/nodegroup_events) |
| supported_event_type."작업 타입"."작업 이름"| Body | Object | 작업 타입 및 설명 |

<details><summary>예시</summary>
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
        "CLUSTER_CREATE": "클러스터 생성",
        "CLUSTER_DELETE": "클러스터 삭제",
        "CLUSTER_HANDOVER": "클러스터 OWNER 변경",
        "CLUSTER_UPDATE_VM_AUTH_KEY": "키페어 업데이트",
        "NODEGROUP_CREATE": "노드 그룹 생성",
        "NODEGROUP_DELETE": "노드 그룹 삭제",
        "CLUSTER_RESIZE": "클러스터 크기 조정",
        "NODEGROUP_SCALE_OUT": "노드 증설",
        "NODEGROUP_SCALE_IN": "노드 감축",
        "NODEGROUP_UPDATE_FLAVOR": "인스턴스 타입 변경",
        "NODEGROUP_UPGRADE": "노드 그룹 업그레이드",
        "NODEGROUP_USERSCRIPT_UPDATE": "유저 스크립트 변경",
        "NODEGROUP_SET_CLUSTER_AUTOSCALER": "클러스터 오토스케일러 설정 변경",
        "NODEGROUP_SET_METRIC_BASE_AUTOSCALER": "지표 기반 오토스케일러 설정 변경",
        "NODEGROUP_METRIC_BASE_AUTOSCALER_SCALE_OUT": "임계치 기반 오토스케일러를 통한 노드 증설",
        "NODEGROUP_METRIC_BASE_AUTOSCALER_SCALE_IN": "임계치 기반 오토스케일러를 통한 노드 감축",
        "CLUSTER_API_EP_IPACL_UPDATE": "클러스터 API 엔드포인트 IP 접근 제어 변경",
        "NODEGROUP_NODE_ACTION_START_NODE": "워커 노드 시작",
        "NODEGROUP_NODE_ACTION_STOP_NODE": "워커 노드 중지",
        "CLUSTER_UPDATE_SGW": "클러스터 서비스 게이트웨이 변경",
        "CLUSTER_ROTATE_CERTIFICATE": "클러스터 인증서 갱신",
        "CLUSTER_UPDATE_NKS_REGISTRY": "NKS 레지스트리 활성화",
        "NODEGROUP_UPDATE_EXTRA_VOLUME": "추가 블록 스토리지 변경",
        "NODEGROUP_UPDATE_EXTRA_SECURITY_GROUP": "추가 보안 그룹 변경",
        "CLUSTER_UPDATE_K8S_ARGS": "Kubernetes 구성 요소 옵션 변경",
        "CLUSTER_UPDATE_OIDC_ARGS": "OIDC 설정 변경",
        "NODEGROUP_UPDATE_K8S_NODE_LABELS": "노드 그룹 Kubernetes 레이블 설정 변경",
        "CLUSTER_INSTALL_ADDON": "Addon 설치",
        "CLUSTER_UNINSTALL_ADDON": "Addon 제거",
        "CLUSTER_UPDATE_ADDON": "Addon 업데이트",
        "CLUSTER_UPDATE_CONTROL_PLANE_LOG": "컨트롤 플레인 로그 수집 업데이트",
        "NODEGROUP_UPDATE_FIP_AUTO_BIND": "노드 그룹 플로팅 IP 자동 할당 설정 변경",
        "K8S_API_NOT_WORKING": "kube-apiserver 정지",
        "ALL_NODES_NOT_READY": "모든 노드 정지 상태",
        "AUTO_HEALING": "오토 힐링"
    }
}
```

</p>
</details>

