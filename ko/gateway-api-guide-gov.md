## Container > NHN Kubernetes Service(NKS) > 애플리케이션 가이드 > Gateway API

### 개요

Gateway API는 Kubernetes에서 트래픽 라우팅을 관리하기 위한 차세대 표준 API입니다. 기존 Ingress API의 한계를 극복하고 보다 표현력 있고 확장 가능한 방식으로 클러스터 외부에서 내부 서비스로의 HTTP, HTTPS, TCP 등 다양한 트래픽을 라우팅할 수 있습니다. Gateway API와 관련 리소스에 대한 자세한 내용은 [Gateway API](https://gateway-api.sigs.k8s.io/) 문서를 참고하세요.

Gateway API는 다양한 구현체를 지원하며, 사용 환경에 따라 적합한 구현체를 선택할 수 있습니다. 이 문서에서는 NGINX Gateway Fabric를 예제로 설명합니다. 다른 구현체에 대한 정보는 [Gateway API 구현체 목록](https://gateway-api.sigs.k8s.io/implementations/)을 참고하세요.

**NGINX Gateway Fabric(NGF)** 은 NGINX를 data plane으로 사용하는 Gateway API 구현체입니다. NGF의 control plane은 Gateway API 및 관련 Kubernetes 리소스를 감시하며, 각 Gateway에 대해 NGINX data plane Deployment와 Service를 생성하고 관리합니다. 자세한 내용은 [NGINX Gateway Fabric](https://docs.nginx.com/nginx-gateway-fabric/) 문서를 참고하세요.

> [참고] Gateway API는 Kubernetes SIG-Network에서 관리하는 API로, Ingress의 한계를 보완하기 위해 설계된 확장 가능한 트래픽 관리 모델입니다.

> [참고] NKS에서는 Gateway API 구현체의 직접적인 설치를 지원하지 않으며, 사용자가 직접 구현체를 설치하고 운영해야 합니다.

> [참고] 구현체별로 지원하는 Gateway API 버전이 다를 수 있으므로, 자세한 내용은 각 구현체의 공식 문서를 참고하세요.

#### Gateway API 주요 리소스

Gateway API는 역할에 따라 리소스를 분리하여 운영자와 애플리케이션 개발자가 각자의 권한 범위 내에서 설정할 수 있도록 설계되어 있습니다.

| 리소스 | 담당 | 설명 |
|---|---|---|
| **GatewayClass** | 플랫폼/인프라 운영자 | Gateway를 관리할 구현체를 지정하고 공통 설정(parametersRef)을 정의하는 클러스터 수준 리소스 |
| **Gateway** | 클러스터 운영자 | 외부 트래픽 진입점(Listener)을 정의하며, Listener에 프로토콜/포트 및 TLS(mode, certificate 등) 설정을 포함 |
| **HTTPRoute** | 애플리케이션 개발자 | HTTP 트래픽 라우팅 규칙 정의 (경로, 헤더, 메서드 기반) |
| **GRPCRoute** | 애플리케이션 개발자 | gRPC 트래픽 라우팅 규칙 정의 (서비스/메서드 기반) |
| **TLSRoute** | 애플리케이션 개발자 | TLS 트래픽 라우팅 규칙 정의 (SNI 기반, TLS 처리 방식은 Gateway Listener 설정에 따름) |
| **TCPRoute** | 애플리케이션 개발자 | TCP 트래픽 라우팅 규칙 정의 (L4 passthrough) (Experimental) |
| **UDPRoute** | 애플리케이션 개발자 | UDP 트래픽 라우팅 규칙 정의 (connectionless L4 routing) (Experimental) |
| **ReferenceGrant** | 네임스페이스 소유자 | 다른 네임스페이스 리소스를 참조할 수 있도록 명시적으로 허용 |

> [참고] NGF에서는 NginxProxy 리소스를 통해 NGINX data plane 동작(서비스 설정, 레플리카 수 등)을 추가로 구성할 수 있습니다.

### NGINX Gateway Fabric 설치

NGINX Gateway Fabric 설치에 대한 자세한 내용은 [NGINX Gateway Fabric 설치 가이드](https://docs.nginx.com/nginx-gateway-fabric/install) 문서를 참고하세요.

### HTTP 라우팅 예제

다음은 URI 경로를 기반으로 여러 서비스로 트래픽을 분기하는 예제입니다. 아래 그림은 `/coffee`와 `/tea` 경로에 따라 각각 다른 서비스로 요청을 라우팅하는 구조를 나타냅니다.

```bash
클라이언트 → Gateway (LoadBalancer) → HTTPRoute
                                         ├── /coffee → coffee-svc → coffee 파드
                                         └── /tea    → tea-svc    → tea 파드
```

#### 서비스 및 파드 배포

다음과 같이 서비스와 파드를 생성하기 위한 매니페스트를 작성합니다. `coffee-svc` 서비스에는 `coffee` 파드를, `tea-svc` 서비스에는 `tea` 파드를 연결합니다.

```yaml
# cafe.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: coffee
spec:
  replicas: 2
  selector:
    matchLabels:
      app: coffee
  template:
    metadata:
      labels:
        app: coffee
    spec:
      containers:
      - name: coffee
        image: nginxdemos/nginx-hello:plain-text
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: coffee-svc
spec:
  ports:
  - port: 80
    targetPort: 8080
    protocol: TCP
    name: http
  selector:
    app: coffee
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: tea
spec:
  replicas: 1
  selector:
    matchLabels:
      app: tea
  template:
    metadata:
      labels:
        app: tea
    spec:
      containers:
      - name: tea
        image: nginxdemos/nginx-hello:plain-text
        ports:
        - containerPort: 8080
---
apiVersion: v1
kind: Service
metadata:
  name: tea-svc
spec:
  ports:
  - port: 80
    targetPort: 8080
    protocol: TCP
    name: http
  selector:
    app: tea
```

위 매니페스트를 적용합니다.

```bash
kubectl apply -f cafe.yaml
```

#### GatewayClass 확인

NGINX Gateway Fabric은 Helm 설치 시 `nginx`라는 이름의 GatewayClass가 생성됩니다. 설치 방식에 따라 GatewayClass가 자동 생성되지 않을 수 있으므로, 필요 시 직접 생성해야 합니다.

```bash
kubectl get gatewayclass
```

출력 예시는 다음과 같습니다.

```bash
NAME    CONTROLLER                                    ACCEPTED   AGE
nginx   gateway.nginx.org/nginx-gateway-controller    True       10s
```

`ACCEPTED` 항목이 `True`인 경우 GatewayClass가 정상적으로 생성된 것입니다.

#### Gateway 생성

클러스터 외부에서 트래픽을 수신할 게이트웨이를 생성합니다. 아래 매니페스트는 80번 포트로 HTTP 트래픽을 수신하는 게이트웨이를 정의합니다.

```yaml
# gateway.yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: nginx
spec:
  gatewayClassName: nginx
  listeners:
  - name: http
    protocol: HTTP
    port: 80
```

위 매니페스트를 적용합니다.

```bash
kubectl apply -f gateway.yaml
```

게이트웨이가 생성되면 LoadBalancer 타입 서비스가 생성되며, NHN Cloud 로드 밸런서가 자동으로 프로비저닝됩니다. 아래 명령어로 게이트웨이 상태와 외부 IP를 확인합니다.

```bash
kubectl get gateway nginx
```

출력 예시는 다음과 같습니다.

```bash
NAME    CLASS   ADDRESS          PROGRAMMED   AGE
nginx   nginx   123.123.123.44   True         1m
```

`PROGRAMMED` 항목이 `True`이고 `ADDRESS`에 IP 주소가 할당된 것을 확인합니다.

#### HTTPRoute 생성

경로 기반 라우팅 규칙을 정의하는 HTTPRoute를 생성합니다. 아래 매니페스트를 작성하고 적용합니다.

```yaml
# cafe-route.yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: cafe-route
spec:
  parentRefs:
  - name: nginx
  rules:
  - matches:
    - path:
        type: PathPrefix
        value: /coffee
    backendRefs:
    - name: coffee-svc
      port: 80
  - matches:
    - path:
        type: PathPrefix
        value: /tea
    backendRefs:
    - name: tea-svc
      port: 80
```

위 매니페스트를 적용합니다.

```bash
kubectl apply -f cafe-route.yaml
```

#### 동작 확인

외부 호스트에서 게이트웨이의 IP 주소로 HTTP 요청을 전송하여 라우팅이 올바르게 설정되었는지 확인합니다.

```bash
GATEWAY_IP=$(kubectl get gateway nginx -o jsonpath='{.status.addresses[0].value}')

# /coffee 엔드포인트 확인
curl http://${GATEWAY_IP}/coffee

# /tea 엔드포인트 확인
curl http://${GATEWAY_IP}/tea
```

`/coffee` 경로로 요청 시 `coffee-svc` 서비스에 전달되어 `coffee` 파드가 응답합니다. 응답의 `Server name` 항목을 보면 `coffee` 파드들이 라운드-로빈 방식으로 번갈아 응답하는 것을 확인할 수 있습니다.

### 호스트 기반 라우팅 예제

다음은 요청의 호스트명(Host 헤더)을 기반으로 서로 다른 서비스로 트래픽을 분기하는 예제입니다. 아래 그림은 `coffee.example.com`과 `tea.example.com` 호스트명에 따라 각각 다른 서비스로 요청을 라우팅하는 구조를 나타냅니다.

```bash
클라이언트 → Gateway (LoadBalancer) → HTTPRoute (coffee.example.com) → coffee-svc → coffee 파드
                                  → HTTPRoute (tea.example.com)    → tea-svc    → tea 파드
```

서비스 및 파드는 앞선 예제의 `cafe.yaml`을 그대로 사용합니다.

#### HTTPRoute 생성

호스트명별로 HTTPRoute를 각각 생성합니다. `spec.hostnames` 필드에 라우팅할 호스트명을 지정합니다.

```yaml
# cafe-route-host.yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: coffee-route
spec:
  parentRefs:
  - name: nginx
  hostnames:
  - coffee.example.com
  rules:
  - backendRefs:
    - name: coffee-svc
      port: 80
---
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: tea-route
spec:
  parentRefs:
  - name: nginx
  hostnames:
  - tea.example.com
  rules:
  - backendRefs:
    - name: tea-svc
      port: 80
```

위 매니페스트를 적용합니다.

```bash
kubectl apply -f cafe-route-host.yaml
```

#### 동작 확인

외부 호스트에서 `--resolve` 옵션으로 호스트명을 게이트웨이 IP에 매핑하여 요청을 전송합니다.

```bash
GATEWAY_IP=$(kubectl get gateway nginx -o jsonpath='{.status.addresses[0].value}')

# coffee.example.com 호스트 확인
curl --resolve coffee.example.com:80:${GATEWAY_IP} \
  http://coffee.example.com

# tea.example.com 호스트 확인
curl --resolve tea.example.com:80:${GATEWAY_IP} \
  http://tea.example.com
```

`coffee.example.com` 호스트로 요청 시 `coffee-svc` 서비스에 전달되어 `coffee` 파드가 응답합니다. `tea.example.com` 호스트로 요청 시 `tea-svc` 서비스에 전달되어 `tea` 파드가 응답합니다.

> [참고] 실제 운영 환경에서는 DNS에 각 호스트명이 게이트웨이의 외부 IP로 등록되어 있어야 합니다. 테스트 시에는 위와 같이 `--resolve` 옵션으로 호스트명을 게이트웨이 IP로 직접 매핑하거나, `/etc/hosts` 파일에 레코드를 추가하여 확인할 수 있습니다.

### 네임스페이스 간 라우팅 예제

Gateway API는 여러 네임스페이스에 걸친 트래픽 라우팅을 지원합니다. 이 예제에서는 `nginx-gateway` 네임스페이스에 Gateway를, `default` 네임스페이스에 HTTPRoute를, `app` 네임스페이스에 Service를 배포합니다.

Gateway API에서 네임스페이스 간 참조는 다음 두 가지 설정으로 제어됩니다. 두 설정은 역할이 다르며 목적에 따라 함께 사용해야 합니다.

* **`allowedRoutes.namespaces`**: Gateway와 HTTPRoute의 네임스페이스가 다를 때 필요합니다. HTTPRoute가 다른 네임스페이스의 Gateway를 `parentRef`로 참조할 수 있도록 허용합니다.
* **`ReferenceGrant`**: HTTPRoute와 백엔드 Service의 네임스페이스가 다를 때 필요합니다. HTTPRoute가 다른 네임스페이스의 Service를 `backendRef`로 참조할 수 있도록 허용합니다.

#### 서비스 및 파드 배포

예제에서 사용할 `app` 네임스페이스를 생성합니다.

```bash
kubectl create namespace app
```

앞선 예제의 `cafe.yaml`을 `app` 네임스페이스에 배포합니다.

```bash
kubectl apply -f cafe.yaml -n app
```

#### Gateway 생성

`nginx-gateway` 네임스페이스에 Gateway를 생성합니다. `default` 네임스페이스의 HTTPRoute가 이 Gateway를 참조할 수 있도록 `allowedRoutes.namespaces.from: All`로 설정합니다. 아래 매니페스트를 작성하고 적용합니다.

```yaml
# gateway-cross-ns.yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: nginx-cross-ns
  namespace: nginx-gateway
spec:
  gatewayClassName: nginx
  listeners:
  - name: http
    protocol: HTTP
    port: 80
    allowedRoutes:
      namespaces:
        from: All
```

위 매니페스트를 적용합니다.

```bash
kubectl apply -f gateway-cross-ns.yaml
```

#### ReferenceGrant 생성

`default` 네임스페이스의 HTTPRoute가 `app` 네임스페이스의 Service를 참조할 수 있도록 `ReferenceGrant`를 생성합니다. 아래 매니페스트를 작성하고 적용합니다.

```yaml
# reference-grant.yaml
apiVersion: gateway.networking.k8s.io/v1beta1
kind: ReferenceGrant
metadata:
  name: allow-default-to-app
  namespace: app
spec:
  from:
  - group: gateway.networking.k8s.io
    kind: HTTPRoute
    namespace: default
  to:
  - group: ""
    kind: Service
```

위 매니페스트를 적용합니다.

```bash
kubectl apply -f reference-grant.yaml
```

> [참고] ReferenceGrant 리소스는 Gateway API v1.5부터 v1 API로 제공됩니다. 사용 중인 Gateway API 버전 또는 구현체에 따라 v1beta1 API를 사용할 수 있습니다.

#### HTTPRoute 생성

`default` 네임스페이스에 HTTPRoute를 생성합니다. `parentRef`에 Gateway의 네임스페이스(`nginx-gateway`)를, `backendRef`에 Service의 네임스페이스(`app`)를 명시합니다. 아래 매니페스트를 작성하고 적용합니다.

```yaml
# cafe-route-cross-ns.yaml
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: cafe-route-cross-ns
  namespace: default
spec:
  parentRefs:
  - name: nginx-cross-ns
    namespace: nginx-gateway
  rules:
  - matches:
    - path:
        type: PathPrefix
        value: /coffee
    backendRefs:
    - name: coffee-svc
      namespace: app
      port: 80
  - matches:
    - path:
        type: PathPrefix
        value: /tea
    backendRefs:
    - name: tea-svc
      namespace: app
      port: 80
```

위 매니페스트를 적용합니다.

```bash
kubectl apply -f cafe-route-cross-ns.yaml
```

#### 동작 확인

아래 명령어로 라우팅이 올바르게 설정되었는지 확인합니다.

```bash
GATEWAY_IP=$(kubectl get gateway nginx-cross-ns -n nginx-gateway -o jsonpath='{.status.addresses[0].value}')

# /coffee 엔드포인트 확인
curl http://${GATEWAY_IP}/coffee

# /tea 엔드포인트 확인
curl http://${GATEWAY_IP}/tea
```

### Ingress에서 Gateway API로 마이그레이션

`ingress2gateway`는 Kubernetes SIG-Network에서 관리하는 도구로, 기존 Ingress 리소스를 Gateway API 리소스(Gateway, HTTPRoute 등)로 변환하는 데 사용할 수 있습니다. 다만 모든 Ingress 설정이 완전히 변환되는 것은 아니며, 일부 기능은 수동 보정이 필요할 수 있습니다. 자세한 내용은 [ingress2gateway](https://github.com/kubernetes-sigs/ingress2gateway) 문서를 참고하세요.

> [참고] `ingress2gateway`는 변환 결과를 stdout으로 출력할 뿐 클러스터에 직접 적용하지 않습니다. 변환된 리소스를 검토한 후 직접 적용해야 합니다.

> [참고] 모든 Ingress 어노테이션이 Gateway API로 변환되는 것은 아닙니다. 변환 후 지원되지 않는 어노테이션이 있는 경우 경고 메시지가 출력됩니다. 자세한 내용은 [지원 프로바이더 목록](https://github.com/kubernetes-sigs/ingress2gateway?tab=readme-ov-file#supported-providers)을 참고하세요.

#### ingress2gateway 설치

ingress2gateway 설치에 대한 자세한 내용은 [ingress2gateway 설치 가이드](https://github.com/kubernetes-sigs/ingress2gateway?tab=readme-ov-file#installation) 문서를 참고하세요.

#### Ingress 리소스 변환

현재 클러스터에 배포된 Ingress 리소스를 Gateway API 리소스로 변환합니다. `--providers` 옵션으로 사용 중인 Ingress 컨트롤러를 지정하고, `-A` 옵션으로 모든 네임스페이스의 리소스를 변환합니다. 변환 결과를 파일로 저장하려면 아래 명령어를 사용합니다.

```bash
ingress2gateway print --providers=ingress-nginx -A > gateway-resources.yaml
```

클러스터가 아닌 매니페스트 파일을 직접 변환하려면 `--input-file` 옵션을 사용합니다. 먼저 클러스터의 Ingress 리소스를 파일로 저장합니다.

```bash
kubectl get ingress -A -o yaml > /path/to/ingress.yaml
```

저장한 파일을 입력으로 변환합니다.

```bash
ingress2gateway print --providers=ingress-nginx --input-file /path/to/ingress.yaml > gateway-resources.yaml
```

#### 변환 예시

아래는 변환 전 Ingress 리소스와 변환 후 Gateway API 리소스의 예시입니다.

변환 전 Ingress 리소스는 다음과 같습니다.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: cafe-ingress
spec:
  ingressClassName: nginx
  rules:
  - host: cafe.example.com
    http:
      paths:
      - path: /coffee
        pathType: Prefix
        backend:
          service:
            name: coffee-svc
            port:
              number: 80
      - path: /tea
        pathType: Prefix
        backend:
          service:
            name: tea-svc
            port:
              number: 80
```

변환 후 생성되는 Gateway API 리소스는 다음과 같습니다.

```yaml
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  annotations:
    gateway.networking.k8s.io/generator: ingress2gateway-dev
  name: nginx
spec:
  gatewayClassName: nginx
  listeners:
  - hostname: cafe.example.com
    name: cafe-example-com-http
    port: 80
    protocol: HTTP
status: {}
---
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  annotations:
    gateway.networking.k8s.io/generator: ingress2gateway-dev
  name: cafe-ingress-cafe-example-com
spec:
  hostnames:
  - cafe.example.com
  parentRefs:
  - name: nginx
  rules:
  - backendRefs:
    - name: coffee-svc
      port: 80
    matches:
    - path:
        type: PathPrefix
        value: /coffee
  - backendRefs:
    - name: tea-svc
      port: 80
    matches:
    - path:
        type: PathPrefix
        value: /tea
status:
  parents: []
```

> [참고] 변환된 리소스의 `gatewayClassName`은 기존 Ingress 클래스명(`nginx`)을 그대로 사용합니다. 앞서 설치 시 지정한 GatewayClass 이름과 일치하는지 확인하세요.

#### 변환 결과 적용

변환된 리소스를 검토한 후 클러스터에 적용합니다.

```bash
kubectl apply -f gateway-resources.yaml
```

기존 Ingress 리소스와 Gateway API 리소스가 동시에 동작하는 것을 확인한 후 Ingress 리소스를 삭제합니다.

```bash
kubectl delete ingress cafe-ingress
```