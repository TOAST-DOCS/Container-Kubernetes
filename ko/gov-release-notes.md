## Container > NHN Kubernetes Service(NKS) > 릴리스 노트

### 2023. 11. 30.

#### 기능 추가
* kubelet 사용자 정의 아규먼트 설정 기능이 추가되었습니다.
* 로드 밸런서 상세 옵션 설정으로 멤버 서브넷 설정 기능이 추가되었습니다.
* 로드 밸런서 상세 옵션 설정으로 keep-alive 타임아웃 값 설정 기능이 추가되었습니다.
* 커스텀 이미지를 워커 이미지로 활용하는 기능이 추가되었습니다.
    * 자세한 내용은 [사용자 가이드](/Container/NKS/ko/gov-user-guide/#_23)를 참고하세요.

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

#### 신규 서비스 출시
* 한국(평촌) 리전에서도 Kubernetes 서비스를 사용할 수 있습니다.

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

### 2023. 02. 07.

#### 기능 추가

* 클러스터 OWNER 변경 기능이 추가되었습니다.
    * 자세한 내용은 [사용 가이드](/Container/NKS/ko/gov-user-guide/#owner)를 참고하세요.
* Kubernetes v1.25.4를 지원합니다.
* 클러스터 생성 시 Kubernetes v1.21.6은 더 이상 지원하지 않습니다. 단, 사용 중인 클러스터에는 영향이 없습니다.
* 로드 밸런서의 리스너에 프록시 프로토콜(Proxy Protocol)을 설정할 수 있습니다.

### 2023. 01. 05.

#### 기능 추가

* 이미지 추가
    * Rocky Linux 8.6 - Container (2022.12)

### 2022. 12. 06.

#### 기능 개선

* 이미지 업데이트
    * 변경 사항
        * nvidia-device-plugin 버전이 450.156.00에서 450.191.01으로 변경되었습니다.
        * Docker 버전이 19.03에서 20.10으로 변경되었습니다.
    * 대상 이미지
        * Ubuntu Server 18.04.6 LTS - Container (2022.11.22)
        * CentOS 7.9 - Container (2022.11.22)

#### 기능 추가

* 노드 시작/중지 기능을 사용할 수 있습니다.
* 여러 가지 타입의 로드 밸런서를 생성할 수 있습니다.
* 클러스터 이름과 노드 그룹 이름을 각각 최대 32자로 설정해 생성할 수 있습니다.
* 이미지 추가
    * Debian 11.5 Bullseye - Container (2022.11.22)


### 2022. 10. 04.

#### 기능 추가

* Kubernetes v1.24.3을 지원합니다.
* 클러스터 생성 시 Kubernetes v1.20.12는 더 이상 지원하지 않습니다. 단, 사용 중인 클러스터에는 영향이 없습니다.


### 2022. 08. 02.

#### 기능 추가

* 노드 그룹 생성 후에도 사용자 스크립트를 변경할 수 있습니다.
* 사용자 스크립트 변경 API가 추가됐습니다.
* 워커 노드 그룹 업그레이드 시 최대 노드 수와 최대 서비스 불가 노드 수를 지정할 수 있습니다.

### 2022. 05. 31.

#### 기능 개선
* 내부 구조를 개선해 서비스의 성능과 안정성을 향상했습니다.


### 2022. 04. 05.

#### 기능 변경

* Kubernetes 서비스의 이름이 NHN Kubernetes Service(NKS)로 변경되었습니다.
* 기능 이름이 변경되었습니다.
    * 변경 전: 예약 스크립트
    * 변경 후: 사용자 스크립트
* GPU 워커 노드에서 사용하는 NVIDIA 드라이버가 업데이트되었습니다.
    * 기존 버전: 450.119.04
    * 변경 버전: 450.156.00
* 인스턴스 생성 시 Prometheus 호환 exporter가 자동으로 설치되지 않도록 변경했습니다.


#### 기능 추가

* 아래 Kubernetes 버전을 지원합니다.
    * v1.20.12
    * v1.21.6
    * v1.22.3
    * v1.23.3

* 아래 Kubernetes 버전은 클러스터 생성을 지원하지 않습니다. 단, 사용 중인 클러스터에는 영향이 없습니다.
    * v1.17.6
    * v1.18.19
    * v1.19.13

* 로드 밸런서의 리스너 프로토콜을 TERMINATED_HTTPS로 설정할 때 SSL 버전을 TLSv1.3으로 설정할 수 있습니다.

* LoadBalancer 타입의 서비스 객체 생성 시 리스너별 설정을 지원합니다.

* 이미지 추가
    * CentOS 7.8 - Container (2022.01.20)
    * Ubuntu Server 18.04.6 LTS - Container (2022.01.20)
        * 클러스터 생성 및 노드 그룹 생성 시 Ubuntu 워커 이미지를 사용할 수 있습니다.


### 2022. 02. 22.

#### 신규 서비스 출시
* 콘솔에서 Kubernetes 클러스터를 생성하고 관리할 수 있습니다.


