## Container > NHN Kubernetes Service(NKS) > 外部サービス連動ガイド > Istio

### 概要
この文書はユーザーがNKS(NHN Kubernetes Service(NKS)クラスタにIstioを構成してテストできる例について説明したガイド文書です。NKSでは、インターネットに接続されていないクラスタでもIstioを構成できるように、ガイドとNCRレジストリを通じたIstioイメージを提供しています。ユーザーはこのガイド文書を通じてNKSが提供するイメージを使用してIstioを構成したり、Istio公式ガイド文書を参考にして直接Istioを構成できます。

### Istio
サービスメッシュ(Service Mesh)は、分散システムでサービス間の通信を制御し、効率的に管理できるようにすることに特化したマイクロサービスのためのインフラ層です。
Istioはよく使われるオープンソースのサービスメッシュの一つです。 この例では、クラスタにdemoプロファイルのIstioサービスを配布した後、動作することを示します。Istioの詳細は[Istio](https://istio.io/latest/about/service-mesh/)を参照してください。


### Istio 配布のための事前準備
配布するIstioのバージョンとクラスタに合う正しいレジストリ情報を確認した後、変数に指定します。設定した変数は以降の配布過程で使用されます。

```
$ VERSION="{ダウンロードするistioのバージョン}"
$ REGISTRY="{リージョン別レジストリ}"
```

#### IstioのバージョンごとにサポートされるKubernetesのバージョン

| バージョン | サポートされるKubernetesバージョン |
| --- | --- |
| 1.20.0 | 1.25, 1.26, 1.27, 1.28 |
| 1.19.0 | 1.25, 1.26, 1.27, 1.28 |

> [参考]
> NKSは上記の表に記載されたIstioのバージョンに対してのみインストールを提供します。NKSが提供しないバージョンを使用するには、ユーザーが直接Istioを構成する必要があります。 サポートされるIstioバージョンに関する情報は[istioサポートポリシー](https://istio.io/latest/docs/releases/supported-releases/)を参考してください。


#### リージョン及びインターネット環境別レジストリ情報
| リージョン | インターネット接続 | レジストリ |
| --- | --- | --- |
| 韓国(パンギョ)リージョン | O | dfe965c3-kr1-registry.container.nhncloud.com/container_service/istio |
| | X | private-dfe965c3-kr1-registry.container.nhncloud.com/container_service/istio |
| 韓国(ピョンチョン)リージョン | O | 6e7f43c6-kr2-registry.container.cloud.toast.com/container_service/istio |
| | X | private-6e7f43c6-kr2-registry.container.cloud.toast.com/container_service/istio |

> [参考]
> インターネットに接続されていないクラスタがNCRレジストリを使用するためには、Private URIを使用するための環境設定が必要です。Private URIの使用方法の詳細は[NHN Cloud Container Registry(NCR)ユーザーガイド](/Container/NCR/ja/user-guide/#private-uri)を参照してください。


#### ORASコマンドラインツールのインストール
アーティファクトをpullするためにORAS(OCI Registry As Storage)コマンドラインツールをダウンロードします。ORASはOCIレジストリでOCIアーティファクトをpushおよびpullする方法を提供するツールです。
下記のコマンドを実行してORASコマンドラインツールをインストールします。ORASコマンドラインツールの詳しい使い方は[ORAS docs](https://oras.land/docs/)を参照してください。

```
$ ORAS_VERSION="1.1.0"
$ curl -LO "https://github.com/oras-project/oras/releases/download/v${ORAS_VERSION}/oras_${ORAS_VERSION}_linux_amd64.tar.gz"
$ mkdir -p oras-install/
$ tar -zxf oras_${ORAS_VERSION}_*.tar.gz -C oras-install/
$ sudo mv oras-install/oras /usr/local/bin/
$ rm -rf oras_${ORAS_VERSION}_*.tar.gz oras-install/
$ export PATH="/usr/local/bin:$PATH"
```


### Istioのインストール

#### 1. ORASコマンドラインツールを使ってistioctlをダウンロードしてインストールします。
istioctlはIstioが提供するサービスメッシュディストリビューションをデバッグして診断することができる構成コマンドラインユーティリティで、Istioディストリビューション構成オプションの設定など多様な機能を提供します。istioctlの詳しい説明は[istioctl](https://istio.io/latest/docs/reference/commands/istioctl/)を参照してください。

配布するIstioのバージョンに合うistioctlツールをダウンロードする必要があります。下記のコマンドを実行してistioctlをダウンロードします。
```
$ oras pull ${REGISTRY}/istioctl-tool:${VERSION}
$ tar -zxvf istio-${VERSION}-linux-amd64.tar.gz
$ cd istio-${VERSION}
$ export PATH=$PWD/bin:$PATH
```

#### 2. istioctlを使用してIstioコンポーネントを配布します。
プロファイルは、Istioコントロールプレーンとデータプレーンのサイドカーのユーザー定義配布機能を提供します。設定されたプロファイルによって、配布時に有効化されるコンポーネントが異なります。ユーザーはクラスタの環境構成に応じて基本提供されるプロファイルを使用するか、特定の要求に合わせて構成をユーザー定義できます。 Istioプロファイルの詳しい説明は[Istioインストール構成プロファイル](https://istio.io/latest/docs/setup/additional-setup/config-profiles/)を参照してください。この例では、基本的に提供されるdemoプロファイルを使用して配布します。

`istioctl install`コマンドでIstioコンポーネントを配布します。プロファイルを省略した場合、defaultプロファイルが適用されます。以下は`istioctl install`コマンドを使用してistioコンポーネントを配布する例です。
```
$ istioctl install --set profile=demo --set hub=${REGISTRY} -y
✔ Istio core installed
✔ Istiod installed
✔ Egress gateways installed
✔ Ingress gateways installed
✔ Installation complete
```

マニフェストを作成して配布するには`istioctl manifest generate`コマンドを使用します。以下は`istioctl manifest generate`コマンドを使用してistioコンポーネントを配布する例です。
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

コンポーネントが正常に配布されたか確認します。
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

### アプリケーション配布
Istioが正常にインストールされ、動作することを確認するためアプリケーションを配布します。この例では、Istioが基本的に提供するBookinfoサンプルアプリケーションを使います。サンプルアプリケーションはproductpage, details, reviews, ratingsの4つのマイクロサービスで構成されています。Bookinfoサンプルアプリケーションの詳細は[Istio Bookinfo Application](https://istio.io/latest/docs/examples/bookinfo/)を参照してください。

#### 1. 名前空間にラベルを指定する
Istioサイドカープロキシを使用するためには、Podが作成される名前空間にラベルを指定する必要があります。ラベルはIstioコンポーネントを配布する名前空間を識別するために使用されます。ラベルが設定された名前空間にPodが配布されると、Istioサイドカープロキシが自動的にPodに配布されます。

以下はdefault名前空間にラベルを設定する例です。
```
$ kubectl label namespace default istio-injection=enabled
namespace/default labeled
```

#### 2. アプリケーションコンポーネント配布する
アプリケーションコンポーネントを配布し、正常に配布されたか確認します。

下記のコマンドでアプリケーションを配布します。
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

コンポーネントが正常に配布されたか確認します。
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

下記のコマンドでアプリケーションが正常に動作するか確認します。
```
$ kubectl exec "$(kubectl get pod -l app=ratings -o jsonpath='{.items[0].metadata.name}')" -c ratings -- curl -sS productpage:9080/productpage | grep -o "<title>.*</title>"
<title>Simple Bookstore App</title>
```

### サービス表示
上のプロセスまで実行した時、サービスはクラスタ内のホストだけアクセスできる`ClusterIP`の形で設定されています。 Kubernetesではサービスを公開するために`NodePort`、`LoadBalancer`など様々なタイプの`Service`オブジェクトを提供していますが、Istioではより幅広い範囲のネットワークトラフィックを処理するためのgatewayオブジェクトを提供しています。下記の例ではIstioが基本的に提供するサンプルbookinfo gatewayを使用してサービスを公開する過程を示しています。Istio gatewayの詳しい説明は[Istio Gateway](https://istio.io/latest/docs/concepts/traffic-management/#gateways)を参照してください。

#### イングレスゲートウェイを構成する
サービスを公開するためのIstioイングレスゲートウェイを追加で構成します。

下記のコマンドでIstioイングレスゲートウェイコンポーネントを配布します。
```
$ kubectl apply -f samples/bookinfo/networking/bookinfo-gateway.yaml
gateway.networking.istio.io/bookinfo-gateway created
virtualservice.networking.istio.io/bookinfo created
```

`istioctl analyze`コマンドでイングレスゲートウェイ構成に問題がないか確認します。
```
$ istioctl analyze
✔ No validation issues found when analyzing namespace: default.
```

#### サービスにアクセスする
公開されたサービスにアクセスするため、ゲートウェイURL情報を確認します。ゲートウェイURLは基本的に`イングレスホスト:イングレスIP`の構成になっています。クラスタがインターネット上またはクローズドネットワーク環境に構成されたかによって、ゲートウェイURL情報を確認する方法が異なります。インターネット上に構成されたクラスタはロードバランサーの `EXTERNAL-IP` 情報に基づいてゲートウェイ URL が構成され、クローズドネットワーク環境に構成されたクラスタはノードポートに基づいてゲートウェイ URL が構成されます。

インターネット上に構成されたクラスタの場合、下記のコマンドを実行してゲートウェイURL情報を確認できます。
```
$ export INGRESS_HOST=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
$ export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].port}')
$ export GATEWAY_URL=$INGRESS_HOST:$INGRESS_PORT

$ echo "$GATEWAY_URL"
114.110.160.79:80
```

クローズドネットワークに構成されたクラスタの場合、下記のコマンドを実行してゲートウェイURL情報を確認します。
```
$ export INGRESS_PORT=$(kubectl -n istio-system get service istio-ingressgateway -o jsonpath='{.spec.ports[?(@.name=="http2")].nodePort}')
$ export INGRESS_HOST=$(kubectl get po -l istio=ingressgateway -n istio-system -o jsonpath='{.items[0].status.hostIP}')
$ export GATEWAY_URL=$INGRESS_HOST:$INGRESS_PORT

$ echo "$GATEWAY_URL"
172.16.0.42:30745
```

ゲートウェイURLにリクエストを送ってサービスが正常に公開されたか確認します。
```
$ curl http://114.110.160.79:80/productpage
<!DOCTYPE html>
<html>
  <head>
    <title>Simple Bookstore App</title>
<meta charset="utf-8">
...
```


### Istio削除
下記のコマンドを実行して例題で使ったbookinfoアプリケーション関連コンポーネントを削除します。
```
$ kubectl delete -f samples/bookinfo/networking/bookinfo-gateway.yaml
$ kubectl delete -f samples/bookinfo/platform/kube/bookinfo.yaml
```

クラスタからIstioの全てのコンポーネントを削除する場合は、下記のコマンドを実行します。
```
$ istioctl unintall --purge -y
$ kubectl delete namespace istio-system
$ kubectl label namespace default istio-injection-
```

Istioの全てのコンポーネントを削除するのではなく、特定のIstioコントロールプレーンだけを削除したい場合は、下記のコマンドを実行します。
```
$ istioctl uninstall <your original installation options>
```
