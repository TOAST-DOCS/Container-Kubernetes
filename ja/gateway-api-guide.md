## Container > NHN Kubernetes Service(NKS) > アプリケーションガイド > Gateway API

### 概要

Gateway APIは、Kubernetesでトラフィックルーティングを管理するための次世代標準APIです。従来のIngress APIの限界を克服し、より表現力豊かで拡張可能な方法で、クラスター外部から内部サービスへのHTTP、HTTPS、TCPなど多様なトラフィックをルーティングできます。Gateway APIと関連リソースの詳細は、[Gateway API](https://gateway-api.sigs.k8s.io/)ドキュメントを参照してください。

Gateway APIは多様な実装体をサポートしており、使用環境に応じて適切なものを選択できます。このドキュメントではNGINX Gateway Fabricを例として説明します。他の実装体に関する情報は、[Gateway API実装体一覧](https://gateway-api.sigs.k8s.io/implementations/)を参照してください。

**NGINX Gateway Fabric(NGF)**は、NGINXをdata planeとして使用するGateway API実装体です。NGFのcontrol planeはGateway API及び関連するKubernetesリソースを監視し、各Gatewayに対してNGINX data plane DeploymentとServiceを作成し管理します。詳細は、[NGINX Gateway Fabric](https://docs.nginx.com/nginx-gateway-fabric/)ドキュメントを参照してください。

> [参考] Gateway APIはKubernetes SIG-Networkが管理するAPIで、Ingressの限界を補完するために設計された拡張可能なトラフィック管理モデルです。

> [参考] NKSではGateway API実装体の直接インストールをサポートしておらず、ユーザーが独自に実装体をインストールして運用する必要があります。

> [参考] 実装体ごとにサポートするGateway APIのバージョンが異なる場合があるため、詳細は各実装体の公式ドキュメントを参照してください。

#### Gateway APIの主要リソース

Gateway APIは役割に応じてリソースを分離し、運営者とアプリケーション開発者が各自の権限範囲内で設定できるように設計されています。

| リソース | 担当 | 説明 |
|---|---|---|
| **GatewayClass** | プラットフォーム/インフラ運営者 | Gatewayを管理する実装体を指定し、共通設定(parametersRef)を定義するクラスターレベルのリソース |
| **Gateway** | クラスター運営者 | 外部トラフィックの進入点(Listener)を定義し、Listenerにプロトコル/ポート及びTLS(mode、certificateなど)設定を含む |
| **HTTPRoute** | アプリケーション開発者 | HTTPトラフィックルーティングルールの定義(パス、ヘッダ、メソッドベース) |
| **GRPCRoute** | アプリケーション開発者 | gRPCトラフィックルーティングルールの定義(サービス/メソッドベース) |
| **TLSRoute** | アプリケーション開発者 | TLSトラフィックルーティングルールの定義(SNIベース、TLS処理方式はGateway Listenerの設定に従う) |
| **TCPRoute** | アプリケーション開発者 | TCPトラフィックルーティングルールの定義(L4 passthrough) (Experimental) |
| **UDPRoute** | アプリケーション開発者 | UDPトラフィックルーティングルールの定義(connectionless L4 routing) (Experimental) |
| **ReferenceGrant** | ネームスペースの所有者 | 他のネームスペースのリソースを参照できるように明示的に許可 |

> [参考] NGFではNginxProxyリソースを通じてNGINX data planeの動作(サービス設定、レプリカ数など)を追加で構成できます。

### NGINX Gateway Fabricのインストール

NGINX Gateway Fabricのインストールに関する詳細は、[NGINX Gateway Fabricインストールガイド](https://docs.nginx.com/nginx-gateway-fabric/install)ドキュメントを参照してください。

### HTTPルーティングの例

以下は、URIパスに基づいて複数のサービスへトラフィックを分岐する例です。下の図は、`/coffee`と`/tea`パスに応じてそれぞれ異なるサービスへリクエストをルーティングする構造を示しています。

```bash
クライアント → Gateway (LoadBalancer) → HTTPRoute
                                         ├── /coffee → coffee-svc → coffee Pod
                                         └── /tea    → tea-svc    → tea Pod
```

#### サービスおよびPodのデプロイ

次のようにサービスとPodを作成するためのマニフェストを作成します。`coffee-svc`サービスには`coffee` Podを、`tea-svc`サービスには`tea` Podを接続します。

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

上記のマニフェストを適用します。

```bash
kubectl apply -f cafe.yaml
```

#### GatewayClassの確認

NGINX Gateway Fabricは、Helmでのインストール時に`nginx`という名前のGatewayClassが作成されます。インストール方式によってはGatewayClassが自動作成されない場合があるため、必要に応じて独自に作成する必要があります。

```bash
kubectl get gatewayclass
```

出力例は次のとおりです。

```bash
NAME    CONTROLLER                                    ACCEPTED   AGE
nginx   gateway.nginx.org/nginx-gateway-controller    True       10s
```

`ACCEPTED`項目が`True`の場合、GatewayClassが正常に作成されています。

#### Gatewayの作成

クラスター外部からトラフィックを受信するゲートウェイを作成します。以下のマニフェストは、80番ポートでHTTPトラフィックを受信するゲートウェイを定義します。

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

上記のマニフェストを適用します。

```bash
kubectl apply -f gateway.yaml
```

ゲートウェイが作成されるとLoadBalancerタイプのサービスが作成され、NHN Cloudロードバランサーが自動的にプロビジョニングされます。以下のコマンドでゲートウェイの状態と外部IPを確認します。

```bash
kubectl get gateway nginx
```

出力例は次のとおりです。

```bash
NAME    CLASS   ADDRESS          PROGRAMMED   AGE
nginx   nginx   123.123.123.44   True         1m
```

`PROGRAMMED`項目が`True`であり、`ADDRESS`にIPアドレスが割り当てられていることを確認します。

#### HTTPRouteの作成

パスベースのルーティングルールを定義するHTTPRouteを作成します。以下のマニフェストを作成して適用します。

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

上記のマニフェストを適用します。

```bash
kubectl apply -f cafe-route.yaml
```

#### 動作確認

外部ホストからゲートウェイのIPアドレスへHTTPリクエストを送信し、ルーティングが正しく設定されているか確認します。

```bash
GATEWAY_IP=$(kubectl get gateway nginx -o jsonpath='{.status.addresses[0].value}')

# /coffeeエンドポイントの確認
curl http://${GATEWAY_IP}/coffee

# /teaエンドポイントの確認
curl http://${GATEWAY_IP}/tea
```

`/coffee`パスへのリクエスト時は`coffee-svc`サービスに転送され、`coffee` Podがレスポンスを返します。レスポンスの`Server name`項目を見ると、`coffee` Podがラウンドロビン方式で交互にレスポンスを返していることが確認できます。

### ホストベースのルーティングの例

以下は、リクエストのホスト名(Hostヘッダ)に基づいて異なるサービスへトラフィックを分岐する例です。下の図は、`coffee.example.com`と`tea.example.com`のホスト名に応じてそれぞれ異なるサービスへリクエストをルーティングする構造を示しています。

```bash
クライアント → Gateway (LoadBalancer) → HTTPRoute (coffee.example.com) → coffee-svc → coffee Pod
                                  → HTTPRoute (tea.example.com)    → tea-svc    → tea Pod
```

サービスおよびPodは、前の例の`cafe.yaml`をそのまま使用します。

#### HTTPRouteの作成

ホスト名ごとにHTTPRouteをそれぞれ作成します。`spec.hostnames`フィールドにルーティングするホスト名を指定します。

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

上記のマニフェストを適用します。

```bash
kubectl apply -f cafe-route-host.yaml
```

#### 動作確認

外部ホストから`--resolve`オプションを使用してホスト名をゲートウェイIPにマッピングし、リクエストを送信します。

```bash
GATEWAY_IP=$(kubectl get gateway nginx -o jsonpath='{.status.addresses[0].value}')

# coffee.example.comホストの確認
curl --resolve coffee.example.com:80:${GATEWAY_IP} \
  http://coffee.example.com

# tea.example.comホストの確認
curl --resolve tea.example.com:80:${GATEWAY_IP} \
  http://tea.example.com
```

`coffee.example.com`ホストへのリクエスト時は`coffee-svc`サービスに転送され、`coffee` Podがレスポンスを返します。`tea.example.com`ホストへのリクエスト時は`tea-svc`サービスに転送され、`tea` Podがレスポンスを返します。

> [参考] 実際の運用環境では、DNSに各ホスト名がゲートウェイの外部IPとして登録されている必要があります。テスト時は上記のように`--resolve`オプションを使用してホスト名をゲートウェイIPに直接マッピングするか、`/etc/hosts`ファイルにレコードを追加して確認できます。

### ネームスペース間のルーティングの例

Gateway APIは、複数のネームスペースにまたがるトラフィックルーティングをサポートします。この例では、`nginx-gateway`ネームスペースにGatewayを、`default`ネームスペースにHTTPRouteを、`app`ネームスペースにServiceをデプロイします。

Gateway APIにおいて、ネームスペース間の参照は次の2つの設定で制御されます。2つの設定は役割が異なり、目的に応じて一緒に使用する必要があります。

* **`allowedRoutes.namespaces`**: GatewayとHTTPRouteのネームスペースが異なる場合に必要です。HTTPRouteが他のネームスペースのGatewayを`parentRef`として参照できるように許可します。
* **`ReferenceGrant`**: HTTPRouteとバックエンドServiceのネームスペースが異なる場合に必要です。HTTPRouteが他のネームスペースのServiceを`backendRef`として参照できるように許可します。

#### サービスおよびPodのデプロイ

例で使用する`app`ネームスペースを作成します。

```bash
kubectl create namespace app
```

前の例の`cafe.yaml`を`app`ネームスペースにデプロイします。

```bash
kubectl apply -f cafe.yaml -n app
```

#### Gatewayの作成

`nginx-gateway`ネームスペースにGatewayを作成します。`default`ネームスペースのHTTPRouteがこのGatewayを参照できるように、`allowedRoutes.namespaces.from: All`と設定します。以下のマニフェストを作成して適用します。

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

上記のマニフェストを適用します。

```bash
kubectl apply -f gateway-cross-ns.yaml
```

#### ReferenceGrantの作成

`default`ネームスペースのHTTPRouteが`app`ネームスペースのServiceを参照できるように、`ReferenceGrant`を作成します。以下のマニフェストを作成して適用します。

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

上記のマニフェストを適用します。

```bash
kubectl apply -f reference-grant.yaml
```

> [参考] ReferenceGrantリソースは、Gateway API v1.5からv1 APIとして提供されています。使用中のGateway APIバージョンまたは実装体によっては、v1beta1 APIを使用できる場合があります。

#### HTTPRouteの作成

`default`ネームスペースにHTTPRouteを作成します。`parentRef`にGatewayのネームスペース(`nginx-gateway`)を、`backendRef`にServiceのネームスペース(`app`)を明記します。以下のマニフェストを作成して適用します。

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

上記のマニフェストを適用します。

```bash
kubectl apply -f cafe-route-cross-ns.yaml
```

#### 動作確認

以下のコマンドで、ルーティングが正しく設定されているか確認します。

```bash
GATEWAY_IP=$(kubectl get gateway nginx-cross-ns -n nginx-gateway -o jsonpath='{.status.addresses[0].value}')

# /coffeeエンドポイントの確認
curl http://${GATEWAY_IP}/coffee

# /teaエンドポイントの確認
curl http://${GATEWAY_IP}/tea
```

### IngressからGateway APIへのマイグレーション

`ingress2gateway`はKubernetes SIG-Networkが管理するツールで、既存のIngressリソースをGateway APIリソース(Gateway、HTTPRouteなど)に変換するために使用できます。ただし、全てのIngress設定が完全に変換されるわけではなく、一部の機能は手動での補正が必要になる場合があります。詳細は、[ingress2gateway](https://github.com/kubernetes-sigs/ingress2gateway)ドキュメントを参照してください。

> [参考] `ingress2gateway`は変換結果をstdoutに出力するだけで、クラスターに直接適用しません。変換されたリソースを検討した後、独自に適用する必要があります。

> [参考] 全てのIngressアノテーションがGateway APIに変換されるわけではありません。変換後にサポートされないアノテーションがある場合は警告メッセージが出力されます。詳細は、[サポートプロバイダー一覧](https://github.com/kubernetes-sigs/ingress2gateway?tab=readme-ov-file#supported-providers)を参照してください。

#### ingress2gatewayのインストール

ingress2gatewayのインストールに関する詳細は、[ingress2gatewayインストールガイド](https://github.com/kubernetes-sigs/ingress2gateway?tab=readme-ov-file#installation)ドキュメントを参照してください。

#### Ingressリソースの変換

現在クラスターにデプロイされているIngressリソースをGateway APIリソースに変換します。`--providers`オプションで使用中のIngressコントローラーを指定し、`-A`オプションで全てのネームスペースのリソースを変換します。変換結果をファイルとして保存するには、以下のコマンドを使用します。

```bash
ingress2gateway print --providers=ingress-nginx -A > gateway-resources.yaml
```

クラスターではなくマニフェストファイルを直接変換するには、`--input-file`オプションを使用します。まず、クラスターのIngressリソースをファイルとして保存します。

```bash
kubectl get ingress -A -o yaml > /path/to/ingress.yaml
```

保存したファイルを入力として変換します。

```bash
ingress2gateway print --providers=ingress-nginx --input-file /path/to/ingress.yaml > gateway-resources.yaml
```

#### 変換例

以下は変換前のIngressリソースと変換後のGateway APIリソースの例です。

変換前のIngressリソースは次のとおりです。

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

変換後に作成されるGateway APIリソースは次のとおりです。

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

> [参考] 変換されたリソースの`gatewayClassName`は既存のIngressクラス名(`nginx`)をそのまま使用します。先ほどインストール時に指定したGatewayClass名と一致するか確認してください。

#### 変換結果の適用

変換されたリソースを検討した後、クラスターに適用します。

```bash
kubectl apply -f gateway-resources.yaml
```

既存のIngressリソースとGateway APIリソースが同時に動作していることを確認した後、Ingressリソースを削除します。

```bash
kubectl delete ingress cafe-ingress
```
