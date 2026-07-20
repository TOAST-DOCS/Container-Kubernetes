<!-- pre-align:aligned sig=091ebaa2b119 -->

<a id="container-nhn-kubernetes-service-nks-application-guide-gateway-api"></a>
## Container > NHN Kubernetes Service (NKS) > Application Guide > Gateway API { #container-nhn-kubernetes-service-nks-application-guide-gateway-api }

<a id="overview"></a>
### Overview { #overview }

Gateway API is a next-generation standard API for managing traffic routing in Kubernetes. It overcomes the limitations of the existing Ingress API and enables routing of various traffic types — such as HTTP, HTTPS, and TCP — from outside the cluster to internal services in a more expressive and extensible way. For more information on Gateway API and related resources, see the [Gateway API](https://gateway-api.sigs.k8s.io/) documentation.

Gateway API supports various implementations, and you can choose the one that best fits your environment. This document uses NGINX Gateway Fabric as an example. For information on other implementations, see the [Gateway API implementations list](https://gateway-api.sigs.k8s.io/implementations/).

**NGINX Gateway Fabric (NGF)** is a Gateway API implementation that uses NGINX as the data plane. The NGF control plane monitors Gateway API and related Kubernetes resources, and creates and manages NGINX data plane Deployments and Services for each Gateway. For more information, see the [NGINX Gateway Fabric](https://docs.nginx.com/nginx-gateway-fabric/) documentation.

> [Note] Gateway API is an API managed by Kubernetes SIG-Network, designed as an extensible traffic management model to address the limitations of Ingress.

> [Note] NKS does not support direct installation of Gateway API implementations. Users must install and operate implementations themselves.

> [Note] The supported Gateway API version may vary by implementation. For more information, refer to the official documentation of each implementation.

<a id="overview-key-gateway-api-resources"></a>
#### Key Gateway API Resources

Gateway API is designed to separate resources by role, allowing platform operators and application developers to configure settings within their respective scopes of authority.

| Resource | Owner | Description |
|---|---|---|
| **GatewayClass** | Platform/infrastructure operator | A cluster-level resource that specifies the implementation to manage the Gateway and defines common settings (parametersRef) |
| **Gateway** | Cluster operator | Defines external traffic entry points (Listeners) and includes protocol/port and TLS (mode, certificate, etc.) settings for each Listener |
| **HTTPRoute** | Application developer | Defines HTTP traffic routing rules (path-, header-, and method-based) |
| **GRPCRoute** | Application developer | Defines gRPC traffic routing rules (service/method-based) |
| **TLSRoute** | Application developer | Defines TLS traffic routing rules (SNI-based; TLS handling method depends on Gateway Listener settings) |
| **TCPRoute** | Application developer | Defines TCP traffic routing rules (L4 passthrough) (Experimental) |
| **UDPRoute** | Application developer | Defines UDP traffic routing rules (connectionless L4 routing) (Experimental) |
| **ReferenceGrant** | Namespace owner | Explicitly allows referencing resources in other namespaces |

> [Note] In NGF, the NginxProxy resource can be used to additionally configure NGINX data plane behavior (service settings, replica count, etc.).

<a id="install-nginx-gateway-fabric"></a>
### Install NGINX Gateway Fabric { #install-nginx-gateway-fabric }

For more information on installing NGINX Gateway Fabric, see the [NGINX Gateway Fabric installation guide](https://docs.nginx.com/nginx-gateway-fabric/install) documentation.

<a id="http-routing-example"></a>
### HTTP Routing Example { #http-routing-example }

The following is an example of routing traffic to multiple services based on the URI path. The diagram below shows the structure for routing requests to different services based on the `/coffee` and `/tea` paths.

```bash
Client → Gateway (LoadBalancer) → HTTPRoute
                                    ├── /coffee → coffee-svc → coffee Pod
                                    └── /tea    → tea-svc    → tea Pod
```

<a id="http-routing-example-deploy-services-and-pods"></a>
#### Deploy Services and Pods

Write a manifest to create services and Pods as shown below. Connect the `coffee` Pod to the `coffee-svc` service and the `tea` Pod to the `tea-svc` service.

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

Apply the above manifest.

```bash
kubectl apply -f cafe.yaml
```

<a id="http-routing-example-verify-gatewayclass"></a>
#### Verify GatewayClass

When NGINX Gateway Fabric is installed via Helm, a GatewayClass named `nginx` is created. Depending on the installation method, the GatewayClass may not be created automatically, in which case you must create it manually.

```bash
kubectl get gatewayclass
```

The output example is as follows.

```bash
NAME    CONTROLLER                                    ACCEPTED   AGE
nginx   gateway.nginx.org/nginx-gateway-controller    True       10s
```

If the `ACCEPTED` field is `True`, the GatewayClass has been created successfully.

<a id="http-routing-example-create-gateway"></a>
#### Create Gateway

Create a gateway to receive traffic from outside the cluster. The following manifest defines a gateway that receives HTTP traffic on port 80.

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

Apply the above manifest.

```bash
kubectl apply -f gateway.yaml
```

When the gateway is created, a LoadBalancer type service is created and an NHN Cloud load balancer is automatically provisioned. Use the following command to check the gateway status and external IP.

```bash
kubectl get gateway nginx
```

The output example is as follows.

```bash
NAME    CLASS   ADDRESS          PROGRAMMED   AGE
nginx   nginx   123.123.123.44   True         1m
```

Verify that the `PROGRAMMED` field is `True` and that an IP address has been assigned to `ADDRESS`.

<a id="http-routing-example-create-httproute"></a>
#### Create HTTPRoute

Create an HTTPRoute that defines path-based routing rules. Write and apply the following manifest.

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

Apply the above manifest.

```bash
kubectl apply -f cafe-route.yaml
```

<a id="http-routing-example-verify-operation"></a>
#### Verify Operation

Send an HTTP request from an external host to the gateway IP address to verify that routing has been configured correctly.

```bash
GATEWAY_IP=$(kubectl get gateway nginx -o jsonpath='{.status.addresses[0].value}')

# Verify /coffee endpoint
curl http://${GATEWAY_IP}/coffee

# Verify /tea endpoint
curl http://${GATEWAY_IP}/tea
```

When a request is sent to the `/coffee` path, it is forwarded to the `coffee-svc` service and the `coffee` Pod responds. The `Server name` field in the response shows that the `coffee` Pods respond alternately in a round-robin manner.

<a id="host-based-routing-example"></a>
### Host-Based Routing Example { #host-based-routing-example }

The following is an example of routing traffic to different services based on the hostname (Host header) of the request. The diagram below shows the structure for routing requests to different services based on the `coffee.example.com` and `tea.example.com` hostnames.

```bash
Client → Gateway (LoadBalancer) → HTTPRoute (coffee.example.com) → coffee-svc → coffee Pod
                               → HTTPRoute (tea.example.com)    → tea-svc    → tea Pod
```

The services and Pods use the `cafe.yaml` from the previous example as-is.

<a id="host-based-routing-example-create-httproute"></a>
#### Create HTTPRoute

Create an HTTPRoute for each hostname. Specify the hostname to route to in the `spec.hostnames` field.

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

Apply the above manifest.

```bash
kubectl apply -f cafe-route-host.yaml
```

<a id="host-based-routing-example-verify-operation"></a>
#### Verify Operation

Send a request from an external host by mapping the hostname to the gateway IP using the `--resolve` option.

```bash
GATEWAY_IP=$(kubectl get gateway nginx -o jsonpath='{.status.addresses[0].value}')

# Verify coffee.example.com host
curl --resolve coffee.example.com:80:${GATEWAY_IP} \
  http://coffee.example.com

# Verify tea.example.com host
curl --resolve tea.example.com:80:${GATEWAY_IP} \
  http://tea.example.com
```

When a request is sent to the `coffee.example.com` host, it is forwarded to the `coffee-svc` service and the `coffee` Pod responds. When a request is sent to the `tea.example.com` host, it is forwarded to the `tea-svc` service and the `tea` Pod responds.

> [Note] In a production environment, each hostname must be registered in DNS to point to the gateway's external IP. For testing, you can map the hostname directly to the gateway IP using the `--resolve` option as shown above, or by adding a record to the `/etc/hosts` file.

<a id="cross-namespace-routing-example"></a>
### Cross-Namespace Routing Example { #cross-namespace-routing-example }

Gateway API supports traffic routing across multiple namespaces. In this example, a Gateway is deployed in the `nginx-gateway` namespace, an HTTPRoute in the `default` namespace, and a Service in the `app` namespace.

Cross-namespace references in Gateway API are controlled by the following two settings. The two settings serve different roles and must be used together depending on the purpose.

* **`allowedRoutes.namespaces`**: Required when the Gateway and HTTPRoute are in different namespaces. Allows the HTTPRoute to reference a Gateway in another namespace as a `parentRef`.
* **`ReferenceGrant`**: Required when the HTTPRoute and backend Service are in different namespaces. Allows the HTTPRoute to reference a Service in another namespace as a `backendRef`.

<a id="cross-namespace-routing-example-deploy-services-and-pods"></a>
#### Deploy Services and Pods

Create the `app` namespace to be used in the example.

```bash
kubectl create namespace app
```

Deploy the `cafe.yaml` from the previous example to the `app` namespace.

```bash
kubectl apply -f cafe.yaml -n app
```

<a id="cross-namespace-routing-example-create-gateway"></a>
#### Create Gateway

Create a Gateway in the `nginx-gateway` namespace. Set `allowedRoutes.namespaces.from: All` to allow the HTTPRoute in the `default` namespace to reference this Gateway. Write and apply the following manifest.

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

Apply the above manifest.

```bash
kubectl apply -f gateway-cross-ns.yaml
```

<a id="cross-namespace-routing-example-create-referencegrant"></a>
#### Create ReferenceGrant

Create a `ReferenceGrant` to allow the HTTPRoute in the `default` namespace to reference the Service in the `app` namespace. Write and apply the following manifest.

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

Apply the above manifest.

```bash
kubectl apply -f reference-grant.yaml
```

> [Note] The ReferenceGrant resource is available as a v1 API starting from Gateway API v1.5. Depending on the Gateway API version or implementation in use, the v1beta1 API may be used.

<a id="cross-namespace-routing-example-create-httproute"></a>
#### Create HTTPRoute

Create an HTTPRoute in the `default` namespace. Specify the Gateway's namespace (`nginx-gateway`) in `parentRef` and the Service's namespace (`app`) in `backendRef`. Write and apply the following manifest.

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

Apply the above manifest.

```bash
kubectl apply -f cafe-route-cross-ns.yaml
```

<a id="cross-namespace-routing-example-verify-operation"></a>
#### Verify Operation

Use the following command to verify that routing has been configured correctly.

```bash
GATEWAY_IP=$(kubectl get gateway nginx-cross-ns -n nginx-gateway -o jsonpath='{.status.addresses[0].value}')

# Verify /coffee endpoint
curl http://${GATEWAY_IP}/coffee

# Verify /tea endpoint
curl http://${GATEWAY_IP}/tea
```

<a id="migrate-from-ingress-to-gateway-api"></a>
### Migrate from Ingress to Gateway API { #migrate-from-ingress-to-gateway-api }

`ingress2gateway` is a tool managed by Kubernetes SIG-Network that can be used to convert existing Ingress resources to Gateway API resources (Gateway, HTTPRoute, etc.). However, not all Ingress settings are fully converted, and some features may require manual adjustments. For more information, see the [ingress2gateway](https://github.com/kubernetes-sigs/ingress2gateway) documentation.

> [Note] `ingress2gateway` only outputs the conversion results to stdout and does not apply them directly to the cluster. You must review the converted resources and apply them manually.

> [Note] Not all Ingress annotations are converted to Gateway API. If there are unsupported annotations after conversion, a warning message is displayed. For more information, see the [supported providers list](https://github.com/kubernetes-sigs/ingress2gateway?tab=readme-ov-file#supported-providers).

<a id="migrate-from-ingress-to-gateway-api-install-ingress2gateway"></a>
#### Install ingress2gateway

For more information on installing ingress2gateway, see the [ingress2gateway installation guide](https://github.com/kubernetes-sigs/ingress2gateway?tab=readme-ov-file#installation) documentation.

<a id="migrate-from-ingress-to-gateway-api-convert-ingress-resources"></a>
#### Convert Ingress Resources

Convert the Ingress resources deployed in the current cluster to Gateway API resources. Specify the Ingress controller in use with the `--providers` option and convert resources from all namespaces with the `-A` option. Use the following command to save the conversion results to a file.

```bash
ingress2gateway print --providers=ingress-nginx -A > gateway-resources.yaml
```

To convert a manifest file directly instead of from the cluster, use the `--input-file` option. First, save the cluster's Ingress resources to a file.

```bash
kubectl get ingress -A -o yaml > /path/to/ingress.yaml
```

Convert the saved file as input.

```bash
ingress2gateway print --providers=ingress-nginx --input-file /path/to/ingress.yaml > gateway-resources.yaml
```

<a id="migrate-from-ingress-to-gateway-api-conversion-example"></a>
#### Conversion Example

The following is an example of an Ingress resource before conversion and a Gateway API resource after conversion.

The Ingress resource before conversion is as follows.

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

The Gateway API resource generated after conversion is as follows.

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

> [Note] The `gatewayClassName` of the converted resource uses the existing Ingress class name (`nginx`) as-is. Verify that it matches the GatewayClass name specified during installation.

<a id="migrate-from-ingress-to-gateway-api-apply-conversion-results"></a>
#### Apply Conversion Results

Review the converted resources and apply them to the cluster.

```bash
kubectl apply -f gateway-resources.yaml
```

After verifying that the existing Ingress resources and Gateway API resources are operating simultaneously, delete the Ingress resources.

```bash
kubectl delete ingress cafe-ingress
```