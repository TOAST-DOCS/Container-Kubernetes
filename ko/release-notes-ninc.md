## Container > NHN Kubernetes Service(NKS) > 릴리스 노트

### 2026. 02. xx.

#### 플랫폼 버전 업데이트
* 1.202602.0이 추가되었습니다.
    * Kubernetes 호환 버전: v1.29–v1.33
    * 기능 추가
        * Kubernetes 테인트 설정 기능
        * Kubernetes 컴포넌트 설정 기능 max-pods 설정 지원
        * Secure Key Manager를 이용한 etcd 데이터 암호화 지원
        * CGroup v2 OS 이미지 지원
    * 기능 개선
        * 노드 및 노드 그룹 삭제 시 로드 밸런서 트래픽 유실 개선

#### 애드온 업데이트
* 다음 애드온이 추가되었습니다.
    * calico v3.30.2-nks2
    * coredns 1.8.4-nks2
    * metrics_server v0.4.4-nks2
    * cinder_csi_plugin v1.27.102-nks3
    * snapshot_controller v4.1.1-nks2
    * nfs_csi_plugin v1.0.1-nks2

#### 기능 추가
* CGroup이 v2로 설정된 OS 이미지를 지원합니다.
    * 2026년 3월 이후 배포되는 OS 이미지는 CGroup이 v2로 설정되어 있습니다.
* Kubernetes 컴포넌트 설정 기능으로 kubelet의 --max-pods을 설정할 수 있습니다.
* Kubernetes 테인트 설정 기능이 추가되었습니다.
* Secure Key Manager 서비스를 이용해 클러스터의 기밀 데이터를 암/복호화할 수 있습니다.

### 2025. 11. 28.

#### 서비스 지원 정책 변경
* NKS의 Kubernetes 버전 지원 정책이 변경됩니다.
    * 자세한 내용은 [사용 가이드](/Container/NKS/ko/version-guide-ninc)를 참고하세요.

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


### 2025. 01. 24.
#### 신규 서비스 출시
* 콘솔에서 Kubernetes 클러스터를 생성하고 관리할 수 있습니다.
