## Container > NHN Kubernetes Service(NKS) > 릴리스 노트

### 2023. 12. 05.

#### 기능 추가
* kubelet 사용자 정의 아규먼트 설정 기능이 추가되었습니다.
* 로드 밸런서 상세 옵션 설정으로 멤버 서브넷 설정 기능이 추가되었습니다.
* 로드 밸런서 상세 옵션 설정으로 keep-alive 타임아웃 값 설정 기능이 추가되었습니다.

#### 이미지 업데이트
* 클러스터 및 노드 그룹 생성 시 사용 가능한 신규 이미지가 추가되었습니다.
    * 대상 이미지
        * Ubuntu Server 22.04.3 LTS - Container (2023.11.21)
* 기존 이미지가 GPU 워커 노드 기능을 지원하도록 변경되었습니다.
    * 대상 이미지
        * Debian 11.8 Bullseye - Container (2023.11.21)
* 이미지에 설치된 GPU 드라이버의 버전이 변경되었습니다.
    * 변경 사항
        * nvidia-device-plugin 버전이 470.199.02에서 535.104.12로 변경되었습니다.
        * cuda 버전이 11.4에서 12.2로 변경되었습니다.
        * nvidia-mig-manager 버전이 0.5.3에서 0.5.5로 변경되었습니다.
    * 대상 이미지
        * CentOS 7.9 - Container (2023.11.21)
        * Rocky Linux 8.8 - Container (2023.11.21)
        * Ubuntu Server 20.04.6 LTS - Container (2023.11.21)

### 2023. 08. 31.

#### 기능 추가

* Kubernetes v1.27.3을 지원합니다.
* 클러스터 생성 시 Kubernetes API 엔드포인트의 속성을 설정할 수 있습니다.
* 클러스터와 노드 그룹 목록 조회 화면에서 좀 더 상세한 상태 정보를 제공합니다.

#### 기능 개선
* 클러스터 및 노드 그룹 생성 시 사용하는 이미지의 배포판 버전이 변경되었습니다.
    * 변경 전
        * Rocky Linux 8.7 - Container (2023.07.25)
    * 변경 후
        * Rocky Linux 8.8 - Container (2023.08.22)

#### 이미지 업데이트 
* 변경 사항
    * nvidia-device-plugin 버전이 470.182.03에서 470.199.02로 변경되었습니다.
    * dcgm 버전이 3.1.7에서 3.1.8로 변경되었습니다.
    * nvidia-mig-manager 버전이 0.5.2에서 0.5.3로 변경되었습니다.
* 대상 이미지
    * CentOS 7.9 - Container (2023.08.22)
    * Rocky Linux 8.8 - Container (2023.08.22)
    * Ubuntu Server 20.04.6 LTS - Container (2023.08.22)

### 2023. 07. 19.

#### 이미지 업데이트
* 노드 그룹 생성 시 일부 이미지에서 iptables 커널 모듈이 정상적으로 초기화되지 않는 문제를 수정했습니다.
    * 문제 이미지: Rocky Linux 8.7 - Container (2023.05.25)
    * 해결 이미지: Rocky Linux 8.7 - Container (2023.07.25)
* GPU 노드 그룹 생성 시 일부 이미지에서 nvidia-container-runtime 모듈이 배포되지 않는 문제를 수정했습니다.
    * 문제 이미지: CentOS 7.9 - Container (2023.05.25)
    * 해결 이미지: CentOS 7.9 - Container (2023.07.25)


### 2023. 06. 06.

#### 기능 추가

* Kubernetes v1.26.3을 지원합니다.

#### 기능 개선

* 클러스터 및 노드 그룹 생성 시 사용하는 이미지의 배포판 버전이 변경되었습니다.
    * 변경 전
        * Ubuntu Server 18.04.6 LTS - Container (2023.03.21)
        * Rocky Linux 8.6 - Container (2023.03.21)
    * 변경 후
        * Ubuntu Server 20.04.6 LTS - Container (2023.05.25)
        * Rocky Linux 8.7 - Container (2023.05.25)


* 이미지 업데이트
    * 변경 사항
        * nvidia-device-plugin 버전이 450.216.04에서 470.182.03으로 변경되었습니다.
        * cuda 버전이 11.0.3에서 11.4로 변경되었습니다.
        * dcgm 버전이 3.0.0에서 3.1.7로 변경되었습니다.
        * Docker 버전이 20.10.23에서 20.10.24로 변경되었습니다.
    * 대상 이미지
        * CentOS 7.9 - Container (2023.05.25)
        * Rocky Linux 8.7 - Container (2023.05.25)
        * Ubuntu Server 20.04.6 LTS - Container (2023.05.25)

### 2023. 04. 04.

#### 기능 추가

* 클러스터 CNI 변경 기능이 추가되었습니다.
    * 자세한 내용은 [사용 가이드](/Container/NKS/ko/gov-user-guide/#cni)를 참고하세요.
* 노드 그룹의 인스턴스 타입을 변경할 수 있습니다.
* 콘솔에서 Kubernetes 자원 조회 기능을 사용할 수 있습니다.

#### 기능 변경

* NKS API 주소 도메인이 변경되었습니다.
    * 기존: https://gov-api-kubernetes.infrastructure.cloud.toast.com
    * 변경: https://kr1-api-kubernetes-infrastructure.gov-nhncloudservice.com

#### 기능 개선

* 이미지 업데이트
    * Ubuntu Server 18.04.6 LTS - Container (2023.02.21)
    * Debian 11.6 Bullseye - Container (2023.02.21)
    * Rocky Linux 8.6 - Container (2023.02.21)

### 2023. 02. 13.
#### 신규 서비스 출시
* 콘솔에서 Kubernetes 클러스터를 생성하고 관리할 수 있습니다.
