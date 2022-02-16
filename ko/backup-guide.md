## Container > NHN Kubernetes Service(NKS) > 백업 가이드

## 개요

NHN Kubernetes Service(NKS)의 클러스터의 백업이 필요한 경우 Velero 플러그인을 사용하여 Object Storage에 백업을 할 수 있습니다.
Velero 서버는 `백업 클러스터`와 `복구 클러스터` 에 각각 설치해야하며, 동일한 오브젝트 스토리지를 사용해야 합니다.

* 용어 정리
    * 백업 클러스터 : 백업할 클러스터를 의미합니다.
    * 복구 클러스터 : 백업한 내용을 활용하여 복구되는 클러스터를 의미합니다.

자세한 내용은 [velero](https://velero.io/docs/v1.7/)를 참고하세요.

## Velero를 이용한 클러스터 백업 및 복구
### 사전 준비
Object Storage를 이용하기 위해서는 신원 서비스(Identity), 테넌트 ID, API 비밀번호가 필요합니다.

#### 신원 서비스(Identity) 및 테넌트 ID(Tenant ID) 확인
신원 서비스(Identity), 테넌트 ID, API 비밀번호는 Object Storage 서비스 페이지의 **API Endpoint 설정** 버튼을 클릭해 확인할 수 있습니다.

| 항목 | API Endpoint | 용도 |
| --- | --- | --- |
| 신원 서비스(Identity) | [https://api-identity.infrastructure.cloud.toast.com/v2.0](https://api-identity.infrastructure.cloud.toast.com/v2.0) | 인증 토큰 발급 |
| 테넌드 ID | 숫자 + 영문자로 구성된 32자 길이의 문자열 | 인증 토큰 발급 |

#### API 비밀번호 설정
API 비밀번호는 Object Storage 서비스 페이지의 **API Endpoint 설정** 버튼을 클릭해 설정할 수 있습니다.

1. **API Endpoint 설정** 버튼을 클릭합니다.
2. **API Endpoint 설정** 아래 **API 비밀 번호 설정** 입력 상자에 토큰 발급 시 사용할 비밀번호를 입력합니다.
3. **저장** 버튼을 클릭합니다.

### Velero 클라이언트 설치
Velero Github 저장소에서 클라이언트를 다운로드하여 클러스터 백업 및 복구 시 활용할 수 있습니다. 다운로드 받은 velero 클라이언트 명령을 실행하기 전에 백업 및 복구 클러스터의 kubeconfig 파일을 웹콘솔에서 다운로드 해야 하고, KUBECONFIG 환경 변수를 설정하여 백업 및 복구 대상 클러스터를 정확하게 지정해야 합니다.

#### Velero 클라이언트 다운로드

```
$ wget https://github.com/vmware-tanzu/velero/releases/download/v1.7.1/velero-v1.7.1-linux-amd64.tar.gz
```

#### 압축 해제

```
$ tar xzf velero-v1.7.1-linux-amd64.tar.gz
```

#### 위치 변경 또는 경로 지정

어느 경로에서든 velero 클라이언트를 실행할 수 있도록 환경 변수에 지정된 경로로 옮기거나, velero가 있는 경로를 환경 변수에 추가합니다.

* 환경 변수에 지정된 경로로 위치 변경

```
$ sudo mv velero-v1.7.1-linux-amd64/velero /usr/local/bin
```

* 환경 변수에 경로 추가

```
$ export PATH=$PATH:$(pwd)
```

### Velero 서버 설치
Velero 서버는 Helm 을 이용하여 설치합니다.

#### Helm 다운로드

```
$ curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
```

#### 권한 변경

다운로드한 파일은 기본적으로 실행 권한이 없습니다. 실행 권한을 추가해야 합니다.

```
$ chmod 700 get_helm.sh
```

#### Helm 설치

```
$ ./get_helm.sh
```

#### Helm Repository 추가

Velero 서버를 설치하기 위해서는 Helm Repository를 추가해야 합니다.

```
$ helm repo add vmware-tanzu https://vmware-tanzu.github.io/helm-charts
```

#### Velero 서버 설치

```
$ helm install velero vmware-tanzu/velero \
--namespace velero \
--create-namespace \
--set configuration.provider=community.openstack.org/openstack \
--set initContainers[0].name=velero-plugin-for-openstack \
--set initContainers[0].image=lirt/velero-plugin-for-openstack:v0.3.0 \
--set initContainers[0].volumeMounts[0].mountPath=/target \
--set initContainers[0].volumeMounts[0].name=plugins \
--set deployRestic=true \
--set configuration.defaultVolumesToRestic=true \
--set configuration.defaultResticPruneFrequency=0h1m0s \
--set configuration.backupStorageLocation.bucket={Container} \
--set configuration.backupStorageLocation.config.region={Region} \
--set configuration.backupStorageLocation.config.resticRepoPrefix=swift:{Container}:/restic \
--set configuration.extraEnvVars.OS_AUTH_URL={신원 서비스(Identity)} \
--set configuration.extraEnvVars.OS_TENANT_ID={테넌트 ID} \
--set configuration.extraEnvVars.OS_USERNAME={NHN Cloud 아이디} \
--set configuration.extraEnvVars.OS_PASSWORD={API 비밀번호} \
--set configuration.extraEnvVars.OS_REGION_NAME={Region} \
--set configuration.extraEnvVars.OS_DOMAIN_ID=default
```

| 항목 | 설명 |
| --- | --- |
| Container | Object Sotrage에서 사용하는 컨테이너 이름 |
| Region | 한국(판교) 리전: `KR1`<br>한국(평촌) 리전: `KR2` |
| 신원 서비스(Identity) | API Endpoint 설정의 신원 서비스(Identity) |
| 테넌트 ID | API Endpoint 설정의 테넌트 ID |
| NHN Cloud 아이디 | NHN Cloud 아이디 |
| API 비밀번호 | API Endpoint 설정에 입력한 API 비밀번호 |


### 클러스터 백업
클러스터 백업은 `velero backup create` 명령어로 설정할 수 있습니다.

```
$ export KUBECONFIG={백업 클러스터의 kubeconfig 파일}
$ velero backup create {name} --exclude-namespaces kube-system,velero
```

* name은 원하는 백업 이름으로 지정합니다.
* 클러스터 백업 시 Resource Filtering을 할 수 있습니다. 자세한 내용은 [resource-filtering](https://velero.io/docs/v1.7/resource-filtering/)을 참고하세요.

> [주의]
> `kube-system`, `velero`와 같이 백업이 필요하지 않은 namespace는 제외해야 합니다.
> 백업에 포함되는 경우 복구 시 문제가 발생할 수 있습니다.

클러스터 백업 상태 확인은 `velero backup get` 명령어로 확인할 수 있습니다.

```
$ velero backup get
NAME         STATUS      ERRORS   WARNINGS   CREATED                         EXPIRES   STORAGE LOCATION   SELECTOR
my-backup    Completed   0        0          2022-02-09 10:13:44 +0900 KST   29d       default            <none>
```

* 백업된 정보는 Object Storage 서비스 페이지에서 확인할 수 있습니다.

### 클러스터 복구
클러스터 백업/복구는 `velero restore create` 명령어로 설정할 수 있습니다.

```
$ export KUBECONFIG={복구 클러스터의 kubeconfig 파일}
$ velero restore create --from-backup {name}
```

### 예시
#### 클러스터 백업/복구 예시
* `백업 클러스터`에서 `velero backup create` 명령어를 사용하여 백업합니다.

```
$ velero backup create my-backup --exclude-namespaces kube-system,velero
```

* `velero backup get` 명령어를 이용하여 백업 상태를 확인합니다.

```
$ velero backup get
NAME         STATUS      ERRORS   WARNINGS   CREATED                         EXPIRES   STORAGE LOCATION   SELECTOR
my-backup    Completed   0        0          2022-02-09 13:23:13 +0900 KST   29d       default            <none>
```

* `복구 클러스터`에서 `velero restore create`명령어를 사용하여 복구합니다.

```
$ velero restore create --from-backup my-backup
```

* kubectl을 사용하여 복구 정보를 확인합니다.

```
$ kubectl get pod --all-namespaces
```

#### 주기적 백업 설정 예시

`velero schedule create` 명령어로 주기적으로 백업할 수 있습니다. 자세한 내용은 [schedule-a-backup](https://velero.io/docs/v1.7/backup-reference/#schedule-a-backup)참고하세요.

* `백업 클러스터`에서 `velero schedule create`명령어를 사용하여 일정 시간 간격으로 백업하도록 설정합니다. (예시는 10분 간격)

```
$ velero schedule create my-schedule --schedule="*/10 * * * *" --exclude-namespaces kube-system,velero
```

* `velero backup get`명령어를 사용하여 일정 시간 간격으로 백업되는 것을 확인할 수 있습니다.

```
$ velero backup get
NAME                          STATUS      ERRORS   WARNINGS   CREATED                         EXPIRES   STORAGE LOCATION   SELECTOR
my-schedule-20220209044049    Completed   0        0          2022-02-09 13:40:49 +0900 KST   29d       default            <none>
my-schedule-20220209043115    Completed   0        0          2022-02-09 13:31:15 +0900 KST   29d       default            <none>
```
