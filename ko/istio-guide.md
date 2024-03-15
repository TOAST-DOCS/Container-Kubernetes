## Container > NHN Kubernetes Service(NKS) > 외부 서비스 연동 가이드 > Istio

### Istio
서비스 메시(Service Mesh)는 분산 시스템에서 서비스 간 통신을 제어하고, 효율적으로 관리할 수 있도록 하는 데 특화된 마이크로서비스를 위한 인프라 계층입니다.
Istio는 많이 사용되는 오픈소스 서비스 메시 중 하나입니다. 이 예제에서는 클러스터에 demo 프로파일의 Istio 서비스를 배포한 후 동작하는 것을 보여줍니다. Istio에 대한 자세한 내용은 [Istio](https://istio.io/latest/about/service-mesh/)를 참고하세요.


### Istio 배포를 위한 사전 준비
배포하려는 Istio 버전 및 클러스터에 맞는 올바른 레지스트리 정보를 확인 후 변수에 지정합니다. 설정한 변수는 이후 배포 과정에 사용됩니다.

```
$ VERSION="{다운로드하려는 istio 버전}"
$ REGISTRY="{리전 별 레지스트리}"
```

#### Istio 버전 별 지원되는 쿠버네티스 버전

| 버전 | 지원되는 쿠버네티스 버전 |
| --- | --- |
| 1.20.0 | 1.25, 1.26, 1.27, 1.28 |
| 1.19.0 | 1.25, 1.26, 1.27, 1.28 |

> [참고]
> NKS(NHN Kubernetes Service)는 Istio의 모든 버전을 제공하지 않습니다. 제공되지 않는 버전을 사용하고자 하는 경우 사용자가 직접 Istio를 구성해야합니다. 지원되는 Istio 버전에 관한 정보는 [istio 지원정책](https://istio.io/latest/docs/releases/supported-releases/)을 참고하세요.


#### 리전 및 인터넷 환경 별 레지스트리 정보
| 리전 | 인터넷 연결 | 레지스트리 |
| --- | --- | --- |
| 한국(판교) 리전 | O | dfe965c3-kr1-registry.container.nhncloud.com/container_service/istio |
| | X | private-dfe965c3-kr1-registry.container.nhncloud.com/container_service/istio |
| 한국(평촌) 리전 | O | 6e7f43c6-kr2-registry.container.cloud.toast.com/container_service/istio |
| | X | private-6e7f43c6-kr2-registry.container.cloud.toast.com/container_service/istio |

> [참고]
> 폐쇄망 환경에 구성된 클러스터는 인터넷에 연결되어 있지 않기 때문에 폐쇄망 환경에서 NCR 레지스트리를 사용하기 위해서는 Private URI를 사용하기 위한 환경 구성이 필요합니다. Private URI 사용법에 대한 자세한 내용은 [NHN Cloud Container Registry(NCR) 사용자 가이드](/Container/NCR/ko/user-guide/#private-uri)를 참고하세요.


#### ORAS 명령줄 도구 설치
아티팩트 pull을 위해 ORAS(OCI Registry As Storage) 명령줄 도구를 다운로드합니다. ORAS는 OCI 레지스트리에서 OCI 아티팩트를 push 및 pull 하는 방법을 제공하는 툴입니다.
아래 커맨드를 실행하여 ORAS 명령줄 도구를 설치합니다. ORAS 명령줄 도구의 자세한 사용법은 [ORAS docs](https://oras.land/docs/)를 참고하세요.

```
$ ORAS_VERSION="1.1.0"
$ curl -LO "https://github.com/oras-project/oras/releases/download/v${VERSION}/oras_${ORAS_VERSION}_linux_amd64.tar.gz"
$ mkdir -p oras-install/
$ tar -zxf oras_${ORAS_VERSION}_*.tar.gz -C oras-install/
$ sudo mv oras-install/oras /usr/local/bin/
$ rm -rf oras_${ORAS_VERSION}_*.tar.gz oras-install/
$ export PATH="/usr/local/bin:$PATH"
```


### Istio 설치

#### 1. ORAS 명령줄 도구를 사용하여 istioctl을 다운로드 및 설치합니다.
istioctl은 Istio가 제공하는 서비스 메시 배포를 디버깅하고 진단할 수 있게 해주는 구성 명령줄 유틸리티로 Istio 배포 구성 옵션 설정 등 다양한 기능을 제공합니다. istioctl에 대한 자세한 설명은 [istioctl](https://istio.io/latest/docs/reference/commands/istioctl/)을 참고하세요. 

배포하려는 Istio 버전에 맞는 istioctl 툴을 다운로드해야합니다. 아래 명령어를 실행하여 istioctl을 다운로드합니다.
```
$ oras pull ${REGISTRY}/istioctl-tool:${VERSION}
$ tar -zxvf istio-${VERSION}-linux-amd64.tar.gz
$ cd istio-${VERSION}
$ export PATH=$PWD/bin:$PATH
```

#### 2. istioctl을 사용해 Istio 구성 요소를 배포합니다.
프로파일은 Istio 컨트롤 플레인과 데이터 플레인의 사이드카에 대한 사용자 정의 배포 기능을 제공합니다. 설정된 프로파일에 따라 배포 시 활성화되는 구성 요소가 달라집니다. 사용자는 클러스터의 환경 구성에 따라 기본 제공되는 프로파일을 사용하거나 특정 요구에 맞춰 구성을 사용자 정의할 수 있습니다. Istio 프로파일에 대한 자세한 설명은 [Istio 설치 구성 프로파일](https://istio.io/latest/docs/setup/additional-setup/config-profiles/)을 참고하세요. 이 예제에서는 기본으로 제공되는 demo 프로파일을 사용하여 배포합니다.

`istioctl install` 명령어를 통해 Istio 구성 요소를 배포합니다. 프로파일 생략시에는 default 프로파일이 적용됩니다. 아래는 `istioctl install` 명령어를 사용하여 istio 구성 요소를 배포하는 예제입니다.
```
$ istioctl install --set profile=demo --set hub=${REGISTRY} -y
✔ Istio core installed
✔ Istiod installed
✔ Egress gateways installed
✔ Ingress gateways installed
✔ Installation complete
```

매니페스트를 생성하여 배포하려면 `istioctl manifest generate` 명령어를 사용합니다. 아래는 `istioctl manifest generate` 명령어를 사용하여 istio 구성 요소를 배포하는 예제입니다.
```
$ istioctl manifest generate --set profile=demo --set hub=${REGISTRY} > istio-manifest-demo.yaml
$ kubectl apply -f istio-manifest-demo.yaml
customresourcedefinition.apiextensions.k8s.io/authorizationpolicies.security.istio.io created
customresourcedefinition.apiextensions.k8s.io/destinationrules.networking.istio.io created
customresourcedefinition.apiextensions.k8s.io/envoyfilters.networking.istio.io created
customresourcedefinition.apiextensions.k8s.io/gateways.networking.istio.io created
customresourcedefinition.apiextensions.k8s.io/istiooperators.install.istio.io created
customresourcedefinition.apiextensions.k8s.io/peerauthentications.security.istio.io created
customresourcedefinition.apiextensions.k8s.io/proxyconfigs.networking.istio.io created
customresourcedefinition.apiextensions.k8s.io/requestauthentications.security.istio.io created
customresourcedefinition.apiextensions.k8s.io/serviceentries.networking.istio.io created
customresourcedefinition.apiextensions.k8s.io/sidecars.networking.istio.io created
customresourcedefinition.apiextensions.k8s.io/telemetries.telemetry.istio.io created
customresourcedefinition.apiextensions.k8s.io/virtualservices.networking.istio.io created
customresourcedefinition.apiextensions.k8s.io/wasmplugins.extensions.istio.io created
customresourcedefinition.apiextensions.k8s.io/workloadentries.networking.istio.io created
customresourcedefinition.apiextensions.k8s.io/workloadgroups.networking.istio.io created
serviceaccount/istio-egressgateway-service-account created
serviceaccount/istio-ingressgateway-service-account created
serviceaccount/istio-reader-service-account created
serviceaccount/istiod created
clusterrole.rbac.authorization.k8s.io/istio-reader-clusterrole-istio-system created
clusterrole.rbac.authorization.k8s.io/istiod-clusterrole-istio-system created
clusterrole.rbac.authorization.k8s.io/istiod-gateway-controller-istio-system created
clusterrolebinding.rbac.authorization.k8s.io/istio-reader-clusterrole-istio-system created
clusterrolebinding.rbac.authorization.k8s.io/istiod-clusterrole-istio-system created
clusterrolebinding.rbac.authorization.k8s.io/istiod-gateway-controller-istio-system created
validatingwebhookconfiguration.admissionregistration.k8s.io/istio-validator-istio-system created
configmap/istio created
configmap/istio-sidecar-injector created
mutatingwebhookconfiguration.admissionregistration.k8s.io/istio-sidecar-injector created
deployment.apps/istio-egressgateway created
deployment.apps/istio-ingressgateway created
deployment.apps/istiod created
poddisruptionbudget.policy/istio-egressgateway created
poddisruptionbudget.policy/istio-ingressgateway created
poddisruptionbudget.policy/istiod created
role.rbac.authorization.k8s.io/istio-egressgateway-sds created
role.rbac.authorization.k8s.io/istio-ingressgateway-sds created
role.rbac.authorization.k8s.io/istiod created
rolebinding.rbac.authorization.k8s.io/istio-egressgateway-sds created
rolebinding.rbac.authorization.k8s.io/istio-ingressgateway-sds created
rolebinding.rbac.authorization.k8s.io/istiod created
service/istio-egressgateway created
service/istio-ingressgateway created
service/istiod created
```

구성 요소가 정상적으로 배포되었는지 확인합니다.
```
$ kubectl get all -n istio-system
NAME                                       READY   STATUS    RESTARTS   AGE
pod/istio-egressgateway-d859c887c-khf6r    1/1     Running   0          86s
pod/istio-ingressgateway-b8844867c-5nqjj   1/1     Running   0          86s
pod/istiod-8c7fbbdc8-xwjbt                 1/1     Running   0          90s


NAME                           TYPE           CLUSTER-IP      EXTERNAL-IP       PORT(S)                                                                      AGE
service/istio-egressgateway    ClusterIP      10.254.107.64   <none>            80/TCP,443/TCP                                                               86s
service/istio-ingressgateway   LoadBalancer   10.254.64.122   114.110.160.100   15021:31991/TCP,80:32023/TCP,443:32227/TCP,31400:32213/TCP,15443:30244/TCP   86s
service/istiod                 ClusterIP      10.254.110.81   <none>            15010/TCP,15012/TCP,443/TCP,15014/TCP                                        90s


NAME                                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/istio-egressgateway    1/1     1            1           86s
deployment.apps/istio-ingressgateway   1/1     1            1           86s
deployment.apps/istiod                 1/1     1            1           90s

NAME                                             DESIRED   CURRENT   READY   AGE
replicaset.apps/istio-egressgateway-d859c887c    1         1         1       86s
replicaset.apps/istio-ingressgateway-b8844867c   1         1         1       86s
replicaset.apps/istiod-8c7fbbdc8                 1         1         1       90s
```

### 어플리케이션 배포

#### 1. 네임스페이스에 레이블을 지정하기
Istio 사이드카 프록시를 사용하기 위해 파드가 생성될 네임스페이스에 레이블을 지정해야합니다. 레이블은 Istio 구성 요소를 배포할 네임스페이스를 식별하는데 사용됩니다. 레이블이 설정되어있는 네임스페이스에 파드가 배포되면 파드에 자동으로 Istio 사이드카 프록시가 배포됩니다.

아래는 default 네임스페이스에 레이블을 설정하는 예제입니다.
```
$ kubectl label namespace default istio-injection=enabled
namespace/default labeled
```

#### 2. 어플리케이션 구성 요소 배포하기
이 예제에서는 Istio에서 기본적으로 제공하는 Bookinfo 샘플 어플리케이션을 사용합니다. 샘플 어플리케이션은 productpage, details, reviews, ratings 4개의 마이크로서비스로 구성되어있습니다. Bookinfo 샘플 어플리케이션에 대한 자세한 내용은 [Istio Bookinfo Application](https://istio.io/latest/docs/examples/bookinfo/)을 참고하세요.

아래 명령어를 통해 어플리케이션을 배포합니다.
```
$ kubectl apply -f samples/bookinfo/platform/kube/bookinfo.yaml
service/details created
serviceaccount/bookinfo-details created
deployment.apps/details-v1 created
service/ratings created
serviceaccount/bookinfo-ratings created
deployment.apps/ratings-v1 created
service/reviews created
serviceaccount/bookinfo-reviews created
deployment.apps/reviews-v1 created
deployment.apps/reviews-v2 created
deployment.apps/reviews-v3 created
service/productpage created
serviceaccount/bookinfo-productpage created
deployment.apps/productpage-v1 created
```

구성 요소가 정상적으로 배포되었는지 확인합니다.
```
$ kubectl get svc,pods
NAME                  TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)    AGE
service/details       ClusterIP   10.254.91.224    <none>        9080/TCP   13m
service/kubernetes    ClusterIP   10.254.0.1       <none>        443/TCP    37d
service/productpage   ClusterIP   10.254.111.194   <none>        9080/TCP   13m
service/ratings       ClusterIP   10.254.232.169   <none>        9080/TCP   13m
service/reviews       ClusterIP   10.254.29.51     <none>        9080/TCP   13m

NAME                                 READY   STATUS    RESTARTS   AGE
pod/details-v1-5f4d584748-flpdl      2/2     Running   0          13m
pod/productpage-v1-564d4686f-z97zr   2/2     Running   0          13m
pod/ratings-v1-686ccfb5d8-g68rq      2/2     Running   0          13m
pod/reviews-v1-86896b7648-sv5vc      2/2     Running   0          13m
pod/reviews-v2-b7dcd98fb-vppzv       2/2     Running   0          13m
pod/reviews-v3-5c5cc7b6d-4hpr8       2/2     Running   0          13m
```

아래 명령어를 통해 어플리케이션이 정상적으로 동작하는지 확인합니다.
```
$ kubectl exec "$(kubectl get pod -l app=ratings -o jsonpath='{.items[0].metadata.name}')" -c ratings -- curl -sS productpage:9080/productpage | grep -o "<title>.*</title>"
<title>Simple Bookstore App</title>
```

### 서비스 노출

#### 인그레스 게이트웨이 구성하기
서비스를 노출하기 위해 Istio 인그레스 게이트웨이를 추가로 구성합니다.

아래 명령어를 통해 Istio 인그레스 게이트웨이 구성 요소를 배포합니다.
```
$ kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.yaml
gateway.networking.istio.io/bookinfo-gateway created
virtualservice.networking.istio.io/bookinfo created
```

`istioctl analyze` 명령어를 통해 인그레스 게이트웨이 구성에 문제가 없는지 확인합니다.
```
$ istioctl analyze
✔ No validation issues found when analyzing namespace: default.
```

#### 서비스 접근하기
노출된 서비스에 접근하기 위해 게이트웨이 URL 정보를 확인합니다. 게이트웨이 URL은 기본적으로 `인그레스 호스트:인그레스 IP`의 구성으로 되어 있습니다. 클러스터가 인터넷망 또는 폐쇄망 환경에 구성되었는지에 따라 게이트웨이 URL 정보를 확인하는 방법이 달라집니다. 인터넷망에 구성된 클러스터는 로드 밸런서의 `EXTERNAL-IP` 정보를 기반으로 게이트웨이 URL이 구성되며 폐쇄망 환경에 구성된 클러스터는 노드 포트를 기반으로 게이트웨이 URL이 구성됩니다.

인터넷망에 구성된 클러스터의 경우 아래 명령어 실행을 통해 게이트웨이 URL 정보를 확인할 수 있습니다.
```
$ export INGRESS_HOST=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
$ export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].port}')
$ export GATEWAY_URL=$INGRESS_HOST:$INGRESS_PORT

$ echo "$GATEWAY_URL"
114.110.160.79:80
```

폐쇄망에 구성된 클러스터의 경우 아래 명령어 실행을 통해 게이트웨이 URL 정보를 확인합니다.
```
$ export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].nodePort}')
$ export INGRESS_HOST=$(kubectl get po -l istio=ingressgateway -n istio-system -o jsonpath='{.items[0].status.hostIP}')
$ export GATEWAY_URL=$INGRESS_HOST:$INGRESS_PORT

$ echo "$GATEWAY_URL"
172.16.0.42:30745
```

게이트웨이 URL로 요청을 보내 서비스가 정상적으로 노출되었는지 확인합니다. 
```
$ curl http://114.110.160.79:80/productpage
<!DOCTYPE html>
<html>
  <head>
    <title>Simple Bookstore App</title>
<meta charset="utf-8">
...
```