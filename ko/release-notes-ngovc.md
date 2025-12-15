## Container > NHN Kubernetes Service(NKS) > 릴리스 노트

### 2025. 12. xx. -> 배포 전에 수정 필요

#### 서비스 지원 정책 변경
* NKS의 Kubernetes 버전 지원 정책이 변경됩니다.
    * 자세한 내용은 [사용 가이드](/Container/NKS/ko/version-guide-ngovc)를 참고하세요.

#### 애드온 업데이트
* 다음 애드온이 추가되었습니다.
    * Calico CNI v3.30.2-nks1
    * Cinder CSI Plugin v1.27.101-nks1, v1.27.102-nks1
    * Metrics Server v0.4.4-nks1
    * Snapshot Controller v4.1.1-nks1
    * NFS CSI Plugin v1.0.1-nks1

#### 기능 추가
* Kubernetes v1.33.4를 지원합니다.
* 컨트롤 플레인과 워커 노드 그룹의 플랫폼 버전의 조회 및 업그레이드 기능이 추가되었습니다.
* 로드 밸런서 상세 옵션 설정에 상태 확인 포트 설정 기능이 추가되었습니다.
* 로드 밸런서 상세 옵션 설정에 상태 확인 호스트 헤더 설정 기능이 추가되었습니다.


### 2025. 08. 25.
#### 신규 서비스 출시
* 콘솔에서 Kubernetes 클러스터를 생성하고 관리할 수 있습니다.
