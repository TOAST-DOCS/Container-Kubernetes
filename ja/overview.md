<!-- pre-align:aligned sig=380a0a4ca9e2 -->

<a id="container-nhn-kubernetes-service-nks-overview"></a>
## Container > NHN Kubernetes Service(NKS) > 概要 { #container-nhn-kubernetes-service-nks-overview }
ここではKubernetesとは何か、そしてNHN Cloudで提供するNHN Kubernetes Service(NKS)について説明します。

<a id="kubernetes"></a>
## Kubernetes { #kubernetes }
Kubernetesは、コンテナ化されたワークロードとサービスを管理できるオープンソースのプラットフォームです。Kubernetesは次のような機能を提供します。

* サービスディスカバリーとロードバランシング
* ストレージオーケストレーション
* 自動化されたロールアウトとロールバック
* 自動化されたビンパッキング(bin packing)
* 自動化された復旧(self-healing)
* シークレットと構成管理

詳細な内容は、下記のKubernetes文書を参照してください。

* [Kubernetesとは？](https://kubernetes.io/docs/concepts/overview/)

<a id="kubernetes-cluster"></a>
## Kubernetesクラスター { #kubernetes-cluster }
Kubernetesクラスターは、相互に接続されて1つのユニットのように動作するコンピュータクラスターです。Kubernetesが提供する機能はクラスター単位で動作し、クラスター単位で設定できます。

<a id="configuration"></a>
### 構成 { #configuration }
Kubernetesクラスターはコントロールプレーンとノードで構成されます。

<a id="configuration-control-plane"></a>
#### コントロールプレーン
コントロールプレーンはクラスターの管理を担当します。アプリケーションをスケジューリングしたり、スケーリング、アップデートなど、クラスター内の全ての活動を管理します。一般的にコントロールプレーンのコンポーネントは別のマシン(仮想または物理マシン)で起動します。高可用性を保障するために、1つのクラスターに複数のコントロールプレーンコンポーネントを構成することもできます。

<a id="configuration-node"></a>
#### ノード
ノードは、ユーザーのアプリケーションが起動するワーカーマシンです。1つのクラスターには複数のノードが存在できます。ノードはコントロールプレーンと接続すると動作します。コントロールプレーンのアプリケーション起動、停止などのコマンドに応じてそのまま実行します。


<a id="nhn-kubernetes-service-nks"></a>
## NHN Kubernetes Service(NKS) { #nhn-kubernetes-service-nks }
NHN Kubernetes Service(NKS)は、クラウドでKubernetesを正しく安全に起動できるようにKubernetesクラスターを作成し、管理できるサービスです。ユーザーはWebコンソールを利用してNHN Cloudに最適なKubernetesクラスターを作成し、管理できます。安全かつ効率的に運営を行えるように、コントロールプレーンはNHN Cloudで管理し、ユーザーはノードとサービス、 Pod(ポッド)などを管理します。

NHN Kubernetes Service(NKS)の主な機能は次のとおりです。

* NHN Cloudに最適なKubernetesクラスターの作成と管理
    * NHN Cloudの基盤サービスと連携
    * ロードバランサーを利用したサービス公開
    * ブロックストレージと連携したパシステントボリューム(Persistent Volume、PV)をサポート

* 高可用性を保障するコントロールプレーン管理

* Webコンソールを使って簡単に操作
    * クラスター作成、削除、照会
    * ノードグループ作成、削除、照会
    * kubectl設定をサポート
