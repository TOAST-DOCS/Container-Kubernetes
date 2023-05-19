## Container > NHN Kubernetes Service(NKS) > 개요
여기에서는 Kubernetes(쿠버네티스)가 무엇인지 간략히 알아보고, NHN Cloud에서 제공하는 NHN Kubernetes Service(NKS)를 살펴봅니다.

## Kubernetes
Kubernetes는 컨테이너화된 워크로드와 서비스를 관리할 수 있는 오픈소스 플랫폼입니다. Kubernetes는 다음과 같은 기능을 제공합니다.

* 서비스 디스커버리와 로드 밸런싱
* 스토리지 오케스트레이션
* 자동화된 롤아웃과 롤백
* 자동화된 빈 패킹(bin packing)
* 자동화된 복구(self-healing)
* 시크릿과 구성 관리

자세한 내용은 아래 Kubernetes 문서를 참고하시기 바랍니다.

* [쿠버네티스란 무엇인가?](https://kubernetes.io/ko/docs/concepts/overview/)

## Kubernetes 클러스터
Kubernetes 클러스터는 서로 연결되어 하나의 유닛처럼 동작하는 컴퓨터 클러스터입니다. Kubernetes가 제공하는 기능은 클러스터 단위로 동작하며, 클러스터 단위로 설정할 수 있습니다.

### 구성
Kubernetes 클러스터는 마스터와 노드로 구성됩니다.

#### 마스터
마스터는 클러스터 관리를 담당합니다. 애플리케이션을 스케줄링하거나 스케일링, 업데이트하는 등 클러스터 내의 모든 활동을 관리합니다. 일반적으로 마스터의 구성 요소들은 별도의 머신(가상 머신 혹은 물리 머신)에서 구동됩니다. 고가용성 보장을 위해 한 클러스터에 다수의 마스터를 구성할 수도 있습니다.

#### 노드
노드는 사용자의 애플리케이션이 구동되는 워커 머신입니다. 하나의 클러스터에는 다수의 노드가 존재할 수 있습니다. 노드는 마스터와 연결해야 동작됩니다. 마스터의 애플리케이션 구동, 정지 등의 명령에 따라 그대로 수행합니다.


## NHN Kubernetes Service(NKS)
NHN Kubernetes Service(NKS)는 클라우드에서 Kubernetes를 올바르고 안전하게 구동할 수 있게 Kubernetes 클러스터를 생성하고 관리할 수 있는 서비스입니다. 사용자는 웹 콘솔을 이용해 NHN Cloud에 꼭 맞는 Kubernetes 클러스터를 만들고 관리할 수 있습니다. 안전하고 효율적으로 운영할 수 있게 마스터는 NHN Cloud에서 관리하고, 사용자는 노드와 서비스, Pod(파드) 등을 관리합니다.

NHN Kubernetes Service(NKS)의 주요 기능은 다음과 같습니다.

* NHN Cloud에 꼭맞는 Kubernetes 클러스터 생성과 관리
    * NHN Cloud의 기반 서비스와 연동
    * 로드 밸런서를 이용한 서비스 공개
    * 블록 스토리지와 연동한 퍼시스턴트 볼륨(Persistent Volume, PV) 지원

* 고가용성을 보장하는 마스터 관리

* 웹 콘솔을 이용한 쉬운 조작
    * 클러스터 생성, 삭제, 조회
    * 노드 그룹 생성, 삭제, 조회
    * kubectl 설정 지원
