<!-- pre-align:aligned sig=c2b4f6b0a381 -->

<a id="container-nhn-kubernetes-servicenks-version-guide"></a>
## Container > NHN Kubernetes Service(NKS) > 버전 가이드 { #container-nhn-kubernetes-servicenks-version-guide }

<a id="cluster-version-management"></a>
## 클러스터 버전 관리 { #cluster-version-management }

NKS 클러스터는 클러스터 컨트롤 플레인과 워커 노드 그룹별로 Kubernetes 버전과 플랫폼 버전을 관리합니다. 두 버전의 차이는 다음과 같습니다.

<a id="kubernetes-version"></a>
### Kubernetes 버전 { #kubernetes-version }
- 업스트림 Kubernetes에서 정의하는 버전입니다.
- NKS 클러스터를 구성하는 Kubernetes 주요 구성 요소의 버전을 결정합니다.
- 주요 구성 요소: `kube-apiserver`, `kube-controller-manager`, `kube-scheduler`, `kubelet`, `kube-proxy`

<a id="cluster-version-management-platform-version"></a>
### 플랫폼 버전 { #cluster-version-management-platform-version }
- NKS 서비스 수준에서 정의하는 버전입니다.
- 클러스터를 구성하는 여러 구성 요소를 하나의 버전으로 정의해 관리합니다.
- 주요 구성 요소: `containerd`, `etcd` 등 컨트롤 플레인 및 워커 노드 주요 구성 요소, 각종 시스템 구성 요소 및 시스템 관리 도구 등

<a id="upgrade-targets-by-version-status"></a>
### 버전 상태에 따른 업그레이드 대상 { #upgrade-targets-by-version-status }

| Kubernetes 버전의 최신 여부 | 플랫폼 버전의 최신 여부 | 업그레이드 대상 |
|----------------------------|----------------------|----------------|
| X                          | X                    | Kubernetes 버전 및 플랫폼 버전 |
| X                          | O                    | Kubernetes 버전 |
| O                          | X                    | 플랫폼 버전 |
| O                          | O                    | 없음 |

> 컨트롤 플레인의 버전 정보는 **클러스터 조회** 화면에서, 워커 노드 그룹의 버전 정보는 각 **워커 노드 그룹 조회** 화면에서 확인할 수 있습니다.

<a id="support-policy"></a>
## Kubernetes 버전 지원 정책 { #support-policy }

<a id="available-cluster-versions"></a>
### 클러스터 생성 가능 버전 { #available-cluster-versions }
- NKS에서 릴리스한 Kubernetes 버전 중 최신 3개 버전으로 신규 클러스터를 생성할 수 있습니다.
- 신규 버전이 릴리스되면, 기존 생성 가능 버전 목록 중 가장 낮은 버전은 자동으로 제거됩니다.
- Kubernetes 버전의 패치 버전은 내부 정책에 따라 업데이트될 수 있습니다.

<a id="service-support-policy"></a>
### 서비스 지원 정책 { #service-support-policy }
안정적인 서비스 운영을 위해 Kubernetes 버전별 서비스 지원 정책을 적용합니다.

- NKS는 **Kubernetes 버전**을 기준으로 서비스 지원 여부가 결정됩니다.
- 서비스 지원 정책의 적용 기준은 NKS의 신규 Kubernetes 버전(`x.y`) 릴리스 시점입니다.
- NKS는 최신 Kubernetes 버전을 지속적으로 제공하며, 사용 중인 클러스터는 업그레이드를 통해 최신 상태를 유지할 수 있습니다.

| 서비스 지원 정책 | 적용 기준 | NKS 기능 동작 보장 | 클러스터 업그레이드 |
|-----------------|------------|------------------|------------------|
| **서비스 지원** | Kubernetes 버전 릴리스 후 약 14개월 미만 | 보장 | 가능 |
| **서비스 미지원** | Kubernetes 버전 릴리스 후 약 14개월 이상 | 보장 안 됨 | 단, 서비스 지원 종료 후 10개월간 업그레이드 지원 |

<a id="service-support-policy-example-lifecycle-of-version-v133-released-in-november-2025"></a>
#### 예시: 2025년 11월 릴리스된 v1.33 버전의 수명주기

| 구분 | 기간 | 주요 특징 |
|------|------|-----------|
| **서비스 지원** | 2025.11 ~ 2027.01.31 (약 14개월) | - 모든 기능 정상 동작 및 기술 지원 제공 |
| **서비스 미지원 (업그레이드 지원)** | 2027.02.1 ~ 2027.11.30 (약 10개월) | - 업그레이드를 제외한 기술 지원 불가 |
| **서비스 미지원 (지원 종료 - EOS)** | 2027.12.01 이후 | - 업그레이드를 포함한 기술 지원 불가 <br>- 신규 클러스터 생성 후 마이그레이션 필요 |

> ### 운영 팁
> - 각 버전은 릴리스 후 최대 약 24개월간 관리됩니다.
> - 서비스 미지원 단계에 진입하면 10개월 내 업그레이드를 완료하는 것이 좋습니다.
> - 지원 종료(EOS - End of Support) 상태에서는 신규 클러스터 생성 및 워크로드 이전(Self-Migration)이 필요합니다.


<a id="k8s-version-support-time-table"></a>
## Kubernetes 버전별 지원 일정 { #k8s-version-support-time-table }

> [안내] 2025년 11월부터 Kubernetes 버전 지원 정책이 변경됩니다.
> - v1.32까지는 기존 일정과 정책이 적용됩니다.
> - v1.33부터는 신규 정책이 적용됩니다.
> - 표의 날짜는 UTC+00:00 기준입니다.

<a id="new-policy-applied-version-v133-or-later"></a>
### 신규 정책 적용 버전 (v1.33 이상) { #new-policy-applied-version-v133-or-later }

| 버전 | 릴리스 | 서비스 지원 종료(업그레이드 지원) | 서비스 지원 종료(EOS) |
|------|--------|---------------------------------|---------------------|
| v1.33 | 2025.11 | 2027.01.31 | 2027.11.30 |
| v1.34 | 2026.05 | 2027.07.31 | 2028.05.31 |
| v1.35 | 2026.08 | 2027.10.30 | 2028.08.31 |

<a id="old-policy-applied-version-v132-or-earlier"></a>
### 기존 정책 적용 버전 (v1.32 이하) { #old-policy-applied-version-v132-or-earlier }

| 버전 | 릴리스 | 서비스 지원 종료(업그레이드 지원) | 서비스 지원 종료(EOS) |
|------|--------|---------------------------------|---------------------|
| v1.28.3 | 2024.02 | 2025.11.30 | 2025.11.30 |
| v1.29.3 | 2024.05 | 2026.02.28 | 2026.02.28 |
| v1.30.3 | 2024.08 | 2026.05.31 | 2026.05.31 |
| v1.31.4 | 2025.02 | 2026.08.31 | 2026.08.31 |
| v1.32.3 | 2025.05 | 2027.02.28 (예정) | 2027.02.28 (예정) |

<a id="platform-version"></a>
## 플랫폼 버전 { #platform-version }

<a id="platform-version-info"></a>
### 플랫폼 버전별 정보 { #platform-version-info }

| 버전 | 릴리스 시점 | Kubernetes 호환 버전 | 설명 |
|------|------------|--------------------|-----|
| 1.202505.0 | 2025.11 | v1.28–v1.32 | 플랫폼 버전이 적용되지 않은 클러스터를 나타냅니다. |
| 1.202511.0 | 2025.11 | v1.28–v1.33 | 플랫폼 버전 초기 릴리스 |
| 1.202511.1 | 2025.12 | v1.28–v1.33 | 헬스 체크 확인 포트 설정 오류 수정 |
| 1.202602.0 | 2026.02 | v1.29–v1.33 | 기능 추가<br>- Kubernetes 테인트 설정 기능 <br>- Kubernetes 컴포넌트 설정 기능 max-pods 설정 지원 <br>- Secure Key Manager를 이용한 etcd 데이터 암호화 지원 <br>- CGroup v2 OS 이미지 지원<br><br>기능 개선<br>- 노드 및 노드 그룹 삭제 시 로드 밸런서 트래픽 유실 개선 |
| 1.202602.1 | 2026.03 | v1.29–v1.33 | 기능 개선<br>- Service 객체로 생성된 LB의 안정성 강화 | 
| 1.202605.0 | 2026.05 | v1.30–v1.34 | 기능 개선<br>- 워커 노드 CGroup v1 → v2 마이그레이션 지원<br>- Kubernetes v1.34 클러스터의 `ImageVolume` feature gate 활성화<br>- kube-apiserver와 pod 간의 통신을 위한 konnectivity 지원<br>- etcd 업그레이드 지원 |
| 1.202608.0 | 2026.08 | v1.31–v1.35 | addon 설치/업데이트 실패 시 오류 메시지 표시 개선 |

<a id="platform-component-versions"></a>
### 플랫폼 버전별 주요 컴포넌트 버전 { #platform-component-versions }

| 플랫폼 버전 | etcd | containerd |
| :--- | :--- | :--- |
| **1.202505.0** | v3.4.16 | 1.6.21 |
| **1.202511.0** | v3.4.16 | 1.6.21 |
| **1.202511.1** | v3.4.16 | 1.6.21 |
| **1.202602.0** | v3.4.16 | 1.6.21 |
| **1.202605.0** | v3.5.29 | k8s v1.33 이하 : 1.7.27 </br> k8s v1.34 이상 : 2.2.1 |
| **1.202608.0** | v3.5.29 | k8s v1.33 이하 : 1.7.27 </br> k8s v1.34 이상 : 2.2.1 |

<a id="platform-version-cgroup-v2-support"></a>
### CGroup v2 OS 이미지 사용을 위한 플랫폼 버전 { #platform-version-cgroup-v2-support }
* 플랫폼 버전 1.202602.0 미만인 클러스터는 CGroup 버전이 v2로 설정된 OS 이미지를 사용할 수 없습니다.
* OS 이미지의 릴리스 날짜에 따라 설정된 CGroup 버전이 다릅니다.
  * 2026/03/10 이전 릴리스 이미지: CGroup v1
  * 2026/03/10 이후 릴리스 이미지: CGroup v2
* 따라서 플랫폼 버전 1.202602.0 미만인 클러스터는 노드 그룹 추가 시 사용할 수 있는 이미지가 없을 수 있습니다. 
* CGroup v2인 OS 이미지를 사용하기 위해서는 플랫폼 버전을 1.202602.0 이상으로 업그레이드해야 합니다.
