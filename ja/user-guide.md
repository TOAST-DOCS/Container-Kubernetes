<!-- pre-align:aligned sig=98b43adf5be7 -->

<a id="container-nhn-kubernetes-service-nks-user-guide"></a>
## Container > NHN Kubernetes Service(NKS) > 使用ガイド { #container-nhn-kubernetes-service-nks-user-guide }

<a id="cluster-headings"></a>
## クラスター { #cluster-headings }
クラスターは、ユーザーの Kubernetes を構成するインスタンスのグループです。

<a id="cluster-create"></a>
### クラスター作成 { #cluster-create }
NHN Kubernetes Service(NKS) を使用するには、まずクラスターを作成する必要があります。

> [注意] クラスター使用のための権限設定<br>
> クラスターを作成するユーザーは、対象プロジェクトに対して基本インフラサービスの **Infrastructure ADMIN** または **Infrastructure LoadBalancer ADMIN** または **Infrastructure NKS ADMIN** 権限を持つ必要があります。
> 該当する権限がある場合にのみ、基本インフラサービスをベースとするクラスターを正常に作成および活用できます。また、いずれかの権限を持った状態で他の権限が追加されても使用上の問題はありません。
> 権限設定については、[プロジェクトメンバー管理](/nhncloud/ja/console-user-guide/#_3)を参照してください。

**Container > NHN Kubernetes Service(NKS)** ページで **[クラスター作成]** をクリックすると、クラスター作成ページが表示されます。クラスター作成に必要な項目は次のとおりです。

| 項目 | 説明 |
| --- | --- |
| クラスター名 | Kubernetes クラスターの名前。32 文字以内で英小文字、数字、「-」のみ入力可能です。英小文字で始まり、英小文字または数字で終わる必要があります。RFC 4122 標準の UUID 形式は使用できません。 |
| Kubernetes バージョン | 使用する Kubernetes バージョン |
| VPC | クラスターに接続する VPC ネットワーク |
| サブネット | VPC に定義されたサブネットのうち、クラスターを構成するインスタンスに接続するサブネット |
| NCRサービスゲートウェイ | NCR タイプのサービスゲートウェイ<br>（サブネットにインターネットゲートウェイが接続されていない場合に限る） |
| OBSサービスゲートウェイ | OBS タイプのサービスゲートウェイ<br>（サブネットにインターネットゲートウェイが接続されていない場合に限る） |
| K8sサービスネットワーク | クラスターの service object CIDR 設定 |
| Podネットワーク | クラスターの Pod ネットワーク設定 |
| Podサブネットサイズ | クラスターの Pod サブネットサイズ設定 |
| Kubernetes API エンドポイント | Public: エンドポイントにドメインアドレスを割り当て、フローティング IP を接続<br>Private: エンドポイントを内部ネットワークアドレスに設定 |
| 強化されたセキュリティルール | ワーカーノードのセキュリティグループ作成時に必須セキュリティルールのみ作成。クラスターワーカーノード必須セキュリティルール項目を参照<br>True: 必須セキュリティルールのみ作成<br>False: 必須セキュリティルールとすべてのポートを許可するセキュリティルールを作成 |
| イメージ | クラスターを構成するインスタンスに使用するイメージ |
| Availability Zone | デフォルトノードグループのインスタンスを作成するゾーン |
| インスタンスタイプ | デフォルトノードグループのインスタンス仕様 |
| ノード数 | デフォルトノードグループのインスタンス数 |
| キーペア | デフォルトノードグループへのアクセスに使用するキーペア |
| ブロックストレージタイプ | デフォルトノードグループのインスタンスのブロックストレージ種類 |
| ブロックストレージサイズ | デフォルトノードグループのインスタンスのブロックストレージサイズ |
| 追加ネットワーク | デフォルトワーカーノードグループに作成する追加ネットワーク/サブネット |

必要な情報を入力し、**[クラスター作成]** をクリックするとクラスターの作成が開始されます。クラスター一覧で状態を確認できます。作成には約 10 分程度かかります。クラスターの設定によってはさらに時間がかかる場合があります。

> [注意]
> VPC ネットワークサブネット、K8sサービスネットワーク、Podネットワークの CIDR は、以下の制約事項に該当しないように設定する必要があります。
>  - リンクローカルアドレス帯域（169.254.0.0/16）と重複できません。
>  - VPC ネットワークサブネット、追加ネットワークサブネット、Podネットワーク、K8sサービスネットワーク帯域は重複できません。
>  - NKS 内部で使用している IP 帯域（198.18.0.0/19）と重複できません。
>  - /24 より大きい CIDR ブロックは入力できません（例: /26、/30 は使用できません）。
>  - v1.23.3 以下のクラスターの場合、Docker BIP（bridged IP range）と重複できません（172.17.0.0/16）。
>
> クラスター作成時に設定したサービスゲートウェイは削除しないでください。
>  - 選択したサブネットがインターネットゲートウェイに接続されていない場合、NCRサービスゲートウェイと OBSサービスゲートウェイの設定が必要です。
>  - この 2 つのサービスゲートウェイは、NKS クラスターの構成および基本機能に必要なイメージ/バイナリを取得する際に使用されます。
>  - クラスター作成時に設定したサービスゲートウェイを削除すると、クラスターが正常に動作しなくなります。
>  - クラスター作成時に設定したサービスゲートウェイが削除された場合は、クラスターごとにサービスゲートウェイを再設定する必要があります。
>  - クラスターのサービスゲートウェイは、クラスターの **[基本情報]** タブのサービスゲートウェイ照会結果の横にある **[変更]** ボタンをクリックして再設定できます。
>
> クラスター作成時に設定したサブネットのインターネットゲートウェイ接続状態を変更しないでください。
>  - クラスター作成時に設定したサブネットのインターネットゲートウェイ接続状態によって、イメージ/バイナリを取得するレジストリが異なります。
>  - クラスター作成後にサブネットのインターネットゲートウェイ接続状態が変更されると、設定されたレジストリに接続できなくなり、クラスターが正常に動作しなくなります。

> [最大作成可能なノード数]
> クラスター作成時に作成可能な最大ノード数は、Podネットワークと Podサブネットサイズの設定によって決まります。
> 計算式: 2 ^ (Podサブネットサイズ - Podネットワークのホストビット数) - 3
> 例:
>  - Podサブネットサイズ = 24
>  - Podネットワーク = 10.100.0.0/16
>  - 計算: 2 ^ (24 - 16) - 3 = 最大 253 ノード作成可能

> [各ノードあたりの Pod に割り当て可能な最大 IP 数]
> 1 つのノードで使用可能な最大 IP 数は、Podサブネットサイズの設定によって決まります。
> 計算式: 2 ^ (32 - pods_network_subnet) - 2
> 例:
>  - Podサブネットサイズ = 24
>  - 計算: 2 ^ (32 - 24) - 2 = 最大 254 個の IP 使用可能

> [クラスターで Pod に割り当て可能な最大 IP 数]
> 計算式: 各ノードあたりの Pod に割り当て可能な最大 IP 数 × 最大作成可能なノード数
> 例:
>  - Podサブネットサイズ = 24
>  - Podネットワーク = 10.100.0.0/16
>  - 計算: 254（各ノードあたりの Pod に割り当て可能な最大 IP 数）× 253（最大作成可能なノード数）= 最大 64,262 個の IP 使用可能

<a id="cluster-show"></a>
### クラスター照会 { #cluster-show }
作成したクラスターは、**Container > NHN Kubernetes Service(NKS)** ページで確認できます。クラスター一覧には、各クラスターの概要情報が表示されます。

| 項目 | 説明 |
| --- | --- |
| クラスター名 | クラスターの名前 |
| ノード数 | クラスターの全ワーカーノード数 |
| Kubernetes バージョン | Kubernetes バージョン情報 |
| kubeconfigファイル | クラスターを制御するための kubeconfigファイルダウンロードボタン |
| 作業状態 | クラスターに対して実行したコマンドの作業状態 |
| k8s API状態 | Kubernetes API エンドポイントの動作状態 |
| k8s Node状態 | Kubernetes Node リソースの状態 |

作業状態のアイコンの意味は次のとおりです。

| アイコン | 意味 |
| --- | --- |
| 緑色のソリッドアイコン | 作業正常終了 |
| 円形回転アイコン | 作業進行中 |
| 赤色のソリッドアイコン | 作業失敗 |
| 灰色のソリッドアイコン | クラスタ使用不可 |

k8s API状態のアイコンの意味は次のとおりです。

| アイコン | 意味 |
| --- | --- |
| 緑色のソリッドアイコン | 正常動作中 |
| 黄色のソリッドアイコン | 情報の有効期間（5 分）が残り少なく、情報が正確でない可能性があります |
| 赤色のソリッドアイコン | Kubernetes API エンドポイントが正常に動作していないか、情報の有効期間が満了しています |

k8s Node状態のアイコンの意味は次のとおりです。

| アイコン | 意味 |
| --- | --- |
| 緑色のソリッドアイコン | クラスターのすべてのノードが Ready 状態です |
| 黄色のソリッドアイコン | Kubernetes API エンドポイントが正常に動作していないか、クラスター内に NotReady 状態のノードが存在します |
| 赤色のソリッドアイコン | クラスターのすべてのノードが NotReady 状態です |

クラスターを選択すると、下部にクラスター情報が表示されます。

| 項目 | 説明 |
| --- | --- |
| クラスター名 | Kubernetes クラスターの名前と ID |
| ノード数 | クラスターを構成するすべてのノードインスタンス数 |
| Kubernetes バージョン | 使用中の Kubernetes バージョン |
| Kubernetes 証明書 | クラスター証明書の有効期間および有効期限 |
| CNI | 使用中の Kubernetes CNI 種類 |
| K8sサービスネットワーク | クラスターの service object CIDR 設定 |
| Podネットワーク | 使用中の Kubernetes Pod ネットワーク設定 |
| Podサブネットサイズ | 使用中の Kubernetes Pod サブネットサイズ設定 |
| VPC | クラスターに接続された VPC ネットワーク |
| サブネット | クラスターを構成するノードインスタンスに接続されたサブネット |
| API エンドポイント | クラスターにアクセスして操作するための API エンドポイント URI |
| 設定ファイル | クラスターにアクセスして操作するために必要な設定ファイルダウンロードボタン |

<a id="cluster-delete"></a>
### クラスター削除 { #cluster-delete }
削除するクラスターを選択し、**[クラスター削除]** をクリックすると削除が開始されます。削除には約 5 分程度かかります。クラスターの状態によってはさらに時間がかかる場合があります。

<a id="change-keypair"></a>
### クラスターキーペア変更 { #change-keypair }

クラスターに属するすべてのワーカーノードのキーペアを変更します。設定するキーペアは、ログインしているユーザーのキーペアの中から選択します。キーペアを変更すると、以下の内容が適用されます。

* すべてのワーカーノード VM に選択したキーペアが設定されます。
* 設定したキーペアを使用して、すべてのワーカーノード VM に SSH で接続できます。
* 各ワーカーノードインスタンスのキーペアは `managed-by-nks` と表示されます。

キーペアが設定されたクラスターは、サービスユーザーの権限で動作します。サービスユーザーは NKS サービスレベルで管理される内部ユーザーであり、NKS の機能動作およびサービス連携がサービスユーザーの権限で動作します。サービスユーザーの権限で動作するクラスターは、オーナーを変更・管理する必要はありません。

> [注意]
> * 一般ユーザーがオーナーに設定されているクラスターは、キーペア変更機能を使用してサービスユーザーの権限で動作するように変更できます。
> * クラスターオーナー変更機能は提供されなくなりました。クラスターがサービスユーザーの権限で動作できるようにするには、キーペア変更機能を使用してください。

<a id="nodegroup-headings"></a>
## ノードグループ { #nodegroup-headings }
ノードグループは、Kubernetes を構成するワーカーノードインスタンスのグループです。

<a id="nodegroup-show"></a>
### ノードグループ照会 { #nodegroup-show }
クラスター一覧でクラスター名をクリックすると、ノードグループ一覧を確認できます。ノードグループ一覧には、各ノードグループの概要情報が表示されます。

| 項目 | 説明 |
| --- | --- |
| ノードグループ名 | ノードグループの名前 |
| ノード数 | ノードグループに属するノード数 |
| Kubernetes バージョン | ノードグループに適用された Kubernetes バージョン情報 |
| Availability Zone | ノードグループに適用された Availability Zone 情報 |
| インスタンスタイプ | ノードグループのインスタンスタイプ |
| イメージタイプ | ノードグループのイメージタイプ |
| 作業状態 | ノードグループに対して実行したコマンドの作業状態 |
| k8s Node状態 | ノードグループに属する Kubernetes Node リソースの状態 |

作業状態のアイコンの意味は次のとおりです。

| アイコン | 意味 |
| --- | --- |
| 緑色のソリッドアイコン | 作業正常終了 |
| 円形回転アイコン | 作業進行中 |
| 赤色のソリッドアイコン | 作業失敗 |
| オレンジ色のソリッドアイコン | 一部のノードの作業が成功 |
| 灰色のソリッドアイコン | クラスターおよびノードグループ使用不可 |

k8s Node状態のアイコンの意味は次のとおりです。

| アイコン | 意味 |
| --- | --- |
| 緑色のソリッドアイコン | ノードグループのすべてのノードが Ready 状態です |
| 黄色のソリッドアイコン | Kubernetes API エンドポイントが正常に動作していないか、ノードグループ内に NotReady 状態のノードが存在します |
| 赤色のソリッドアイコン | ノードグループのすべてのノードが NotReady 状態です |

ノードグループを選択すると、下部にノードグループ情報が表示されます。

**[基本情報]** タブでは、次の情報を確認できます。

| 項目 | 説明 |
| --- | --- |
| ノードグループ名 | ノードグループの名前と ID |
| クラスター名 | ノードグループが属するクラスターの名前と ID |
| Kubernetes バージョン | 使用中の Kubernetes バージョン |
| Availability Zone | ノードグループのインスタンスが作成されたゾーン |
| インスタンスタイプ | ノードグループのインスタンス仕様 |
| イメージタイプ | ノードグループのインスタンスに使用したイメージ種類 |
| ブロックストレージサイズ | ノードグループのインスタンスのブロックストレージサイズ |
| 作成日時 | ノードグループが作成された日時 |
| 更新日時 | ノードグループが最後に更新された日時 |

* ノード一覧
**[ノード一覧]** タブでは、ノードグループを構成するインスタンスの一覧を確認できます。

<a id="nodegroup-create"></a>
### ノードグループ作成 { #nodegroup-create }
クラスターを作成するとデフォルトのノードグループが作成されますが、必要に応じて追加のノードグループを作成できます。デフォルトのノードグループのインスタンスより高い仕様のコンテナ実行環境が必要な場合や、スケールアウト（拡張）のためにより多くのワーカーノードインスタンスが必要な場合は、追加のノードグループを作成して使用できます。ノードグループ一覧ページで **[ノードグループ作成]** ボタンをクリックすると、ノードグループ作成ページが表示されます。ノードグループ作成に必要な項目は次のとおりです。

| 項目 | 説明 |
| --- | --- |
| Availability Zone | クラスターを構成するインスタンスを作成するゾーン |
| ノードグループ名 | 追加ノードグループの名前。32 文字以内で英小文字、数字、「-」のみ入力可能です。英小文字で始まり、英小文字または数字で終わる必要があります。RFC 4122 標準の UUID 形式は使用できません。 |
| インスタンスタイプ | 追加ノードグループのインスタンス仕様 |
| ノード数 | 追加ノードグループのインスタンス数 |
| キーペア | 追加ノードグループへのアクセスに使用するキーペア |
| ブロックストレージタイプ | 追加ノードグループのインスタンスのブロックストレージ種類 |
| ブロックストレージサイズ | 追加ノードグループのインスタンスのブロックストレージサイズ |
| 追加ネットワーク | デフォルトワーカーノードグループに作成する追加ネットワーク/サブネット |

必要な情報を入力し、**[ノードグループ作成]** ボタンをクリックするとノードグループの作成が開始されます。ノードグループ一覧で状態を確認できます。ノードグループの作成には約 5 分程度かかります。ノードグループの設定によってはさらに時間がかかる場合があります。

> [注意]
> 該当するクラスターを作成したユーザーのみがノードグループを作成できます。

<a id="nodegroup-delete"></a>
### ノードグループ削除 { #nodegroup-delete }
ノードグループ一覧で削除するノードグループを選択し、**[ノードグループ削除]** ボタンをクリックすると削除が開始されます。ノードグループの削除には約 5 分程度かかります。ノードグループの状態によってはさらに時間がかかる場合があります。

ノードグループに含まれるすべてのノードは、次の順序で削除されます。
* 該当ノードが LoadBalancer タイプの Service のメンバーである場合、該当 LB のメンバーを INACTIVE 状態にします（プラットフォームバージョン 1.202602.0 以上の場合にのみサポート）。
* 該当ノードが drain されます。
* 該当ノードが Kubernetes ノードリソースから削除されます。
* 該当ノードがインスタンスレベルで削除されます。

<a id="nodegroup-scale-out"></a>
### ノードグループへのノード追加 { #nodegroup-scale-out }
動作中のノードグループにノードを追加できます。ノードグループ情報照会ページのノード一覧タブをクリックすると、現在のノード一覧が表示されます。ノード追加ボタンをクリックしてノード数を入力すると、ノードが追加されます。

> [注意]
> オートスケーラーが有効になっているノードグループは、手動でノードを追加することはできません。

<a id="nodegroup-scale-in"></a>
### ノードグループからのノード削除 { #nodegroup-scale-in }
動作中のノードグループからノードを削除できます。ノードグループ情報照会ページのノード一覧タブをクリックすると、現在のノード一覧が表示されます。ノード一覧から削除するノードを選択し、ノード削除ボタンをクリックすると確認ダイアログが表示されます。削除するノード名を再度確認し、確認ボタンをクリックするとノードが削除されます。

ノードグループに含まれるすべてのノードは、次の順序で削除されます。
* 該当ノードが LoadBalancer タイプの Service のメンバーである場合、該当 LB のメンバーを INACTIVE 状態にします（プラットフォームバージョン 1.202602.0 以上の場合にのみサポート）。
* 該当ノードが drain されます。
* 該当ノードが Kubernetes ノードリソースから削除されます。
* 該当ノードがインスタンスレベルで削除されます。

> [注意]
> オートスケーラーが有効になっているノードグループは、手動でノードを削除することはできません。

<a id="node-start-stop"></a>
### ノードの停止と起動 { #node-start-stop }
ノードグループに属するノードの一部を停止し、停止されたノードを再起動できます。ノードグループ情報照会ページのノードリストタブをクリックすると、現在のノードリストが表示されます。停止するノードを選択してノード停止ボタンをクリックすると、ノードが停止されます。停止されたノードを選択してノード起動ボタンをクリックすると、ノードが再起動されます。

<a id="node-start-stop-action-process"></a>
#### 動作過程

起動状態のノードを停止すると、次の順序で動作します。

* 該当ノードが LoadBalancer タイプの Service のメンバーである場合、該当 LB のメンバーを INACTIVE 状態にします。(プラットフォームバージョン 1.202602.0 以上の場合のみサポート)
* 該当ノードが drain されます。
* 該当ノードが Kubernetes ノードリソースから削除されます。
* 該当ノードをインスタンスレベルで SHUTDOWN 状態にします。

停止状態のノードを起動すると、次の順序で動作します。

* 該当ノードをインスタンスレベルで ACTIVE 状態にします。
* 該当ノードが Kubernetes ノードリソースに再追加されます。


<a id="node-start-stop-constraints"></a>
#### 制約事項

ノードの停止と起動機能には次の制約事項があります。

* 起動状態のノードを停止でき、停止状態のノードを起動できます。
* ワーカーノードグループ内のすべてのノードを停止することはできません。
* オートスケーラーが有効化されているノードグループはノードを停止できません。
* 停止されたノードが存在するノードグループはオートスケーラーを有効化できません。
* 停止されたノードが存在するノードグループはアップグレードできません。


<a id="node-start-stop-display-status"></a>
#### 状態表示

ノードの状態に応じて、ノードリストタブの状態アイコンが表示されます。アイコンの色ごとの状態は次のとおりです。

* 緑色: 起動状態のノード
* グレー: 停止状態のノード
* 赤色: 異常状態のノード

<a id="use-gpu-nodegroup"></a>
### GPU ノードグループの使用 { #use-gpu-nodegroup }
Kubernetes を通じた GPU ベースのワークロード実行が必要な場合、GPU インスタンスで構成されたノードグループを作成できます。
クラスターまたはノードグループの作成時にインスタンスタイプを選択する際、`g2` タイプを選択すると GPU ノードグループを作成できます。

> [注記]
> NHN Cloud GPU インスタンスで提供される GPU は NVIDIA 系です。([使用可能な GPU 仕様の確認](/Compute/GPU%20Instance/ja/overview/#gpu))
> NVIDIA GPU の利用のために Kubernetes に必要な nvidia-device-plugin は、GPU ノードグループ作成時に自動的にインストールされます。

作成された GPU ノードの基本的な設定状態の確認および簡単な動作テストには、次の方法を使用します。

<a id="use-gpu-nodegroup-node-level-status-check"></a>
#### ノードレベルの状態確認
GPU ノードに接続した後、`nvidia-smi` コマンドを実行します。
次のような内容が出力されれば、GPU ドライバーが正常に動作しています。

```
$ nvidia-smi
Mon Jul 27 14:38:07 2020
+-----------------------------------------------------------------------------+
| NVIDIA-SMI 418.152.00   Driver Version: 418.152.00   CUDA Version: 10.1     |
|-------------------------------+----------------------+----------------------+
| GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
|===============================+======================+======================|
|   0  Tesla T4            Off  | 00000000:00:05.0 Off |                    0 |
| N/A   30C    P8     9W /  70W |      0MiB / 15079MiB |      0%      Default |
+-------------------------------+----------------------+----------------------+

+-----------------------------------------------------------------------------+
| Processes:                                                       GPU Memory |
|  GPU       PID   Type   Process name                             Usage      |
|=============================================================================|
|  No running processes found                                                 |
+-----------------------------------------------------------------------------+ 
```

<a id="use-gpu-nodegroup-kubernetes-level-status-check"></a>
#### Kubernetes レベルの状態確認
`kubectl` コマンドを使用して、クラスターレベルで使用可能な GPU リソース情報を確認します。
以下は、各ノードで使用可能な GPU コア数を出力するコマンドおよび実行結果です。

```
$ kubectl get nodes -A -o custom-columns='NAME:.metadata.name,GPU Allocatable:.status.allocatable.nvidia\.com/gpu,GPU Capacity:.status.capacity.nvidia\.com/gpu'
NAME                                       GPU Allocatable   GPU Capacity
my-cluster-default-w-vdqxpwisjjsk-node-1   1                 1
```

<a id="use-gpu-nodegroup-sample-workload-execution-for-gpu-testing"></a>
#### GPU テスト用サンプルワークロードの実行
Kubernetes クラスターに属する GPU ノードは、CPU とメモリ以外に `nvidia.com/gpu` という名前のリソースを提供します。
GPU を使用したい場合は、`nvidia.com/gpu` リソースを割り当てるように、以下のサンプルファイルのように入力します。

* resnet.yaml
```
apiVersion: v1
kind: Pod
metadata:
  name: resnet-gpu-pod
spec:
  imagePullSecrets:
    - name: nvcr.dgxkey
  containers:
    - name: resnet
      image: nvcr.io/nvidia/tensorflow:18.07-py3
      command: ["mpiexec"]
      args: ["--allow-run-as-root", "--bind-to", "socket", "-np", "1", "python", "/opt/tensorflow/nvidia-examples/cnn/resnet.py", "--layers=50", "--precision=fp16", "--batch_size=64", "--num_iter=90"]
      resources:
        limits:
          nvidia.com/gpu: 1
``` 

上記のファイルを実行すると、次のような結果を確認できます。

```
$ kubectl create -f resnet.yaml
pod/resnet-gpu-pod created

$ kubectl get pods resnet-gpu-pod
NAME             READY   STATUS    RESTARTS   AGE
resnet-gpu-pod   0/1     Running   0          17s 

$ kubectl logs resnet-gpu-pod -n default -f
PY 3.5.2 (default, Nov 23 2017, 16:37:01)
[GCC 5.4.0 20160609]
TF 1.8.0
Script arguments:
  --layers 50
  --display_every 10
  --iter_unit epoch
  --batch_size 64
  --num_iter 100
  --precision fp16
Training
WARNING:tensorflow:Using temporary folder as model directory: /tmp/tmpjw90ypze
2020-07-31 00:57:23.020712: I tensorflow/stream_executor/cuda/cuda_gpu_executor.cc:898] successful NUMA node read from SysFS had negative value (-1), but there must be at least one NUMA node, so returning NUMA node zero
2020-07-31 00:57:23.023190: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1356] Found device 0 with properties:
name: Tesla T4 major: 7 minor: 5 memoryClockRate(GHz): 1.59
pciBusID: 0000:00:05.0
totalMemory: 14.73GiB freeMemory: 14.62GiB
2020-07-31 00:57:23.023226: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1435] Adding visible gpu devices: 0
2020-07-31 00:57:23.846680: I tensorflow/core/common_runtime/gpu/gpu_device.cc:923] Device interconnect StreamExecutor with strength 1 edge matrix:
2020-07-31 00:57:23.846743: I tensorflow/core/common_runtime/gpu/gpu_device.cc:929]      0
2020-07-31 00:57:23.846753: I tensorflow/core/common_runtime/gpu/gpu_device.cc:942] 0:   N
2020-07-31 00:57:23.847023: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1053] Created TensorFlow device (/job:localhost/replica:0/task:0/device:GPU:0 with 14151 MB memory) -> physical GPU (device: 0, name: Tesla T4, pci bus id: 0000:00:05.0, compute capability: 7.5)
  Step Epoch Img/sec   Loss  LR
     1   1.0     3.1  7.936  8.907 2.00000
    10  10.0    68.3  1.989  2.961 1.65620
    20  20.0   214.0  0.002  0.978 1.31220
    30  30.0   213.8  0.008  0.979 1.00820
    40  40.0   210.8  0.095  1.063 0.74420
    50  50.0   211.9  0.261  1.231 0.52020
    60  60.0   211.6  0.104  1.078 0.33620
    70  70.0   211.3  0.340  1.317 0.19220
    80  80.0   206.7  0.168  1.148 0.08820
    90  90.0   210.4  0.092  1.073 0.02420
   100 100.0   210.4  0.001  0.982 0.00020
```

> [注記]
> GPU が不要なワークロードが GPU ノードに割り当てられるのを防ぎたい場合は、[Taint および Toleration の概要](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/)を参照してください。

<a id="autoscaler"></a>
### オートスケーラー { #autoscaler }
オートスケーラーは、ノードグループの利用可能なリソースが不足している場合、またはノードの使用率が一定レベル以下に維持される場合に、ノード数を自動的に調整する機能です。この機能はノードグループごとに設定でき、それぞれ独立して動作します。NKS では 2 種類のオートスケーラーをサポートしています。

* 指標ベースのオートスケーラー
* クラスターオートスケーラー

オートスケーラー機能はノードグループごとに設定して動作します。機能は以下のパスで設定できます。

* クラスター作成時にデフォルトノードグループに設定
* ノードグループ追加時に追加ノードグループに設定
* 作成済みのノードグループに設定

> [注意]
> オートスケーラーが有効化されているノードグループは、手動でノードを追加または削除することはできません。
> オートスケーラーは重複して有効化することはできません。

**用語の整理**
オートスケーラー機能で使用する用語とその意味は次のとおりです。

| 用語 | 意味 |
| --- | --- |
| スケールアウト | ノード数を増加させることをいいます |
| スケールイン | ノード数を減少させることをいいます |

<a id="metric-base-autoscaler"></a>
#### 指標ベースのオートスケーラー
指標ベースのオートスケーラーは、NHN Cloud の [Cloud Monitoring](/Monitoring/Cloud%20Monitoring/ja/overview/) サービスをベースに動作します。ワーカーノードにインストールされた指標収集エージェントが 1 分周期でシステム指標を Cloud Monitoring に送信し、収集された指標が設定したしきい値を超過または下回った場合に、自動的にノードを追加または削除します。スケールアウト (Scale Out) とスケールイン (Scale In) の機能はそれぞれ独立して有効化できます。

<a id="metric-base-autoscaler-set"></a>
##### 指標ベースのオートスケーラーの設定
指標ベースのオートスケーラーを有効化する際、以下の項目を設定できます。

**スケールアウトの設定**

| 設定項目 | 意味 | 有効範囲 | デフォルト値 |
| --- | --- | --- | --- |
| 最大ノード数 | スケールアウト可能な最大ノード数 | 1〜10 | 10 |
| 有効化 | ノードスケールアウト オートスケーラーの有効/無効を設定 | 有効/無効 | 無効 |

**スケールインの設定**

| 設定項目 | 意味 | 有効範囲 | デフォルト値 |
| --- | --- | --- | --- |
| 最小ノード数 | スケールイン可能な最小ノード数 | 1〜10 | 10 |
| 有効化 | ノードスケールイン オートスケーラーの有効/無効を設定 | 有効/無効 | 無効 |

**共通設定**

| 設定項目 | 意味 | 有効範囲 | デフォルト値 | 単位 |
| --- | --- | --- | --- | --- |
| ルール演算子 | オートスケーリング発動条件間に適用する演算子の設定<br>AND：すべての条件を満たした場合に発動<br>OR：いずれか 1 つの条件を満たした場合に発動 | AND/OR | OR | - |
| オートスケーリング待機時間 | 前回のスケール完了後、次のスケールまで待機する最小時間（スケールアウト・スケールインそれぞれ個別に設定可能） | 1〜60 | 10 | 分 |
| ノードパフォーマンス指標 | モニタリング対象の指標設定（下表参照） | 指標の種類 | 必須設定 | - |
| ノード調整数 | オートスケーリング発生時に追加/削除するノード数 | 1〜10 | 1 | 台 |
| しきい値設定 | 条件発動のための指標しきい値 | 指標ごと | 必須設定 | - |
| しきい値維持時間 | しきい値状態が設定時間（2〜60 分）以上継続した場合にスケール動作を実行 | 2〜60 | 必須設定 | 分 |

**ノードパフォーマンス指標**

| システムリソース | 提供する統計データ | 単位 |
| --- | --- | --- |
| CPU 使用率 | ノードグループに属するすべてのノードの CPU 使用量の平均 | % |
| メモリ使用率 | ノードグループに属するすべてのノードのメモリ使用量の平均 | % |
| ディスク転送率 (読み取り) | ノードグループに属するすべてのノードの 1 秒あたりのディスク読み取りデータ量の平均 | Bytes/s |
| ディスク転送率 (書き込み) | ノードグループに属するすべてのノードの 1 秒あたりのディスク書き込みデータ量の平均 | Bytes/s |
| ネットワーク転送率 (送信) | スケーリンググループに属するすべてのインスタンスの 1 秒あたりのネットワーク送信データ量の平均 | Bytes/s |
| ネットワーク転送率 (受信) | スケーリンググループに属するすべてのインスタンスの 1 秒あたりのネットワーク受信データ量の平均 | Bytes/s |

<a id="metric-base-autoscaler-resize"></a>
##### スケールアウトおよびスケールインの条件
以下の条件をすべて満たした場合、ノードをスケールアウトします。

* 選択したノードパフォーマンス指標がしきい値を超過した状態で、しきい値維持時間以上継続している
* 現在のノード数 < 最大ノード数
* オートスケーリング待機時間が経過している

以下の条件をすべて満たした場合、ノードをスケールインします。

* ノードパフォーマンス指標がしきい値を下回った状態で、しきい値維持時間以上継続している
* 現在のノード数 > 最小ノード数
* オートスケーリング待機時間が経過している

> [注記]
> オートスケーリング待機時間は、スケールアウトポリシーとスケールインポリシーそれぞれに指定できます。
> 通常、スケールアウト待機時間を短く設定することで、急激な負荷の上昇に即座に対応できます。
> 反対に、スケールイン待機時間は長く設定してインスタンスを徐々に削減することで、安定性を確保します。
> サービスの負荷状況を継続的にモニタリングし、適切なポリシーを設定することで、インスタンスの無駄遣いを防ぐことができます。
> 特定の 1 ノードのみが条件を満たす場合は、ポリシーは発動しません。ノードグループに属するすべてのノードの平均値で計算されます。
> 指定されたパフォーマンス指標がしきい値維持時間の間、基準値を超過し続けているかどうかを継続的に監視し、ポリシーの発動可否を決定します。
> たとえば、条件が「CPU 使用率が 90% 以上」でしきい値維持時間が 5 分の場合、5 分間 CPU 使用率が 90% を下回らなかった場合にポリシーが発動します。

> [ノードスケールインに関する注記]
> 指標ベースのオートスケーラーがスケールインを実行する際は、最も直近に作成されたノードから順に削除します。

<a id="metric-base-autoscaler-example"></a>
##### 動作例

**スケールアウトポリシー**

| 設定項目 | 設定値 |
| --- | --- |
| 最大ノード数 | 7 台 |
| スケールアウト ノード調整数 | 3 台 |
| スケールアウト後の待機時間 | 5 分 |
| スケールアウト条件: 指標 | CPU |
| スケールアウト条件: しきい値維持時間 | 5 分 |
| スケールアウト条件: しきい値 | 70% 以上 |

**スケールインポリシー**

| 設定項目 | 設定値 |
| --- | --- |
| 最小ノード数 | 3 台 |
| スケールイン ノード調整数 | 1 台 |
| スケールイン後の待機時間 | 10 分 |
| スケールイン条件: 指標 | CPU |
| スケールイン条件: しきい値維持時間 | 2 分 |
| スケールイン条件: しきい値 | 30% 以下 |

**動作サマリー**

* 現在のノードグループのノード数: 5 台
* 5 台のノードの CPU 使用量平均が 70% 以上の状態が 5 分間継続し、ノードのスケールアウトが要求される
* スケールアウトポリシーのノード調整数は 3 台だが、最大ノード数が 7 台のため、実際には +2 台のスケールアウトが実行される（ノード数: 5 → 7）
* ノードスケールアウト作業完了から 5 分後、7 台のノードの CPU 使用量平均が 30% 以下の状態が 2 分間継続し、ノードのスケールインが要求される
* スケールイン後の待機時間が 10 分であるため、要求が拒否される
* 10 分経過後にノードスケールインが実行される
* スケールインポリシーのノード調整数が 1 台のため、ノードを 1 台削除する（ノード数: 7 → 6）
* スケールイン後の 10 分間の待機中は、追加のスケールインは発生しない

**動作プロセスの詳細**

| 時刻 (分) | CPU 平均 | ノード数 | スケール状態 | 説明 |
| ------ | ------ | ---- | ------ | --- |
| 0〜3 | 65% | 5 | – | しきい値 (70%) 未満 |
| 4 | 72% | 5 | – | スケールアウト条件のしきい値以上 → しきい値維持時間 5 分の計測開始 |
| 4〜8 | 73% | 5 | – | スケールアウト条件のしきい値以上の状態が 5 分間維持され、スケールアウト条件を充足 |
| 8 | 76% | 5 → 7 | スケールアウト要求 | スケールアウト ノード調整数 3 台だが最大ノード数 7 台の制限により、実際は +2 台<br>ノード追加作業開始 |
| 8〜13 | 65% | 7 | – | ノード追加作業完了<br>作業が完了した 13 分が「スケールアウト/スケールイン後の待機」条件の開始時点として設定される |
| 13 | 28% | 7 | – | スケールアウト条件のしきい値以下 → しきい値維持時間 2 分の計測開始 |
| 15 | 27% | 7 | スケールイン要求 (拒否) | スケールイン条件のしきい値以下の状態が 2 分間維持され、スケールイン条件を充足<br>ただしスケールイン後の待機 10 分 (13→23) が進行中のため拒否 |
| 15〜23 | 27% | 7 | – | スケールイン後の待機時間継続 |
| 23 | 27% | 7 → 6 | スケールイン | スケールイン後の待機時間 10 分が満了、スケールイン条件を引き続き充足<br>スケールイン ノード調整数が 1 台のためノードを 1 台削除 |
| 24 | 28% | 6 |  | ノードスケールイン作業完了<br>作業が完了した 24 分が「スケールアウト/スケールイン後の待機」条件の開始時点として設定される |
| 24〜 | 28% | 6 | – | スケールアウト条件のしきい値以下 → しきい値維持時間 2 分の計測開始<br>その後、スケールイン後の待機 10 分 (24→34) の条件を満たすと 1 台ずつスケールインされる |

<a id="cluster-autoscaler"></a>
#### クラスターオートスケーラー
クラスターオートスケーラーは、Kubernetes プロジェクトの公式サポート機能である cluster-autoscaler 機能をベースに動作します。詳細については、[Cluster Autoscaler](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler) を参照してください。

> [注記]
> NHN Kubernetes Service(NKS) に適用された `cluster-autoscaler` のバージョンは `1.19.0` です。

<a id="cluster-autoscaler-set"></a>
##### クラスターオートスケーラーの設定
クラスターオートスケーラーを有効にすると、以下の項目を設定できます。

| 設定項目 | 意味 | 有効範囲 | デフォルト値 | 単位 |
| --- | --- | --- | --- | --- |
| 最小ノード数 | 縮小可能な最小ノード数 | 1〜10 | 1 | 台 |
| 最大ノード数 | 増設可能な最大ノード数 | 1〜10 | 10 | 台 |
| 縮小 | ノード縮小の有効/無効設定 | 有効/無効 | 有効 | - |
| リソース使用量しきい値 | 縮小の基準となるリソース使用量しきい値の基準値 | 1〜100 | 50 | % |
| しきい値維持時間 | 縮小対象となるノードのしきい値以下のリソース使用量を維持する時間 | 1〜1440 | 10 | 分 |
| 増設後の縮小遅延時間 | ノード増設後、縮小対象ノードとしてのモニタリングを開始するまでの遅延時間 | 10〜1440 | 10 | 分 |

<a id="cluster-autoscaler-resize"></a>
##### 増設および縮小の条件
以下の条件をすべて満たした場合、ノードを増設します。

* Podをスケジューリングできるノードが存在しない
* 現在のノード数 < 最大ノード数

以下の条件をすべて満たした場合、ノードを縮小します。

* ノードのリソース使用量がしきい値以下の状態を、しきい値維持時間にわたって維持している
* 現在のノード数 > 最小ノード数

特定のノードに以下の条件を満たす Pod が 1 つでも存在する場合、そのノードはノード縮小の候補から除外されます。

* "PodDisruptionBudget" による制約を受ける Pod
* "kube-system" ネームスペースの Pod
* "deployment"、"replicaset" などの制御オブジェクトによって起動されていない Pod
* ローカルストレージを使用する Pod
* "node selector" などの制約により、他のノードへの移動が不可能な Pod

より詳細な増設および縮小の条件については、[Cluster Autoscaler FAQ](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md) を参照してください。

<a id="cluster-autoscaler-example"></a>
##### 動作例
オートスケーラーの動作を例を通して説明します。

**1. オートスケーラーの有効化**

対象クラスターのデフォルトノードグループのオートスケーラー機能を有効にします。この例では、デフォルトノードグループのノード数を 1 で作成し、オートスケーラーの設定項目は以下のとおりに設定しました。

| 設定項目 | 設定値 |
| --- | --- |
| 最小ノード数 | 1 |
| 最大ノード数 | 5 |
| 縮小 | 有効 |
| リソース使用量しきい値 | 50 |
| しきい値維持時間 | 3 |
| 増設後の縮小遅延時間 | 10 |

**2. Podのデプロイ**

以下のマニフェストで Pod をデプロイします。

> [注意]
> このマニフェストのように、コンテナのリソースリクエストが明示されている必要があります。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 15
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: "100m"
```

デプロイをリクエストした Pod の CPU リソースの合計が、ノード 1 台のリソースを超えるため、以下のようにいくつかの Pod が `Pending` 状態のままになります。この状況でノードの増設が発生します。

```
# kubectl get pods
NAME                               READY   STATUS    RESTARTS   AGE
nginx-deployment-756fd4cdf-5gftm   1/1     Running   0          34s
nginx-deployment-756fd4cdf-64gtv   0/1     Pending   0          34s
nginx-deployment-756fd4cdf-7bsst   0/1     Pending   0          34s
nginx-deployment-756fd4cdf-8892p   1/1     Running   0          34s
nginx-deployment-756fd4cdf-8k4cc   1/1     Running   0          34s
nginx-deployment-756fd4cdf-cprp7   0/1     Pending   0          34s
nginx-deployment-756fd4cdf-cvs97   1/1     Running   0          34s
nginx-deployment-756fd4cdf-h7ftk   1/1     Running   0          34s
nginx-deployment-756fd4cdf-hv2fz   0/1     Pending   0          34s
nginx-deployment-756fd4cdf-j789l   0/1     Pending   0          34s
nginx-deployment-756fd4cdf-jrkfj   0/1     Pending   0          34s
nginx-deployment-756fd4cdf-m887q   0/1     Pending   0          34s
nginx-deployment-756fd4cdf-pvnfc   0/1     Pending   0          34s
nginx-deployment-756fd4cdf-wrj8b   1/1     Running   0          34s
nginx-deployment-756fd4cdf-x7ns5   0/1     Pending   0          34s
```

**3. ノード増設の確認**

以下は増設前のノード一覧です。

```
# kubectl get nodes
NAME                                            STATUS   ROLES    AGE   VERSION
autoscaler-test-default-w-ohw5ab5wpzug-node-0   Ready    <none>   45m   v1.28.3
```

約 5〜10 分後、以下のようにノードが増設されたことを確認できます。

```
# kubectl get nodes
NAME                                            STATUS   ROLES    AGE   VERSION
autoscaler-test-default-w-ohw5ab5wpzug-node-0   Ready    <none>   48m   v1.28.3
autoscaler-test-default-w-ohw5ab5wpzug-node-1   Ready    <none>   77s   v1.28.3
autoscaler-test-default-w-ohw5ab5wpzug-node-2   Ready    <none>   78s   v1.28.3
```

`Pending` 状態だった Pod が、ノード増設後に正常にスケジューリングされたことを確認できます。

```
# kubectl get pods -o wide
NAME                               READY   STATUS    RESTARTS   AGE     IP            NODE                                            NOMINATED NODE   READINESS GATES
nginx-deployment-756fd4cdf-5gftm   1/1     Running   0          4m29s   10.100.8.13   autoscaler-test-default-w-ohw5ab5wpzug-node-0   <none>           <none>
nginx-deployment-756fd4cdf-64gtv   1/1     Running   0          4m29s   10.100.22.5   autoscaler-test-default-w-ohw5ab5wpzug-node-1   <none>           <none>
nginx-deployment-756fd4cdf-7bsst   1/1     Running   0          4m29s   10.100.22.4   autoscaler-test-default-w-ohw5ab5wpzug-node-1   <none>           <none>
nginx-deployment-756fd4cdf-8892p   1/1     Running   0          4m29s   10.100.8.10   autoscaler-test-default-w-ohw5ab5wpzug-node-0   <none>           <none>
nginx-deployment-756fd4cdf-8k4cc   1/1     Running   0          4m29s   10.100.8.12   autoscaler-test-default-w-ohw5ab5wpzug-node-0   <none>           <none>
nginx-deployment-756fd4cdf-cprp7   1/1     Running   0          4m29s   10.100.12.7   autoscaler-test-default-w-ohw5ab5wpzug-node-2   <none>           <none>
nginx-deployment-756fd4cdf-cvs97   1/1     Running   0          4m29s   10.100.8.14   autoscaler-test-default-w-ohw5ab5wpzug-node-0   <none>           <none>
nginx-deployment-756fd4cdf-h7ftk   1/1     Running   0          4m29s   10.100.8.11   autoscaler-test-default-w-ohw5ab5wpzug-node-0   <none>           <none>
nginx-deployment-756fd4cdf-hv2fz   1/1     Running   0          4m29s   10.100.12.5   autoscaler-test-default-w-ohw5ab5wpzug-node-2   <none>           <none>
nginx-deployment-756fd4cdf-j789l   1/1     Running   0          4m29s   10.100.22.6   autoscaler-test-default-w-ohw5ab5wpzug-node-1   <none>           <none>
nginx-deployment-756fd4cdf-jrkfj   1/1     Running   0          4m29s   10.100.12.4   autoscaler-test-default-w-ohw5ab5wpzug-node-2   <none>           <none>
nginx-deployment-756fd4cdf-m887q   1/1     Running   0          4m29s   10.100.22.3   autoscaler-test-default-w-ohw5ab5wpzug-node-1   <none>           <none>
nginx-deployment-756fd4cdf-pvnfc   1/1     Running   0          4m29s   10.100.12.6   autoscaler-test-default-w-ohw5ab5wpzug-node-2   <none>           <none>
nginx-deployment-756fd4cdf-wrj8b   1/1     Running   0          4m29s   10.100.8.15   autoscaler-test-default-w-ohw5ab5wpzug-node-0   <none>           <none>
nginx-deployment-756fd4cdf-x7ns5   1/1     Running   0          4m29s   10.100.12.3   autoscaler-test-default-w-ohw5ab5wpzug-node-2   <none>           <none>
```

ノード増設に関するイベントは、以下のコマンドで確認できます。

```
# kubectl get events --field-selector reason="TriggeredScaleUp"
LAST SEEN   TYPE     REASON             OBJECT                                 MESSAGE
4m          Normal   TriggeredScaleUp   pod/nginx-deployment-756fd4cdf-64gtv   pod triggered scale-up: [{default-worker-bf5999ab 1->3 (max: 5)}]
4m          Normal   TriggeredScaleUp   pod/nginx-deployment-756fd4cdf-7bsst   pod triggered scale-up: [{default-worker-bf5999ab 1->3 (max: 5)}]
...
```

**4. Pod削除後のノード縮小の確認**

デプロイされているデプロイメント (deployment) を削除すると、デプロイされていた Pod が削除されます。

```
# kubectl get pods
NAME                               READY   STATUS        RESTARTS   AGE
nginx-deployment-756fd4cdf-5gftm   0/1     Terminating   0          20m
nginx-deployment-756fd4cdf-64gtv   0/1     Terminating   0          20m
nginx-deployment-756fd4cdf-7bsst   0/1     Terminating   0          20m
nginx-deployment-756fd4cdf-8892p   0/1     Terminating   0          20m
nginx-deployment-756fd4cdf-8k4cc   0/1     Terminating   0          20m
nginx-deployment-756fd4cdf-cprp7   0/1     Terminating   0          20m
nginx-deployment-756fd4cdf-h7ftk   0/1     Terminating   0          20m
nginx-deployment-756fd4cdf-hv2fz   0/1     Terminating   0          20m
nginx-deployment-756fd4cdf-j789l   0/1     Terminating   0          20m
nginx-deployment-756fd4cdf-jrkfj   0/1     Terminating   0          20m
nginx-deployment-756fd4cdf-m887q   0/1     Terminating   0          20m
nginx-deployment-756fd4cdf-pvnfc   0/1     Terminating   0          20m
nginx-deployment-756fd4cdf-wrj8b   0/1     Terminating   0          20m
nginx-deployment-756fd4cdf-x7ns5   0/1     Terminating   0          20m
#
# kubectl get pods
No resources found in default namespace.
#
```

しばらくするとノードの縮小が発生し、ノード数が 1 台に減少したことを確認できます。ノード縮小にかかる時間は、設定によって異なる場合があります。

```
# kubectl get nodes
NAME                                            STATUS   ROLES    AGE   VERSION
autoscaler-test-default-w-ohw5ab5wpzug-node-0   Ready    <none>   71m   v1.28.3
```

ノード縮小に関するイベントは、以下のコマンドで確認できます。

```
# kubectl get events --field-selector reason="ScaleDown"
LAST SEEN   TYPE     REASON      OBJECT                                               MESSAGE
13m         Normal   ScaleDown   node/autoscaler-test-default-w-ohw5ab5wpzug-node-1   node removed by cluster autoscaler
13m         Normal   ScaleDown   node/autoscaler-test-default-w-ohw5ab5wpzug-node-2   node removed by cluster autoscaler
```

ノードグループごとのオートスケーラーのステータス情報は、`configmap/cluster-autoscaler-status` で確認できます。この ConfigMap はノードグループごとに異なるネームスペースに作成されます。オートスケーラーが作成するノードグループごとのネームスペースの命名規則は次のとおりです。

* 形式: nhn-ng-{ノードグループ名}
* {ノードグループ名} にはノードグループの名前が入ります。
* デフォルトノードグループのノードグループ名は "default-worker" です。

デフォルトノードグループのオートスケーラーのステータス情報を確認する方法は次のとおりです。より詳細な情報については、[Cluster Autoscaler FAQ](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md) を参照してください。

```
# kubectl get configmap/cluster-autoscaler-status -n nhn-ng-default-worker -o yaml
apiVersion: v1
data:
  status: |+
    Cluster-autoscaler status at 2020-11-03 12:39:12.190150095 +0000 UTC:
    Cluster-wide:
      Health:      Healthy (ready=1 unready=0 notStarted=0 longNotStarted=0 registered=1 longUnregistered=0)
                   LastProbeTime:      2020-11-03 12:39:12.185954244 +0000 UTC m=+43.664545435
                   LastTransitionTime: 2020-11-03 12:38:41.705407217 +0000 UTC m=+13.183998415
      ScaleUp:     NoActivity (ready=1 registered=1)
                   LastProbeTime:      2020-11-03 12:39:12.185954244 +0000 UTC m=+43.664545435
                   LastTransitionTime: 2020-11-03 12:38:41.705407217 +0000 UTC m=+13.183998415
      ScaleDown:   NoCandidates (candidates=0)
                   LastProbeTime:      2020-11-03 12:39:12.185954244 +0000 UTC m=+43.664545435
                   LastTransitionTime: 2020-11-03 12:38:41.705407217 +0000 UTC m=+13.183998415

NodeGroups:
      Name:        default-worker-f9a9ee5e
      Health:      Healthy (ready=1 unready=0 notStarted=0 longNotStarted=0 registered=1 longUnregistered=0 cloudProviderTarget=1 (minSize=1, maxSize=5))
                   LastProbeTime:      2020-11-03 12:39:12.185954244 +0000 UTC m=+43.664545435
                   LastTransitionTime: 2020-11-03 12:38:41.705407217 +0000 UTC m=+13.183998415
      ScaleUp:     NoActivity (ready=1 cloudProviderTarget=1)
                   LastProbeTime:      2020-11-03 12:39:12.185954244 +0000 UTC m=+43.664545435
                   LastTransitionTime: 2020-11-03 12:38:41.705407217 +0000 UTC m=+13.183998415
      ScaleDown:   NoCandidates (candidates=0)
                   LastProbeTime:      2020-11-03 12:39:12.185954244 +0000 UTC m=+43.664545435
                   LastTransitionTime: 2020-11-03 12:38:41.705407217 +0000 UTC m=+13.183998415

kind: ConfigMap
metadata:
  annotations:
    cluster-autoscaler.kubernetes.io/last-updated: 2020-11-03 12:39:12.190150095 +0000
      UTC
  creationTimestamp: "2020-11-03T12:38:28Z"
  name: cluster-autoscaler-status
  namespace: nhn-ng-default-worker
  resourceVersion: "1610"
  selfLink: /api/v1/namespaces/nhn-ng-default-worker/configmaps/cluster-autoscaler-status
  uid: e72bd1a2-a56f-41b4-92ee-d11600386558
```

> [参考]
> 状態情報の内容のうち、`Cluster-wide` 領域の内容は `NodeGroups` 領域の内容と同じです。

<a id="cluster-autoscaler-with-hpa"></a>
##### HPA(HorizontalPodAutoscale) 機能と連動した動作例
HPA (Horizontal Pod Autoscaler) 機能は、CPU使用量などのリソース使用量を監視して、レプリケーションコントローラー (ReplicationController)、デプロイメント (Deployment)、レプリカセット (ReplicaSet)、ステートフルセット (StatefulSet) のPod数を自動的にスケールします。Pod数を調整する過程で、ノードに利用可能なリソースが不足したり、リソースが大量に余る状況が発生する場合があります。このとき、オートスケーラー機能と連動してノードの数を増減できます。この例では、HPA機能とオートスケーラー機能を連動して動作させる方法を示します。HPAの詳細については、[Horizontal Pod Autoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) のドキュメントを参照してください。

**1. オートスケーラーの有効化**
上記の例と同様に、オートスケーラーを有効にします。

**2. HPA の設定**
Webリクエストを受信すると、一定時間CPU負荷を生成するコンテナをデプロイします。そして、サービスを公開します。以下は `php-apache.yaml` ファイルの内容です。

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: php-apache
spec:
  selector:
    matchLabels:
      run: php-apache
  replicas: 1
  template:
    metadata:
      labels:
        run: php-apache
    spec:
      containers:
      - name: php-apache
        image: k8s.gcr.io/hpa-example
        ports:
        - containerPort: 80
        resources:
          limits:
            cpu: 500m
          requests:
            cpu: 200m
---
apiVersion: v1
kind: Service
metadata:
  name: php-apache
  labels:
    run: php-apache
spec:
  ports:
  - port: 80
  selector:
    run: php-apache
```

```
# kubectl apply -f php-apache.yaml
deployment.apps/php-apache created
service/php-apache created
```

次に、HPAを設定します。先ほど作成した php-apache deployment オブジェクトに対して、最小Pod数を1、最大Pod数を30、目標CPU loadを50%に設定します。

```
# kubectl autoscale deployment php-apache --cpu-percent=50 --min=1 --max=30
horizontalpodautoscaler.autoscaling/php-apache autoscaled
```

HPAの状態を照会すると、設定値と現在の状態を確認できます。まだCPU負荷を発生させるWebリクエストを送信していないため、CPU loadは0%です。

```
# kubectl get hpa
NAME         REFERENCE               TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   0%/50%    1         30        1          80s
```

**3. 負荷の付与**
次に、新しいターミナルで負荷を発生させるPodを実行します。このPodは無限にWebリクエストを送信し続けます。`Ctrl+C` で停止できます。

```
# kubectl run -i --tty load-generator --rm --image=busybox --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://php-apache; done"
If you don't see a command prompt, try pressing enter.
OK!OK!OK!OK!OK!OK!OK!
```

`kubectl top nodes` コマンドを使用して、ノードの現在のリソース使用量を確認できます。負荷を発生させるPodの実行後、時間の経過とともにCPU負荷が増加していることを確認できます。

```
# kubectl top nodes
NAME                                            CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
autoscaler-test-default-w-ohw5ab5wpzug-node-0   66m          6%     1010Mi          58%

(しばらく後)

# kubectl top nodes
NAME                                            CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
autoscaler-test-default-w-ohw5ab5wpzug-node-0   574m         57%    1013Mi          58%
```

HPAの状態を照会すると、CPU loadが増加し、それに対応するためにREPLICAS（= Pod数）が増加していることを確認できます。

```
# kubectl get hpa
NAME         REFERENCE               TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   250%/50%   1         30        5          2m44s
```

**4. オートスケーラーの動作確認**
Podを照会すると、Pod数が増加する中で、一部のPodは `node-0` にスケジューリングされてRunning状態になっていますが、一部はPending状態であることを確認できます。

```
# kubectl get pods -o wide
NAME                          READY   STATUS    RESTARTS   AGE     IP            NODE                                            NOMINATED NODE   READINESS GATES
load-generator                1/1     Running   0          2m      10.100.8.39   autoscaler-test-default-w-ohw5ab5wpzug-node-0   <none>           <none>
php-apache-79544c9bd9-6f7nm   0/1     Pending   0          65s     <none>        <none>                                          <none>           <none>
php-apache-79544c9bd9-82xkn   1/1     Running   0          80s     10.100.8.41   autoscaler-test-default-w-ohw5ab5wpzug-node-0   <none>           <none>
php-apache-79544c9bd9-cjj9q   0/1     Pending   0          80s     <none>        <none>                                          <none>           <none>
php-apache-79544c9bd9-k6nnt   1/1     Running   0          4m27s   10.100.8.38   autoscaler-test-default-w-ohw5ab5wpzug-node-0   <none>           <none>
php-apache-79544c9bd9-mplnn   0/1     Pending   0          19s     <none>        <none>                                          <none>           <none>
php-apache-79544c9bd9-t2knw   1/1     Running   0          80s     10.100.8.40   autoscaler-test-default-w-ohw5ab5wpzug-node-0   <none>           <none>
```

Podをスケジューリングできない状況が、オートスケーラーのノード増設条件です。Cluster Autoscaler Podが提供する状態情報を照会すると、ScaleUpがInProgress状態になっていることを確認できます。

```
# kubectl get cm/cluster-autoscaler-status -n nhn-ng-default-worker -o yaml
apiVersion: v1
data:
  status: |+
    Cluster-autoscaler status at 2020-11-24 13:00:40.210137143 +0000 UTC:
    Cluster-wide:
      Health:      Healthy (ready=1 unready=0 notStarted=0 longNotStarted=0 registered=1 longUnregistered=0)
                   LastProbeTime:      2020-11-24 13:00:39.930763305 +0000 UTC m=+1246178.729396969
                   LastTransitionTime: 2020-11-10 02:51:14.353177175 +0000 UTC m=+13.151810823
      ScaleUp:     InProgress (ready=1 registered=1)
                   LastProbeTime:      2020-11-24 13:00:39.930763305 +0000 UTC m=+1246178.729396969
                   LastTransitionTime: 2020-11-24 12:58:34.83642035 +0000 UTC m=+1246053.635054003
      ScaleDown:   NoCandidates (candidates=0)
                   LastProbeTime:      2020-11-24 13:00:39.930763305 +0000 UTC m=+1246178.729396969
                   LastTransitionTime: 2020-11-20 01:42:32.287146552 +0000 UTC m=+859891.085780205

NodeGroups:
      Name:        default-worker-bf5999ab
      Health:      Healthy (ready=1 unready=0 notStarted=0 longNotStarted=0 registered=1 longUnregistered=0 cloudProviderTarget=2 (minSize=1, maxSize=3))
                   LastProbeTime:      2020-11-24 13:00:39.930763305 +0000 UTC m=+1246178.729396969
                   LastTransitionTime: 2020-11-10 02:51:14.353177175 +0000 UTC m=+13.151810823
      ScaleUp:     InProgress (ready=1 cloudProviderTarget=2)
                   LastProbeTime:      2020-11-24 13:00:39.930763305 +0000 UTC m=+1246178.729396969
                   LastTransitionTime: 2020-11-24 12:58:34.83642035 +0000 UTC m=+1246053.635054003
      ScaleDown:   NoCandidates (candidates=0)
                   LastProbeTime:      2020-11-24 13:00:39.930763305 +0000 UTC m=+1246178.729396969
                   LastTransitionTime: 2020-11-20 01:42:32.287146552 +0000 UTC m=+859891.085780205
...
```

しばらくすると、ノード（node-8）が1つ増加していることを確認できます。

```
# kubectl get nodes
NAME                                            STATUS     ROLES    AGE   VERSION
autoscaler-test-default-w-ohw5ab5wpzug-node-0   Ready      <none>   22d   v1.28.3
autoscaler-test-default-w-ohw5ab5wpzug-node-8   Ready      <none>   90s   v1.28.3
```

Pending状態だったすべてのPodが正常にスケジューリングされ、Running状態になっていることを確認できます。

```
# kubectl get pods -o wide
NAME                          READY   STATUS    RESTARTS   AGE     IP            NODE                                            NOMINATED NODE   READINESS GATES
load-generator                1/1     Running   0          5m32s   10.100.8.39   autoscaler-test-default-w-ohw5ab5wpzug-node-0   <none>           <none>
php-apache-79544c9bd9-6f7nm   1/1     Running   0          4m37s   10.100.42.3   autoscaler-test-default-w-ohw5ab5wpzug-node-8   <none>           <none>
php-apache-79544c9bd9-82xkn   1/1     Running   0          4m52s   10.100.8.41   autoscaler-test-default-w-ohw5ab5wpzug-node-0   <none>           <none>
php-apache-79544c9bd9-cjj9q   1/1     Running   0          4m52s   10.100.42.5   autoscaler-test-default-w-ohw5ab5wpzug-node-8   <none>           <none>
php-apache-79544c9bd9-k6nnt   1/1     Running   0          7m59s   10.100.8.38   autoscaler-test-default-w-ohw5ab5wpzug-node-0   <none>           <none>
php-apache-79544c9bd9-mplnn   1/1     Running   0          3m51s   10.100.42.4   autoscaler-test-default-w-ohw5ab5wpzug-node-8   <none>           <none>
php-apache-79544c9bd9-t2knw   1/1     Running   0          4m52s   10.100.8.40   autoscaler-test-default-w-ohw5ab5wpzug-node-0   <none>           <none>
```

負荷のために実行していたPod（`load-generator`）を `Ctrl+C` で停止すると、しばらくすると負荷が軽減されます。負荷が減ると、Podが占有していたCPU使用量が減少し、Podの数が減少します。

```
# kubectl get hpa
NAME         REFERENCE               TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   0%/50%    1         30        1          31m
```

Podの数が減少してノードのリソース使用量が減ると、最終的にノードのスケールダウンが発生します。新たに追加されていたnode-8が削減されたことを確認できます。

```
# kubectl get nodes
NAME                                            STATUS   ROLES    AGE   VERSION
autoscaler-test-default-w-ohw5ab5wpzug-node-0   Ready    <none>   22d   v1.28.3
```

<a id="user-script-old"></a>
### ユーザースクリプト (old) { #user-script-old }
クラスターを作成するときおよび追加ノードグループを作成するときに、ユーザースクリプトを登録できます。ユーザースクリプト機能には次のような特徴があります。

* 機能設定
    * この機能はワーカーノードグループごとに設定できます。
    * クラスター作成時に入力したユーザースクリプトは、デフォルトのワーカーノードグループに適用されます。
    * 追加ノードグループ作成時に入力したユーザースクリプトは、該当するワーカーノードグループに適用されます。
    * **ワーカーノードグループが作成された後は、ユーザースクリプトの内容を変更できません。**
* スクリプトの実行タイミング
    * ユーザースクリプトは、ワーカーノードの初期化プロセス中にインスタンスの初期化処理で実行されます。
    * ユーザースクリプトが実行された後、該当するインスタンスを「ワーカーノードグループ」のワーカーノードとして設定し、登録します。
* スクリプトの内容
    * ユーザースクリプトの最初の行は必ず `#!` で始まる必要があります。
    * スクリプトの最大サイズは64KBです。
    * スクリプトはroot権限で実行されます。
    * スクリプトの実行ログは以下の場所に保存されます。
        * スクリプトの終了コード: `/var/log/userscript.exitcode`
        * スクリプトの標準出力および標準エラーストリーム: `/var/log/userscript.output`

<a id="user-script"></a>
### ユーザースクリプト { #user-script }
2022年7月26日以降に作成されるノードグループには、新しいバージョンのユーザースクリプト機能が搭載されます。以前のバージョンの機能と比較して、次のような特徴があります。

* **ワーカーノードグループが作成された後でも、ユーザースクリプトの内容を変更できます。**
    * ただし、変更した内容はユーザースクリプトの変更後に作成されるノードにのみ適用されます。
* スクリプトの実行ログは以下の場所に保存されます。
    * スクリプトの終了コード: `/var/log/userscript_v2.exitcode`
    * スクリプトの標準出力および標準エラーストリーム: `/var/log/userscript_v2.output`

* 以前のバージョンとの関係
    * 新バージョンの機能が以前のバージョンの機能を置き換えます。
        * コンソールまたはAPIを通じてノードグループを作成するときに設定したユーザースクリプトは、新バージョンの機能として設定されます。
    * 以前のバージョンのユーザースクリプトを設定したワーカーノードグループでは、以前のバージョンの機能と新バージョンの機能が個別に動作します。
        * 以前のバージョンに設定したユーザースクリプトの内容は変更できません。
        * 新バージョンに設定したユーザースクリプトの内容は変更できます。
    * 以前のバージョンと新バージョンそれぞれにユーザースクリプトを設定した場合、次の順序で実行されます。
        1. 以前のバージョンのユーザースクリプト
        2. 新バージョンのユーザースクリプト

<a id="instance-flavor-update"></a>
### インスタンスタイプの変更 { #instance-flavor-update }
ワーカーノードグループのインスタンスタイプを変更します。ワーカーノードグループに属するすべてのワーカーノードのインスタンスタイプが変更されます。


<a id="instance-flavor-update-process"></a>
#### 進行過程

インスタンスタイプの変更は次の順序で進行します。

1. クラスターオートスケーラー機能を無効にします。
2. 該当するワーカーノードグループにバッファーノードを追加します。
3. ワーカーノードグループ内のすべてのワーカーノードに対して、順次以下の作業を実行します。
    1. 該当するワーカーノードで動作中のPodを退避させ、ノードをスケジュール不可の状態に移行します。
    2. ワーカーノードのインスタンスタイプを変更します。
    3. ノードをスケジュール可能な状態に移行します。
4. バッファーノードで動作中のPodを退避させ、バッファーノードを削除します。
5. クラスターオートスケーラー機能を再度有効にします。

インスタンスタイプの変更は、ワーカーコンポーネントのアップグレードと同様の方法で進行します。バッファーノードの作成と削除、Podの退避については、[クラスターアップグレード](/Container/NKS/ja/user-guide/#cluster-upgrade)を参照してください。


<a id="instance-flavor-update-constraints"></a>
#### 制約事項

インスタンスの現在のタイプによって、変更できるタイプが異なります。

* m2、c2、r2、t2、x1タイプのインスタンスは、m2、c2、r2、t2、x1タイプに変更できます。
* m2、c2、r2、t2、x1、g2タイプのインスタンスは、u2タイプに変更できません。
* u2タイプのインスタンスは、作成後にタイプを変更することはできません。同じu2タイプへの変更も不可です。

<a id="custom-image"></a>
### カスタムイメージをワーカーイメージとして活用 { #custom-image }

ユーザーのカスタムイメージを基にしたワーカーノードグループを作成できます。カスタムイメージをワーカーノードイメージとして活用できるようにするために、NHN Cloud Image Builder サービスで追加作業 (NKS ワーカーノード化) が必要です。Image Builder サービスで NHN Kubernetes Service (NKS) ワーカーノードアプリケーションを使用してイメージテンプレートを作成し、カスタムワーカーノードイメージを生成できます。Image Builder サービスの詳細については、[Image Builder ユーザーガイド](/Compute/Image%20Builder/ja/console-guide/#_1)を参照してください。

> [注意]
> NKS ワーカーノード化の作業にはパッケージのインストールおよび設定の変更などが含まれており、正常に動作しないイメージで作業を進めた場合、失敗する可能性があります。
> Image Builder サービスの使用に対して課金される場合があります。

<a id="custom-image-constraints"></a>
#### 制約事項
サポートされている OS イメージおよび OS イメージごとに選択する必要があるアプリケーションバージョン情報は次の表のとおりです。カスタムイメージを作成する基となるインスタンスのイメージに合わせて、正しいバージョンのアプリケーションを選択する必要があります。

| OS | イメージ | アプリケーションバージョン |
| --- | --- | --- |
| Rocky | Rocky Linux 8.10 (2024.08.20)  | 1.6 |
|  | Rocky Linux 8.10 (2024.11.19)  | 1.7 |
|  | Rocky Linux 8.10 (2025.02.25)  | 1.8 |
|  | Rocky Linux 9.5 (2025.11.18)   | 1.9 |
|  | Rocky Linux 9.7 (2026.03.10)   | 1.9 |
| Ubuntu | Ubuntu Server 22.04.3 LTS (2023.11.21) | 1.3 |
|  | Ubuntu Server 22.04.3 LTS (2024.02.20)  | 1.4 |
|  | Ubuntu Server 22.04.5 LTS (2024.05.21)  | 1.5 |
|  | Ubuntu Server 22.04.5 LTS (2024.11.19)  | 1.7 |
|  | Ubuntu Server 22.04.5 LTS (2025.02.25)  | 1.8 |
|  | Ubuntu Server 22.04.5 LTS (2025.11.18)  | 1.9 |
|  | Ubuntu Server 24.04.3 LTS (2025.11.18)  | 1.9 |
|  | Ubuntu Server 22.04.3 LTS (2026.03.10)  | 1.10 |
|  | Ubuntu Server 24.04.3 LTS (2026.03.10)  | 1.10 |


> [注記]
> カスタムイメージをワーカーノードイメージに変換する過程で選択したオプションに応じて、GPU ドライバーがインストールされます。
> そのため、カスタム GPU ワーカーノードイメージを作成する場合でも、カスタムイメージの作成を GPU インスタンスで行う必要はありません。

<a id="custom-image-process"></a>
#### 進行手順

カスタムイメージをワーカーノードイメージとして活用するために、Image Builder サービスで次の手順を実施します。

1. **[イメージテンプレートの作成]** をクリックします。
2. アプリケーションを選択した後、**[イメージテンプレート名]**、**[OS]**、**[最小ブロックストレージ (GB)]**、**[ユーザースクリプト]**、**[説明]** を入力します。
    * GPU Flavor を使用しないワーカーノードグループの場合は、NHN Kubernetes Service (NKS) Worker Node アプリケーションを選択します。
    * GPU Flavor を使用するワーカーノードグループの場合は、NHN Kubernetes Service (NKS) Worker Node (GPU) アプリケーションを選択します。
3. **[確認]** をクリックしてイメージテンプレートを作成します。
4. 作成されたイメージテンプレートを選択した後、**[イメージビルド]** を選択します。
5. **[イメージビルド]** 画面で **[個人イメージ]** タブを選択し、NKS ワーカーノード化を進めるカスタムイメージを選択します。
6. **[確認]** をクリックすると、NKS ワーカーノード化が実施された後、新しいイメージが作成されます。
7. **[クラスター作成]** または **[ノードグループ作成]** 画面で作成されたカスタムイメージを選択します。

![nkscustom_image_1.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/nkscustom_image_1.png)

![nkscustom_image_2.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/nkscustom_image_2.png)

![nkscustom_image_3.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/nkscustom_image_3.png)

<a id="extra-volumes"></a>
### 追加ブロックストレージ { #extra-volumes }
ノードグループに追加ブロックストレージを使用できます。クラスターおよびノードグループの作成時に追加ブロックストレージを指定して作成するか、既存のノードグループに追加ブロックストレージを作成して使用できます。追加ブロックストレージの特徴は次のとおりです。

* 追加ブロックストレージはノードグループあたり最大 3 個まで設定でき、ブロックストレージのサイズは 1〜2048 GB の範囲で指定できます。
* ノードグループの追加ブロックストレージ設定は、ノードグループに属するすべてのワーカーノードに同一に適用されます。
    * 追加ブロックストレージの変更時、ノードグループのすべてのワーカーノードに変更内容が反映されます。
* 追加ブロックストレージの変更は、サイズ変更およびマウントパスの変更のみサポートします。
    * 作成済みの追加ブロックストレージの削除はできません。
    * 既存の設定値より小さいサイズに変更することはできません。
* 追加ブロックストレージの名前は `{クラスター名}-{ノードグループ名}-{ノード名}-extra-volume-{インデックス}` の形式で指定されます。
* マウントパスを入力した場合、追加ブロックストレージの作成後に指定されたパスへのマウントを試みます。
    * 未入力の場合はマウントを行いません。
    * 不正なマウントパスを入力してマウントに失敗すると、機能が動作しません。

[注意]
> 追加ブロックストレージの設定変更は既存ボリュームのアンマウントを含むため、使用中のサービスに影響を与える可能性があります。

<a id="extra-security-groups"></a>
### 追加セキュリティグループ { #extra-security-groups }
ノードグループに追加セキュリティグループを設定できます。クラスターおよびノードグループの作成時に追加セキュリティグループを指定して作成するか、既存のノードグループに追加セキュリティグループを設定できます。追加セキュリティグループの特徴は次のとおりです。

* 追加セキュリティグループはサブネットあたり最大 8 個まで設定できます。
* ノードグループの追加セキュリティグループ設定は、ノードグループに属するすべてのワーカーノードに同一に適用されます。
* 追加セキュリティグループを未入力の場合、クラスターのデフォルトセキュリティグループのみが適用されます。
* ユーザーが個別のノードに直接設定したセキュリティグループは、ノードグループの追加セキュリティグループ項目には表示されません。

[注記]
> コンソールでノードグループ作成時に指定した追加セキュリティグループは、デフォルトネットワークおよびすべての追加ネットワークに適用されます。個別ネットワークの追加セキュリティグループに対する変更は、ノードグループ作成後に行えます。

[注意]
> ノードグループに追加セキュリティグループを設定する際、既存のインスタンスに割り当てられているセキュリティグループのうち、追加セキュリティグループに定義されていない対象は削除されます。
> 追加セキュリティグループの変更時はネットワーク設定が変更されるため、設定が適用される間、一時的に通信に影響が生じる場合があります。

<a id="fip-auto-bind"></a>
### フローティング IP 自動割り当て { #fip-auto-bind }
ノードグループにフローティング IP 自動割り当て機能を使用できます。機能が有効化されたノードグループは、ノード作成時にフローティング IP を自動的に割り当てます。クラスターおよび追加ノードグループの作成時に機能の有効化/無効化を選択でき、設定したオプションは後から変更できます。フローティング IP 自動割り当て機能を有効化するために必要な項目は次のとおりです。

| 項目 | 説明 | 
| --- | --- | 
| 接続するサブネット | フローティング IP を接続するネットワークインターフェースのサブネットを指定します。該当のサブネットはクラスターのデフォルトサブネット、またはノードグループの追加サブネットに含まれている必要があります。 |
| フローティング IP ラベル | ノードに割り当てるフローティング IP を選別するための識別子です。入力しない場合、割り当て対象はすべてのフローティング IP になります。 |


フローティング IP 自動割り当て機能の特徴は次のとおりです。

* フローティング IP を作成しません。
  * ユーザーが事前に作成したフローティング IP を割り当てる方式で動作します。利用可能なフローティング IP が不足している場合、ノードの増設に失敗する可能性があります。
* フローティング IP 自動割り当て機能の有効化/無効化および設定変更は、既存のノードに影響を与えません。
  * 機能が有効化されていないノードグループで機能を有効化しても、既存のノードにフローティング IP は割り当てられません。
  * 機能が有効化されたノードグループで機能を無効化しても、既存のノードに割り当て済みのフローティング IP は解除されません。

<a id="cluster-management"></a>
## クラスター管理 { #cluster-management }
リモートホストからクラスターを操作・管理するには、Kubernetes が提供するコマンドラインツール (CLI) である `kubectl` が必要です。

<a id="kubectl-install"></a>
### kubectl のインストール { #kubectl-install }
kubectl は特別なインストール手順なしに、実行ファイルをダウンロードしてすぐに使用できます。OS 別のダウンロードコマンドは次のとおりです。

> [注意]
> ワーカーノードでパッケージマネージャーを使用して kubeadm、kubelet、kubectl などの Kubernetes 関連コンポーネントをインストールすると、クラスターの誤動作を引き起こす可能性があります。ワーカーノードに kubectl をインストールする場合は、以下のダウンロードコマンドを参照してファイルをダウンロードしてください。

| OS | ダウンロードコマンド |
| --- | --- |
| Linux | curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.15.7/bin/linux/amd64/kubectl |
| MacOS | curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.15.7/bin/darwin/amd64/kubectl |
| Windows | curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.15.7/bin/windows/amd64/kubectl.exe |

その他のインストール方法やオプションなどの詳細については、[Install and Set Up kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) ドキュメントを参照してください。

<a id="kubectl-install-change-permission"></a>
#### 権限の変更
ダウンロードしたファイルはデフォルトでは実行権限がありません。実行権限を追加する必要があります。

```
$ chmod +x kubectl
```

<a id="kubectl-install-change-the-location-or-set-the-path"></a>
#### 場所の変更またはパスの指定
どのパスからでも kubectl を実行できるように、環境変数に指定されたパスに移動するか、kubectl があるパスを環境変数に追加します。

* 環境変数に指定されたパスへ移動
```
$ sudo mv kubectl /usr/local/bin/
```

* 環境変数にパスを追加
```
// kubectlがあるパスで実行
$ export PATH=$PATH:$(pwd)
```

<a id="kubectl-set-kubeconfig"></a>
### 設定 { #kubectl-set-kubeconfig }
kubectl で Kubernetes クラスターにアクセスするには、クラスター設定ファイル (kubeconfig) が必要です。NHN Cloud Web コンソールで **Container > NHN Kubernetes Service(NKS)** ページを開き、アクセスするクラスターを選択します。下部の **[基本情報]** タブで **[設定ファイル]** 項目の **[ダウンロード]** ボタンをクリックして設定ファイルをダウンロードします。ダウンロードした設定ファイルは任意の場所に移動し、kubectl 実行時に参照できるよう準備します。

> [注意]
> NHN Cloud Web コンソールからダウンロードした設定ファイルには、クラスター情報および認証用のトークンなどが含まれています。設定ファイルがあれば、該当の Kubernetes クラスターにアクセスできる権限を持つことになります。設定ファイルを紛失しないよう十分に注意してください。

kubectl は実行のたびにクラスター設定ファイルが必要です。そのため、毎回 `--kubeconfig` オプションを使用してクラスター設定ファイルを指定する必要があります。ただし、環境変数にクラスター設定ファイルのパスが保存されている場合は、毎回オプションを指定しなくても構いません。

```
$ export KUBECONFIG={クラスター設定ファイルパス}
```

クラスター設定ファイルのパスを環境変数に保存したくない場合は、kubectl のデフォルト設定ファイルである `$HOME/.kube/config` にコピーして使用することもできます。ただし、複数のクラスターを運用する場合は、環境変数の値を変更する方法が便利です。

<a id="kubectl-check-connection"></a>
### 接続確認 { #kubectl-check-connection }
`kubectl version` コマンドで正常に設定されているか確認します。問題がなければ `Server Version` が出力されます。

```
$ kubectl version
Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.7", GitCommit:"6c143d35bb11d74970e7bc0b6c45b6bfdffc0bd4", GitTreeState:"clean", BuildDate:"2019-12-11T12:42:56Z", GoVersion:"go1.12.12", Compiler:"gc", Platform:"darwin/amd64"}
Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.7", GitCommit:"6c143d35bb11d74970e7bc0b6c45b6bfdffc0bd4", GitTreeState:"clean", BuildDate:"2019-12-11T12:34:17Z", GoVersion:"go1.12.12", Compiler:"gc", Platform:"linux/amd64"}
```

* Client Version: 実行した kubectl ファイルのバージョン情報
* Server Version: クラスターを構成している Kubernetes のバージョン情報

<a id="certificatesigningrequest"></a>
### CSR(CertificateSigningRequest) { #certificatesigningrequest }
KubernetesのCertificate APIを通じて、Kubernetes APIクライアント向けのX.509証明書を要求および発行できます。CSRリソースは、証明書の要求と、要求に対する承認・拒否を決定できるようにします。詳細については、[Certificate Signing Requests](https://kubernetes.io/docs/reference/access-authn-authz/certificate-signing-requests/) を参照してください。

<a id="certificatesigningrequest-csr-request-and-issue-approval-example"></a>
#### CSR要求と発行承認の例
まず、秘密鍵(private key)を生成します。証明書の生成に関する詳細は、[Certificates](https://kubernetes.io/docs/tasks/administer-cluster/certificates/) を参照してください。

```
$ openssl genrsa -out dev-user1.key 2048
Generating RSA private key, 2048 bit long modulus
...........................................................................+++++
..................+++++
e is 65537 (0x010001)

$ openssl req -new -key dev-user1.key -subj "/CN=dev-user1" -out dev-user1.csr
```

生成した秘密鍵の情報を含むCSRリソースを作成し、証明書の発行を要求します。

```
$ BASE64_CSR=$(cat dev-user1.csr | base64 | tr -d '\n')
$ cat <<EOF > csr.yaml -
apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  name: dev-user1
spec:
  groups:
  - system:authenticated
  request: ${BASE64_CSR}
  signerName: kubernetes.io/kube-apiserver-client
  expirationSeconds: 86400  # one day
  usages:
  - client auth
EOF

$ kubectl apply -f csr.yaml
certificatesigningrequest.certificates.k8s.io/dev-user1 created
```

登録されたCSRは `Pending` 状態です。この状態は、発行の承認または拒否を待っている状態です。

```
$ kubectl get csr
NAME        AGE   SIGNERNAME                            REQUESTOR   REQUESTEDDURATION   CONDITION
dev-user1   3s    kubernetes.io/kube-apiserver-client   admin       24h                 Pending
```

この証明書発行要求に対して承認処理を行います。

```
$ kubectl certificate approve dev-user1
certificatesigningrequest.certificates.k8s.io/dev-user1 approved
```

CSRを再確認すると、`Approved,Issued` 状態に変更されていることを確認できます。

```
$ kubectl get csr
NAME        AGE   SIGNERNAME                            REQUESTOR   REQUESTEDDURATION   CONDITION
dev-user1   28s   kubernetes.io/kube-apiserver-client   admin       24h                 Approved,Issued
```

証明書は次のように照会できます。証明書は status の certificate フィールドの値です。

```
$ apiVersion: certificates.k8s.io/v1
kind: CertificateSigningRequest
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"certificates.k8s.io/v1","kind":"CertificateSigningRequest","metadata":{"annotations":{},"name":"dev-user1"},"spec":{"expirationSeconds":86400,"groups":["system:authenticated"],"request":"LS0t..(以下省略)","signerName":"kubernetes.io/kube-apiserver-client","usages":["client auth"]}}
  creationTimestamp: "2023-09-15T05:53:12Z"
  name: dev-user1
  resourceVersion: "176619"
  uid: a5813153-40de-4725-9237-3bf684fd1db9
spec:
  expirationSeconds: 86400
  groups:
  - system:masters
  - system:authenticated
  request: LS0t..(以下省略)
  signerName: kubernetes.io/kube-apiserver-client
  usages:
  - client auth
  username: admin
status:
  certificate: LS0t..(以下省略)
  conditions:
  - lastTransitionTime: "2023-09-15T05:53:26Z"
    lastUpdateTime: "2023-09-15T05:53:26Z"
    message: This CSR was approved by kubectl certificate approve.
    reason: KubectlApprove
    status: "True"
    type: Approved
```

> [注意]
> この機能は、クラスターの作成時点が以下の期間に該当する場合にのみ提供されます。
> 
> * 板橋リージョン: 2020年12月29日以降に作成したクラスター
> * 平村リージョン: 2020年12月24日以降に作成したクラスター

<a id="admission-controller"></a>
### Admission Controller プラグイン { #admission-controller }
Admission Controllerは、Kubernetes APIサーバーへのリクエストをインターセプトし、オブジェクトを変更したりリクエストを拒否したりできます。Admission Controllerの詳細については、[承認コントローラー](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/) を参照してください。また、Admission Controllerの使用例については、[承認コントローラーガイド](https://kubernetes.io/blog/2019/03/21/a-guide-to-kubernetes-admission-controllers/) を参照してください。

クラスターのバージョンおよびクラスターの作成時点によって、Admission Controllerに適用されるプラグインの種類が異なります。詳細については、リージョンごとの作成時点に応じたプラグイン一覧を参照してください。

<a id="admission-controller-v11913-or-earlier"></a>
#### v1.19.13 以前のバージョン
板橋リージョンで2021年2月22日以前に作成したクラスターおよび平村リージョンで2021年2月17日以前に作成したクラスターには、次のプラグインが適用されます。

* DefaultStorageClass
* DefaultTolerationSeconds
* LimitRanger
* MutatingAdmissionWebhook
* NamespaceLifecycle
* NodeRestriction
* ResourceQuota
* ServiceAccount
* ValidatingAdmissionWebhook

板橋リージョンで2021年2月23日以降に作成したクラスターおよび平村リージョンで2021年2月18日以降に作成したクラスターには、次のプラグインが適用されます。

* DefaultStorageClass
* DefaultTolerationSeconds
* LimitRanger
* MutatingAdmissionWebhook
* NamespaceLifecycle
* NodeRestriction
* PodSecurityPolicy（新規追加）
* ResourceQuota
* ServiceAccount
* ValidatingAdmissionWebhook

<a id="admission-controller-v12012-or-later"></a>
#### v1.20.12 以降のバージョン
Kubernetesバージョンごとのデフォルトで有効なAdmission Controllerはすべて有効化されます。デフォルトで有効なAdmission Controllerに加え、以下のコントローラーが追加で有効化されます。

* NodeRestriction
* PodSecurityPolicy

<a id="cluster-upgrade"></a>
### クラスターアップグレード { #cluster-upgrade }
NHN Kubernetes Service(NKS)は、動作中の Kubernetes クラスターの Kubernetes コンポーネントのアップグレードをサポートします。

<a id="cluster-upgrade-policy-of-supporting-different-kubernetes-versions"></a>
#### Kubernetesバージョン差異サポートポリシー
Kubernetes バージョンは `x.y.z` で表されます。`x` はメジャーバージョン、`y` はマイナーバージョン、`z` はパッチバージョンです。機能が追加された場合はメジャーバージョンまたはマイナーバージョンを上げ、バグ修正など以前のバージョンと互換性のある機能を提供する場合はパッチバージョンを上げます。詳細については、[Semantic Versioning 2.0.0](https://semver.org/) を参照してください。

Kubernetes クラスターは動作中の状態で Kubernetes コンポーネントをアップグレードできます。そのために、Kubernetes コンポーネントごとに Kubernetes バージョン差異に基づく機能サポートの有無が定義されています。マイナーバージョンを基準とした 1 段階のバージョン差異は相互機能互換をサポートすることで、動作中のクラスターの Kubernetes コンポーネントアップグレードをサポートします。また、コンポーネントの種類ごとにアップグレードの順序が定義されています。詳細については、[Version Skew Policy](https://kubernetes.io/releases/version-skew-policy/) を参照してください。

<br>

<a id="cluster-upgrade-manage-nks-cluster-version"></a>
#### NKSクラスターのバージョン管理
NKS クラスターは、クラスターのコントロールプレーンとワーカーノードグループごとに Kubernetes バージョンとプラットフォームバージョンを管理します。Kubernetes バージョンとプラットフォームバージョンには次のような違いがあります。

##### Kubernetesバージョン
* アップストリーム Kubernetes で定義されるバージョンです。
* NKS クラスターを構成する Kubernetes 主要コンポーネントのバージョンを決定します。
* Kubernetes バージョンの影響を受ける主要コンポーネントは次のとおりです。
    * kube-apiserver
    * kube-controller-manager
    * kube-scheduler
    * kubelet
    * kube-proxy

##### プラットフォームバージョン
* NKS サービスレベルで定義されるバージョンです。
* NKS クラスターを構成するさまざまなコンポーネントを 1 つのバージョンとして定義し、管理します。
* プラットフォームバージョンの影響を受ける主要コンポーネントは次のとおりです。
    * containerd、etcd などのコントロールプレーンおよびワーカーノードの主要コンポーネント
    * 各種システムコンポーネントおよびシステム管理ツールなど

<br>

クラスターの Kubernetes バージョンとプラットフォームバージョンの状態に応じたアップグレード対象は次のとおりです。

| Kubernetesバージョン状態 | プラットフォームバージョン状態 | アップグレード対象 |
| --- | --- | --- |
| 最新でない | 最新でない | Kubernetesバージョンおよびプラットフォームバージョン |
| 最新でない | 最新 | Kubernetesバージョン |
| 最新 | 最新でない | プラットフォームバージョン |
| 最新 | 最新 | なし |

コントロールプレーンの Kubernetes バージョンとプラットフォームバージョンはクラスター照会画面で確認でき、ワーカーノードグループの Kubernetes バージョンとプラットフォームバージョンは各ワーカーノードグループの照会画面で確認できます。

<br>

<a id="cluster-upgrade-upgrade-rules"></a>
#### アップグレードルール
NKS クラスターのバージョン管理方式と Kubernetes バージョン差異サポートポリシーにより、コンポーネントごとに順序に従ってアップグレードする必要があります。NKS クラスターアップグレード機能に適用されるルールは次のとおりです。

* コントロールプレーンと各ワーカーノードグループごとにアップグレードコマンドを実行する必要があります。
* コントロールプレーンの Kubernetes バージョンとすべてのワーカーノードグループの Kubernetes バージョンが一致している必要があります。
* コントロールプレーンを先にアップグレードした後、ワーカーノードグループをアップグレードできます。
* Kubernetes バージョンは、現在の Kubernetes バージョンの次のバージョン（マイナーバージョン基準 +1）にアップグレードできます。
* プラットフォームバージョンは、NKS サービスが提供する最新バージョンにアップグレードできます。
* Kubernetes バージョンとプラットフォームバージョンはいずれもダウングレードをサポートしていません。
* 他の機能の動作によりクラスターが更新中の状態では、アップグレードできません。
* Kubernetes バージョンを v1.25.4 から v1.26.3 にアップグレードする際、CNI が Flannel の場合は Calico-VXLAN に変更する必要があります。
* NKS レジストリが有効化されていないクラスターはアップグレードできません。

次の例は、Kubernetes バージョンのアップグレード過程でアップグレード可否を表にまとめたものです。例に使用した条件は次のとおりです。

* NHN Cloud がサポートする Kubernetes バージョン一覧: v1.31.4、v1.32.3、v1.33.4
* クラスターは v1.31.4 で作成

| 状態 | コントロールプレーンバージョン | コントロールプレーンアップグレード可否 | ワーカーノードグループバージョン | ワーカーノードグループアップグレード可否 |
| --- | :-: | :-: | :-: | :-: |
| 初期状態 | v1.31.4 | 可能 <sup>[1](#footnote_cluster_upgrade_rule_1)</sup> | v1.31.4 | 不可能 <sup>[2](#footnote_cluster_upgrade_rule_2)</sup> |
| コントロールプレーンアップグレード後の状態 | v1.32.3 | 不可能 <sup>[3](#footnote_cluster_upgrade_rule_3)</sup> | v1.31.4 | 可能 <sup>[4](#footnote_cluster_upgrade_rule_4)</sup> |
| ワーカーノードグループアップグレード後の状態 | v1.32.3 | 可能 <sup>[1](#footnote_cluster_upgrade_rule_1)</sup> | v1.32.3 | 不可能 <sup>[2](#footnote_cluster_upgrade_rule_2)</sup> |
| コントロールプレーンアップグレード後の状態 | v1.33.4 | 不可能 <sup>[3](#footnote_cluster_upgrade_rule_3)</sup> | v1.32.3 | 可能 <sup>[4](#footnote_cluster_upgrade_rule_4)</sup> |
| ワーカーノードグループアップグレード後の状態 | v1.33.4 | 不可能 <sup>[5](#footnote_cluster_upgrade_rule_5)</sup> | v1.33.4 | 不可能 <sup>[2](#footnote_cluster_upgrade_rule_2)</sup> |

注釈

* <a name="footnote_cluster_upgrade_rule_1">1</a>: コントロールプレーンとすべてのワーカーノードグループのバージョンが一致しているためアップグレード可能
* <a name="footnote_cluster_upgrade_rule_2">2</a>: ワーカーノードグループはコントロールプレーンがアップグレードされた後にアップグレード可能
* <a name="footnote_cluster_upgrade_rule_3">3</a>: コントロールプレーンとすべてのワーカーノードグループのバージョンが一致している必要があるためアップグレード不可能
* <a name="footnote_cluster_upgrade_rule_4">4</a>: コントロールプレーンがアップグレードされたためアップグレード可能
* <a name="footnote_cluster_upgrade_rule_5">5</a>: NHN Cloud でサポートされている最新バージョンを使用しているためアップグレード不可能

<br>

<a id="cluster-upgrade-considerations-for-etcd-version-changes"></a>
#### etcdバージョン変更に伴う注意事項
クラスターアップグレード作業の実施時、アップグレード対象のプラットフォームバージョンに定義された [etcd バージョン](/Container/NKS/ja/user-guide/#platform-version-etcd-version) が現在のクラスターの etcd バージョンと異なる場合に限り、etcd のアップグレード作業が同時に実施されます。該当作業を開始する前に注意事項を必ず把握し、事前告知・点検時間の確保などの対策を講じることをお勧めします。

##### データ整合性確認のため頻繁なリソース変更を控える
etcd アップグレード時にリソースのデプロイ・削除作業が頻繁に発生すると、データ整合性の確認に失敗してアップグレードが失敗する可能性があります。安全なアップグレードのために、次のような環境でアップグレードすることをお勧めします。
* クラスターリソースの変更作業が少ない時間帯に実施する
* 運用への影響が少ない時間帯（点検時間など）にアップグレードを実施する
* アップグレード直前に大規模なデプロイ・削除、バッチ作業の実行などを避け、トラフィックが安定した後に実施する

##### etcdアップグレード失敗時の自動復旧中のクラスター運用一時停止
etcd のアップグレードが失敗すると、クラスターを以前の状態に戻す自動復旧手順が動作し、この手順が進行している間、クラスターの運用（Kubernetes API の応答）が一時的に停止する場合があります。すでに実行中のワークロード（Pod）には影響はありませんが、kubectl の呼び出しが一時的に遅延したり、新規リソースの作成・変更作業が一時的に停止する場合があります。

<br>

<a id="cluster-upgrade-upgrade-strategy"></a>
#### アップグレード戦略
NKS クラスターは、Rolling Upgrade と Blue/Green Upgrade の 2 種類のアップグレード戦略を提供します。ユーザーは運用ポリシーに応じて適切な戦略を選択してクラスターをアップグレードできます。

<br>

**Rolling Upgrade**

![Rolling_Upgrade.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/Rolling_Upgrade.png)

Rolling Upgrade は、コントロールプレーンとワーカーノードグループを順次アップグレードしてクラスター全体を新しいバージョンに移行するアップグレード戦略です。以下は、Rolling Upgrade 戦略によるクラスターアップグレードの実施過程と各過程の説明です。

<br>

##### 1. クラスター照会画面のアップグレードボタンからコントロールプレーンコンポーネントをアップグレードします。

NKS クラスターのコントロールプレーンは高可用性を保証します。コントロールプレーンはローリングアップデート方式でアップグレードされるため、クラスターの可用性が保証されます。この過程で Kubernetes API が一時的に失敗する場合があります。

##### 2. ノードグループ照会画面のアップグレードボタンからすべてのワーカーノードグループのワーカーコンポーネントをアップグレードします。

ワーカーノードグループごとにワーカーコンポーネントをアップグレードできます。ワーカーコンポーネントのアップグレードは次の順序で進みます。

1. クラスターオートスケーラー機能を無効化します。<sup>[1](#footnote_worker_component_upgrade_1)</sup>
2. 該当ワーカーノードグループにバッファーノード<sup>[2](#footnote_worker_component_upgrade_2)</sup>を追加します。<sup>[3](#footnote_worker_component_upgrade_3)</sup>
3. ワーカーノードグループ内のすべてのワーカーノードに対して順次、以下の作業を実施します。<sup>[4](#footnote_worker_component_upgrade_4)</sup>
    1. 該当ワーカーノードで動作中の Pod を退避し、ノードをスケジュール不可能な状態に移行します。
    2. ワーカーコンポーネントをアップグレードします。
    3. ノードをスケジュール可能な状態に移行します。
4. バッファーノードで動作中の Pod を退避し、バッファーノードを削除します。
5. クラスターオートスケーラー機能を再度有効化します。<sup>[1](#footnote_worker_component_upgrade_1)</sup>

注釈

* <a name="footnote_worker_component_upgrade_1">1</a>: このステップは、アップグレード機能開始前にクラスターオートスケーラー機能が有効化されている場合にのみ有効です。
* <a name="footnote_worker_component_upgrade_2">2</a>: バッファーノードとは、アップグレード過程中に既存のワーカーノードから退避された Pod が再度スケジューリングできるよう作成する余剰ノードのことです。該当ワーカーノードグループで定義されたワーカーノードと同じスペックのノードとして作成され、アップグレード過程が終了すると自動的に削除されます。このノードは Instance 料金ポリシーに従って課金されます。
* <a name="footnote_worker_component_upgrade_3">3</a>: アップグレード時にバッファーノード数を設定できます。デフォルト値は 1 で、0 に設定するとバッファーノードを追加しません。最小値は 0、最大値は（ノードグループあたりの最大ノード数クォータ − 該当ワーカーノードグループの現在のノード数）です。
* <a name="footnote_worker_component_upgrade_4">4</a>: アップグレード時に設定した最大サービス不可ノード数ずつ作業を実施します。デフォルト値は 1 です。最小値は 1、最大値は該当ワーカーノードグループの現在のノード数です。

この過程で、以下のようなことが発生する場合があります。

* サービス中の Pod が退避され、他のノードにスケジューリングされます（Pod 退避の詳細については、以下の Pod 退避に関する注意事項を参照してください）。
* オートスケーラー機能が動作しません。


> [Pod退避に関する注意事項]
> 1. デーモンセット（daemonset）コントローラーによる Pod は退避されません。
> デーモンセットコントローラーは各ワーカーノードごとに Pod を実行するため、デーモンセットコントローラーによって実行された Pod は退避されても他のノードで実行することができません。ワーカーノードグループのアップグレード過程で、デーモンセットコントローラーによって実行された Pod は退避されません。
> 2. ローカルストレージを使用する Pod は退避時に使用していたデータを失います。
> `emptyDir` を使用してノードのローカルストレージを使用する Pod は、退避時に使用していたデータを失います。ノードのローカルに保存されたストレージは他のノードに移行できないためです。
> 3. 他のノードに複製できない Pod は他のノードに移行されません。
> ReplicationController、ReplicaSet、Job、Daemonset、StatefulSet などのコントローラーによって実行された Pod が退避されると、コントローラーによって他のノードにスケジューリングされます。ただし、このようなコントローラーを使用していない Pod は退避後に他のノードにスケジューリングされません。
> 4. PodDisruptionBudgets（PDB）の設定により退避が失敗したり遅延したりする場合があります。
> PodDisruptionBudgets（PDB）の設定で維持すべき Pod 数を定義できます。この機能の設定により、アップグレード過程で Pod の退避が不可能になる場合や、Pod の退避時間が長くなる場合があります。Pod の退避に失敗するとアップグレードが失敗します。そのため、PDB が設定されている場合は、適切な PDB 設定で Pod の退避が円滑に動作するよう設定する必要があります。PDB 設定の詳細については、[こちら](https://kubernetes.io/docs/tasks/run-application/configure-pdb/) を参照してください。


安全な Pod 退避の詳細については、[Safely Drain a Node](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/) を参照してください。


<br>

**Blue/Green Upgrade**

![Blue_Green.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/Blue_Green.png)

Blue/Green Upgrade は、アップグレード過程においてクラスター内に 2 つの異なるバージョンの環境を構成することで、アプリケーションの可用性を高め、デプロイ失敗時のロールバックプロセスを簡素化してアップグレードリスクを低減できるアップグレード戦略です。一方の環境（Blue）はアップグレード前のバージョンのノードグループで構成され、もう一方の環境（Green）はアップグレード後のバージョンのノードグループで構成されます。Green 環境でのテストが完了したら、アプリケーションのトラフィックを Green 環境に移行し、Blue 環境を廃棄します。この過程を通じて、クラスター全体を次のバージョンにアップグレードできます。以下は、Blue/Green Upgrade 戦略によるクラスターアップグレードの実施過程と各過程の説明です。

<br>

##### 1. クラスター照会画面のアップグレードボタンからコントロールプレーンコンポーネントをアップグレードします。
NKS クラスターのコントロールプレーンは高可用性を保証します。コントロールプレーンはローリングアップデート方式でアップグレードされるため、クラスターの可用性が保証されます。この過程で Kubernetes API が一時的に失敗する場合があります。

##### 2. ノードグループを作成します。
新規ノードグループを作成して、テスト用の Green 環境を作成します。コントロールプレーンコンポーネントのアップグレード後に作成される新規ノードグループは、コントロールプレーンの Kubernetes バージョンと同じバージョンで作成されます。Green 環境に Blue 環境（既存のノードグループ）と同じリソースをデプロイして、アップグレード後の環境を検証できます。この際、Blue 環境が既存クラスターの運用に影響を与えないよう、アプリケーションのトラフィックを分離する必要があります。

##### 3. Green環境（新規ノードグループ）の検証後、アプリケーションのトラフィックをGreen環境に切り替えます。
新しく構築した Green 環境で、既存のユーザーが運用していたリソースが次のバージョンの Kubernetes と正常に互換性があるかを検証し、検証が完了したらアプリケーションのトラフィックを既存の Blue 環境から新しく構築した Green 環境に切り替えます。Green 環境での検証段階で問題が発生した場合は、トラフィックを切り替えずに Blue 環境を削除することで簡単にロールバックできます。

##### 4. Blue環境（旧バージョンのすべてのワーカーノードグループ）を廃棄します。
Blue 環境のリソースをすべて廃棄すると、コントロールプレーンとすべてのワーカーノードグループのバージョンがすべて一致した状態になります。

<a id="api-endpoint-ipacl"></a>
### クラスター API エンドポイント IP アクセス制御の適用 { #api-endpoint-ipacl }
クラスター API エンドポイントに IP アクセス制御を適用または解除できます。
IP アクセス制御機能の詳細については、[IP アクセス制御](/Network/Load%20Balancer/ja/overview/#ip) のドキュメントを参照してください。

<a id="api-endpoint-ipacl-ip-access-control-rules"></a>
#### IP アクセス制御対象ルール
クラスター API エンドポイントの IP アクセス制御対象を追加する場合、以下のルールが適用されます。

* IP アクセス制御タイプが **[許可]** に設定されている場合、クラスターのデフォルトサブネット CIDR がアクセス制御対象に自動的に追加されます。
* IP アクセス制御タイプが **[許可]** に設定されている場合、NKS コンソールのダッシュボード、ネームスペース、ワークロード、サービス＆ネットワーク、ストレージ、設定、イベントの各タブが無効になります。
* IP アクセス制御タイプが **[ブロック]** に設定されている場合、クラスターのデフォルトサブネット CIDR 帯域と重複する IP 帯域がアクセス制御対象リストに含まれていると、リクエストが拒否されます。
* 設定可能な IP アクセス制御対象の最大数は 100 件です。
* IP アクセス制御対象は 1 件以上存在する必要があります。

<a id="rotate-certificate"></a>
### クラスター証明書の更新 { #rotate-certificate }
Kubernetes は、コンポーネント間の TLS 認証に PKI 証明書が必要です。PKI 証明書の詳細については、[PKI 証明書と要件](https://kubernetes.io/ko/docs/setup/best-practices/certificates/) を参照してください。NKS クラスターを作成すると、クラスターに必要な証明書が自動的に生成され、この証明書のデフォルトの有効期間は 5 年に設定されています。

証明書の有効期間が満了すると、API サーバー、コントローラーマネージャー、etcd などのクラスターの主要コンポーネントが動作しなくなり、クラスターを使用できなくなります。
証明書が満了する前に、証明書更新機能を使用して有効期間を更新できます。クラスターの証明書有効期間および証明書更新ボタンは、クラスター照会画面 > **[基本情報]** > **[Kubernetes 証明書]** で確認できます。

証明書更新機能の使用方法は以下のとおりです。
1. **[証明書の更新]** をクリックします。
2. 更新期間を選択します。
    * 証明書の有効期間は最大 5 年まで設定できます。
    * 更新は 1 年単位でのみ可能です。
3. 証明書の更新を進めるには、**[確認]** をクリックします。
4. 対象クラスターのステータスを確認します。
    * 証明書更新作業が進行中のクラスターのステータスは `UPDATE_IN_PROGRESS` であり、作業が正常に完了した場合は `UPDATE_COMPLETE` ステータスに移行します。
    * 作業に問題が発生した場合は `UPDATE_FAILED` ステータスに移行し、正常化されるまでクラスターの構成変更作業（ノードの追加など）は許可されません。
        * クラスターステータスを正常化するために、証明書の更新をもう一度実行します。
5. クラスター照会画面で証明書の有効期限が正常に更新されたことを確認します。
6. kubeconfigファイルを新たにダウンロードします。
    * クラスターにアクセスするための kubeconfigファイルには証明書が含まれています。
    * 証明書が更新されると、以前使用していた kubeconfig ではクラスターにアクセスできなくなります。
7. CA 証明書を使用している Pod を再起動します。
    * 証明書更新のプロセスには、ユーザーが作成した Pod を再起動する機能は含まれていません。
    * 証明書設定が含まれた Pod が存在する場合、更新された CA 証明書を適用するために再起動が必要です。

> [注記]
> 証明書更新機能は、バージョン 1.24 以降の Calico-VXLAN CNI または Cilium CNI を使用するクラスターで使用できます。


> [注意]
> 証明書更新機能には、新しい証明書の生成および設定の反映のため、システムコンポーネントおよびクラスター作成時に初期デプロイされたすべての kube-system ネームスペースの Pod の再起動が伴います。
> そのため、証明書更新の進行中は、一時的にクラスターのノードステータスが Not Ready に変更されたり、クラスターの一部のコンポーネントが正常に動作しなくなる場合があります。
> このような作業の影響を最小化するために、証明書更新作業が進行中は新規 Pod の作成などの作業を実施しないでください。

<a id="k8s-component"></a>
### Kubernetes コンポーネント設定機能 { #k8s-component }

Kubernetes コンポーネントのさまざまなオプションを設定できます。クラスター作成時に設定でき、設定したオプションはクラスター作成完了後に変更することもできます。

動作領域ごとに設定をサポートするコンポーネントとオプションは以下のとおりです。各項目の詳細については、[Kubernetes 公式ドキュメント](https://kubernetes.io/docs/) を参照してください。

> [注意]
> * コントロールプレーンで動作するコンポーネントの設定を変更した場合、コントロールプレーンのコンポーネントが再起動されます。
> * ワーカーノードで動作するコンポーネントの設定を変更した場合、ワーカーノードのコンポーネントが再起動されます。
> * ワーカーノードで動作するコンポーネントの設定は、ワーカーノードグループごとに設定できます。（プラットフォームバージョン 1.202602.0 以降に限ります）

<a id="control-plain-options"></a>
### コントロールプレーンオプション { #control-plain-options }

| コンポーネント | オプション | 説明 |
| --- | --- | --- |
| kube-apiserver | default-not-ready-toleration-seconds | ノードが NotReady 状態のとき、そのノードで実行中の Pod がどのくらいの時間許容されるかを定義します。<br>（単位: 秒、デフォルト値: 300、最小値: 0、最大値: 86400） |
| kube-apiserver | default-unreachable-toleration-seconds | ノードがネットワークに接続されていないとき、そのノードで実行中の Pod がどのくらいの時間許容されるかを定義します。<br>（単位: 秒、デフォルト値: 300、最小値: 0、最大値: 86400） |
| kube-controller-manager | node-monitor-grace-period | ノードが異常状態のとき、そのノードを異常と見なすまでの待機時間を定義します。<br>（単位: 秒、デフォルト値: 40、最小値: 0、最大値: 86400） |
| kube-controller-manager | unhealthy-zone-threshold | Availability Zone を異常と見なす NotReady ノード比率のしきい値を定義します。<br>（単位: パーセント、デフォルト値: 55、最小値: 0、最大値: 100） |

<a id="worker-node-options"></a>
### ワーカーノードオプション { #worker-node-options }

| コンポーネント | オプション | 説明 |
| --- | --- | --- |
| kubelet | node-status-update-frequency | kubelet のノードステータス報告周期を定義します。<br>（単位: 秒、デフォルト値: 10、最小値: 0、最大値: 86400） |
| kubelet | max-pods | ノードで実行可能な最大 Pod 数を定義します。<br>（デフォルト値: 110、最小値: 1、最大値: Podネットワークおよびサブネットサイズ設定に基づいて計算された最大作成可能 Pod IP 数）<br>プラットフォームバージョン 1.202602.0 以降でサポートされます。 |

<a id="k8s-label"></a>
### Kubernetes ラベル設定機能 { #k8s-label }
ノードグループごとに Kubernetes ラベル設定機能を使用できます。この機能を通じてラベルが設定されたノードグループは、ノード作成時にユーザーが設定したラベルを自動的に追加します。ラベルは Pod やノードなどのオブジェクトに付与されたキーと値のペアで、オブジェクトの特性を識別するために使用されます。ラベルの詳細については、[Labels and Selectors](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/) を参照してください。

Kubernetes ラベルはキーと値のペアで構成されており、有効なラベルキーと値はそれぞれ以下のルールに従う必要があります。

<a id="k8s-label-label-key"></a>
#### ラベルキー
ラベルキーはスラッシュ（/）で区切られた接頭辞と名前の構造を持つことができ、接頭辞は省略可能です。

* 接頭辞
    * 253 文字以下である必要があります。
    * DNS のサブドメイン形式である必要があります。
    * 事前定義された接頭辞は使用できません。
        * ["kubernetes.io", "k8s.io", "magnum.openstack.org"]
* 名前
    * 63 文字以下である必要があります。
    * アルファベット大文字・小文字、数字、ダッシュ（-）、アンダースコア（_）、ドット（.）のみ使用可能で、英数字で始まり英数字で終わる必要があります。


<a id="k8s-label-label-value"></a>
#### ラベル値
* 空白または 63 文字以下である必要があります。
* アルファベット大文字・小文字、数字、ダッシュ（-）、アンダースコア（_）、ドット（.）のみ使用可能で、英数字で始まり英数字で終わる必要があります。

> [注記]
> * Kubernetes ラベルは最大 20 個まで指定できます。
> * Kubernetes ラベルの設定を変更すると、以降に新規作成されるノードから変更後の設定が適用されます。

<a id="oidc-auth"></a>
### OIDC 認証設定機能 { #oidc-auth }

OIDC（OpenID Connect）は、OAuth 2.0 フレームワークをベースにした相互運用可能な認証プロトコルです。OIDC を使用することで、外部認証サービスを通じてユーザーを認証できます。OIDC の詳細な動作については、[What is OpenID Connect](https://openid.net/developers/how-connect-works/) を参照してください。

NKS クラスターは OIDC を使用した認証を処理するように設定できます。OIDC 認証に関する設定項目は以下のとおりです。

| 項目 | 必須 | 説明 |
| --- | --- | --- |
| Issuer URL | O | `https://` で始まる OIDC プロバイダー URL |
| Client ID | O | OIDC プロバイダーのクライアント ID |
| Username claim | X | username として使用する claim。デフォルト値: `sub`<br>email 以外の claim にはプロバイダー URL が接頭辞として付加されます。 |
| Groups claim | X | groups として使用する claim |
| Username prefix | X | 衝突を防ぐために username claim に付ける接頭辞（prefix）。<br>設定しない場合、email 以外の username claim にはプロバイダー URL が接頭辞として付加されます。<br>接頭辞を使用しない場合は `-` を入力します。 |
| Groups prefix | X | 衝突を防ぐために groups claim に付ける接頭辞（prefix） |
| Required claim | X | ID トークンで確認が必要なキー/値のペア |
| CA File | X | OIDC プロバイダーの Web 証明書に署名した CA の証明書ファイル |
| Signing Algs | X | 許可された JOSE 非対称署名アルゴリズムのリスト。デフォルト値: `RS256` |

<a id="control-plane-k8s-log"></a>
### コントロールプレーン Kubernetes コンポーネントログの保存 { #control-plane-k8s-log }
NHN Kubernetes Service(NKS) は、コントロールプレーンで実行中の主要な Kubernetes コンポーネントのログを提供します。これにより、クラスター内で発生するさまざまなイベントや動作をより明確に把握でき、サービス状態の診断および問題解決に役立てることができます。

コントロールプレーン Kubernetes コンポーネントログの保存機能の特徴は次のとおりです。

* Log & Crash Search、Object Storage の 2 つのサービスのいずれかにログを転送できます。
* 転送されるログレベルは `INFO` に固定されます。
* ログを提供する Kubernetes コンポーネントは以下のとおりです。
    * kube-apiserver
    * kube-scheduler
    * kube-controller-manager


> [注記]
> ログの転送先は 1 つのみ設定できます。Log & Crash Search と Object Storage の両方でログを管理するには、まず転送先を Log & Crash Search に設定した後、「ログ外部保管」機能を使用してそのログを Object Storage に追加保存できます。
> 別プロジェクトの Log & Crash Search または Object Storage にも転送できます。

<a id="control-plane-k8s-log-lncs"></a>
#### Log & Crash Search への転送

<a id="control-plane-k8s-log-lncs-forward"></a>
##### ログ転送周期
ログの転送は、ログの生成時点からユーザーが指定した転送周期の経過後に行われます。転送周期は 1 分〜60 分の範囲で設定できます。

> [注記]
> 転送周期前にログ容量が 300 KB を超えた場合は、即座に Log & Crash Search に転送されます。

<a id="control-plane-k8s-log-lncs-labels"></a>
##### Log & Crash Search ラベル情報
Log & Crash Search へのログ転送時に設定されるラベル情報は次のとおりです。

| ラベル | 説明 |
| --- | --- |
| logType | "log" 固定値 |
| logSource | "NKS" 固定値 |
| logLevel | "INFO" 固定値 |
| logVersion | "v2" 固定値 |
| projectVersion | "1.0.0" 固定値 |
| host | マスターノード名 |
| cluster_uuid | クラスター UUID |
| cluster_name | クラスター名 |
| nks_version | クラスターバージョン |
| component | コンポーネント名 |

> [注記]
> Log & Crash Search コンソールでログを照会する際、cluster_uuid、cluster_name、nks_version、component の 4 つのラベルはデフォルトフィールドに含まれていません。
> 選択フィールド項目からラベルを追加することで直接登録して確認できます。

<a id="control-plane-k8s-log-obs"></a>
#### Object Storage への転送

<a id="control-plane-k8s-log-obs-forward"></a>
##### ログ転送周期
ユーザーが指定した転送周期ごとにログを収集して転送します。転送周期は 1 分〜60 分の範囲で設定できます。

> [注記]
> Object Storage に保存されるファイルの容量が 300 KB を超えると、分割して保存されます。
> ログファイルは 300 KB を超えた時点で即座に転送されます。
> 400 KB 以下: _index0 の接尾辞が付いた単一ファイルとして保存
> 400 KB 超過: _index1、_index2 などの接尾辞が付いた複数のファイルに分割して保存

<a id="control-plane-k8s-log-obs-compression"></a>
##### ファイル圧縮
ストレージに保管する際に gzip 形式で圧縮して保存するかどうかを選択できます。

<a id="control-plane-k8s-log-obs-authorization"></a>
##### ストレージへのアクセス権限の付与
コンソールの NKS ページで **[NKS システムアカウント情報]** をクリックすると、NKS が使用するテナント ID とユーザー ID が表示されます。コントロールプレーンログのストレージタイプを OBS (Object Storage) に設定した場合、この NKS システムアカウントに対象コンテナへの書き込み権限を必ず付与する必要があります。そうしない場合、NKS システムアカウントはユーザーの OBS にデータを書き込むことができません。

設定方法

* NHN Cloud > Object Storage コンソールにアクセスします。
* コントロールプレーンログを保存するコンテナを選択します。
* 下部の基本情報 > アクセスポリシー設定変更をクリックします。
* ロールベースのアクセスポリシーで使用をクリックします。
* 上記で確認した NKS システムアカウント情報のテナント ID とユーザー ID を入力し、Write 権限を付与します。

> [注意]
> コントロールプレーンログの転送中に Object Storage のコンテナが削除された場合、またはコンテナから Write 権限が削除された場合、ログの転送に失敗します。

<a id="control-plane-k8s-log-path"></a>
##### コントロールプレーンログの保存パス
コントロールプレーンログの保存パスは、OBS endpoint、AUTH tenant、Container、Path の情報をもとに以下の形式で構成されます。

* {OBS_https_endpoint}/{AUTH_OBS_TENANT}/{Container}/{Path}

例えば、設定値が以下のような場合

* OBS https endpoint: https://kr1-api-object-storage.nhncloudservice.com/v1
* AUTH_OBS_TENANT: AUTH_e670167936434f85a03694184000ffe6
* Container: nks_log_container
* 希望する保存パス: example/my/folder

実際のコントロールプレーンログの保存パスは次のとおりです。

* https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_e670167936434f85a03694184000ffe6/nks_log_container/example/my/folder

> [注記]
> obs_api_url に設定された OBS endpoint、AUTH_tenant、Container の情報が存在しない場合、設定リクエストは失敗します。

実際のログは上記 URL の配下に次の構造で保存されます。

* ${ユーザー設定 OBS コンテナ名}/NKS/${クラスター UUID}/${マスターノード名}/${K8S コンポーネント名}/${年}/${月}/${年月日-時分秒}-index${index_count}.gz

例えば、設定値が以下のような場合

* Container: nks_log_container
* クラスター UUID: f31dd18f-4dab-49fa-97bb-8feba31cb30b
* クラスター名: nks-test
* コンポーネント: kube-apiserver
* 保存時刻: 2025-04-28 10:15:00

OBS コンテナにログが生成されるパスは次のとおりです。

* nks_log_container/NKS/f31dd18f-4dab-49fa-97bb-8feba31cb30b/
  nks-test-master-0/kube-apiserver/2025/04/20250428-101500-index0.gz

<a id="k8s-taint"></a>
### Kubernetes テイント設定機能 { #k8s-taint }
ノードグループごとに Kubernetes テイント (taint) 設定機能を使用できます。この機能を使用して作成されたノードグループは、ユーザーが設定したテイントが適用された状態で初期化されます。Taint の詳細については、[Taints and Tolerations](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/) を参照してください。

Kubernetes テイントはキー、値、効果 (effect) で構成され、各項目は次のルールに従う必要があります。

<a id="k8s-taint-taint-key"></a>
#### テイントキー

テイントキーは、スラッシュ (/) で区切られた接頭辞と名前の構造を持つことができ、接頭辞は省略可能です。

* 接頭辞
    * 253 文字以下である必要があります。
    * DNS のサブドメイン形式である必要があります。
    * 事前定義された接頭辞は使用できません。
        * ["kubernetes.io", "k8s.io", "magnum.openstack.org"]
* 名前
    * 63 文字以下である必要があります。
    * 英字大文字・小文字、数字、ダッシュ (-)、アンダースコア (_)、ドット (.) のみ使用可能で、英数字で始まり英数字で終わる必要があります。

<a id="k8s-taint-taint-value"></a>
#### テイント値

* 空白または 63 文字以下である必要があります。
* 英字大文字・小文字、数字、ダッシュ (-)、アンダースコア (_)、ドット (.) のみ使用可能で、英数字で始まり英数字で終わる必要があります。

<a id="k8s-taint-taint-effect"></a>
#### テイント Effect

次の 3 つの値のいずれかを指定する必要があります。

* NoSchedule
    * 該当 Taint を許容 (Toleration) しない Pod はノードにスケジューリングされません。
    * 既存に実行中の Pod は影響を受けません。
* PreferNoSchedule
    * できる限り該当ノードを避けてスケジューリングしますが、不可能な場合はスケジューリングされる場合があります。
* NoExecute
    * 該当 Taint を許容しない既存の Pod はノードから即座に退避 (eviction) され、新規 Pod もスケジューリングされません。

[注記]
* Kubernetes テイントはノードグループあたり最大 30 個まで指定できます。
* Kubernetes テイント設定を変更すると、以降に新規作成されるノードから変更後の設定が適用されます。

<a id="konnectivity-description"></a>
### konnectivity { #konnectivity-description }

Konnectivity は、Kubernetes においてコントロールプレーン (API サーバー) とワーカーノード間のネットワーク通信を安全にプロキシするコンポーネントです。従来は API サーバーがノードの kubelet や Pod に直接アクセスする必要があり、ネットワーク構成が複雑になる問題がありました。

Konnectivity はこれを解決するために 2 つの部分で構成されます。
* Konnectivity Server: コントロールプレーンに存在し、API サーバーから受け取ったリクエストを Konnectivity Agent に転送します。
* Konnectivity Agent: ワーカーノードに存在し、Konnectivity Server から受け取ったリクエストを対象 Pod に転送し、その応答を再び Konnectivity Server に返します。

Konnectivity Server と Konnectivity Agent が先に接続を確立してトンネルを生成し、API サーバーはこのトンネルを通じて Pod と通信します。

> [注意]
> 以下のリソースは Konnectivity Agent に関連するリソースであり、対象リソースへの設定変更やリソースの削除などはクラスターの動作に重大な影響を与える可能性があります。
> 
> | 種類 | ネームスペース | 名前 |
> | --- | --- | --- |
> | ServiceAccount | kube-system | konnectivity-agent |
> | ClusterRoleBinding | kube-system | konnectivity-server-auth-delegator |
> | Deployment | kube-system | konnectivity-agent |

> [注記]
> Konnectivity はプラットフォームバージョン 1.202605.0 以上で提供されます。

<a id="worker-node-management"></a>
## ワーカーノード管理 { #worker-node-management }

<a id="container-management"></a>
### コンテナ管理 { #container-management }

<a id="container-management-clusters-of-kubernetes-v1243-or-older"></a>
#### Kubernetes v1.24.3 以前のバージョンのクラスター
Kubernetes v1.24.3 以前のバージョンのクラスターは、Docker を使用してコンテナランタイムを構成します。ワーカーノードで docker CLI を使用してコンテナの状態照会、コンテナイメージの照会などの作業を行うことができます。docker CLI の詳細な説明と使用方法については、[Use the Docker command line](https://docs.docker.com/engine/reference/commandline/cli/) を参照してください。

<a id="container-management-clusters-of-kubernetes-v1243-and-later"></a>
#### Kubernetes v1.24.3 以降のバージョンのクラスター

Kubernetes v1.24.3 以降のバージョンのクラスターは、containerd を使用してコンテナランタイムを構成します。ワーカーノードで docker CLI の代わりに nerdctl を使用してコンテナの状態照会、コンテナイメージの照会などの作業を行うことができます。nerdctl の詳細な説明と使用方法については、[nerdctl: Docker-compatible CLI for containerd](https://github.com/containerd/nerdctl#nerdctl-docker-compatible-cli-for-containerd) を参照してください。

<a id="network-management"></a>
### ネットワーク管理 { #network-management }

<a id="network-management-default-network-interface"></a>
#### デフォルトネットワークインターフェース
すべてのワーカーノードは、クラスター作成時に入力した VPC/サブネットに接続されるネットワークインターフェースを持っています。このデフォルトネットワークインターフェースの名前は「eth0」であり、ワーカーノードはこのネットワークインターフェースを通じてコントロールプレーンと接続されます。

<a id="network-management-additional-network-interface"></a>
#### 追加ネットワークインターフェース
クラスターまたはワーカーノードグループ作成時に追加ネットワークを設定すると、該当ワーカーノードグループのワーカーノードに追加ネットワークインターフェースが作成されます。追加ネットワークインターフェースは、追加ネットワーク設定に入力した順序でインターフェース名が設定されます（eth1、eth2、...）。

<a id="network-management-default-route-settings"></a>
#### デフォルトルート（default route）設定
ワーカーノードに複数のネットワークインターフェースが存在する場合、各ネットワークインターフェースごとにデフォルトルートが設定されます。1 つのシステムに複数のデフォルトルートが設定されている場合、メトリック（metric）値が最も低いデフォルトルートがシステムのデフォルトルートとして動作します。ネットワークインターフェースごとのデフォルトルートは、インターフェース番号が小さいほど低いメトリック値が設定されています。これにより、動作中のネットワークインターフェースの中で最も番号が小さいネットワークインターフェースがシステムのデフォルトルートとして動作します。

システムのデフォルトルートを追加ネットワークインターフェースに設定するには、以下の作業が必要です。

##### 1. ネットワークインターフェースごとのメトリック設定変更
ワーカーノードのすべてのネットワークインターフェースは、DHCP サーバーを通じて IP アドレスを割り当てられます。DHCP サーバーから IP アドレスを割り当てられる際に、ネットワークインターフェースごとのデフォルトルートが設定されます。この際、各デフォルトルートのメトリック値はインターフェースごとに事前に設定されています。Linux ディストリビューションごとの保存場所および設定項目は次のとおりです。

* CentOS
    * 設定ファイルの場所: /etc/sysconfig/network-scripts/ifcfg-{ネットワークインターフェース名}
    * メトリック値の設定項目: METRIC
* Ubuntu
    * 設定ファイルの場所: /etc/systemd/network/toastcloud-{ネットワークインターフェース名}.network
    * メトリック値の設定項目: DHCP セクションの RouteMetric

> [注意]
> デフォルトルートごとのメトリック値は、デフォルトルートが設定されるタイミングで決定されます。
> そのため、変更された設定は次回のデフォルトルート設定タイミングで適用されます。
> 現在システムに適用されているルートごとのメトリック値を変更するには、以下の`現在のルートのメトリック値変更`を参照してください。

##### 2. 現在のルートのメトリック値変更

システムのデフォルトルートを変更するために、ネットワークインターフェースごとのデフォルトルートのメトリック値を調整できます。以下は、route コマンドを使用して各デフォルトルートのメトリック値を調整する例です。

以下は、作業実行前の状態です。インターフェース番号が小さいほどメトリック値が小さく設定されていることを確認できます。
```
# route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         10.0.0.1        0.0.0.0         UG    0      0        0 eth0
0.0.0.0         192.168.0.1     0.0.0.0         UG    100    0        0 eth1
0.0.0.0         172.16.0.1      0.0.0.0         UG    200    0        0 eth2
...
```

eth1 をシステムのデフォルトルートに設定するために、eth1 のメトリック値を 0 に、eth0 のメトリック値を 100 に変更します。メトリック値のみを変更することはできないため、ルートを削除してから再度追加する必要があります。まず eth0 のルートを削除し、eth0 のメトリック値を 100 に設定します。

```
# route del -net 0.0.0.0/0 dev eth0
# route add -net 0.0.0.0/0 gw 10.0.0.1 dev eth0 metric 100
```

eth1 も先に既存のルートを削除し、eth1 のメトリックを 0 に設定します。
```
# route del -net 0.0.0.0/0 dev eth1
# route add -net 0.0.0.0/0 gw 192.168.0.1 dev eth1 metric 0
```

再度ルートを確認すると、メトリック値が変更されていることを確認できます。
```
# route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         192.168.0.1     0.0.0.0         UG    0      0        0 eth1
0.0.0.0         10.0.0.1        0.0.0.0         UG    100    0        0 eth0
0.0.0.0         172.16.0.1      0.0.0.0         UG    200    0        0 eth2
...
```

<a id="network-management-change-default-route-settings-using-user-script"></a>
#### ユーザースクリプト機能を使用したデフォルトルート設定の変更
ユーザースクリプト機能を使用すると、ノードの追加などによりノードが新たに初期化される際にも、上記の設定を維持できます。以下のユーザースクリプトは、CentOS を使用するワーカーノードで eth0 のメトリック値を 100 に、eth1 のメトリック値を 0 に設定する例です。これにより、現在システムに適用されているデフォルトルートごとのメトリック値も変更され、ワーカーノードの再起動後も維持されます。
```
#!/bin/bash
sed -i -e 's|^METRIC=.*$|METRIC=100|g' /etc/sysconfig/network-scripts/ifcfg-eth0
sed -i -e 's|^METRIC=.*$|METRIC=0|g' /etc/sysconfig/network-scripts/ifcfg-eth1
route del -net 0.0.0.0/0 dev eth0
route add -net 0.0.0.0/0 gw 10.0.0.1 dev eth0 metric 100
route del -net 0.0.0.0/0 dev eth1
route add -net 0.0.0.0/0 gw 192.168.0.1 dev eth1 metric 0
```

<a id="kubelet-argument"></a>
### kubelet ユーザー定義引数設定機能 { #kubelet-argument }
kubelet はすべてのワーカーノードで動作するノードエージェントです。kubelet はコマンドライン引数を使用してさまざまな設定を受け取ります。NKS が提供する kubelet ユーザー定義引数設定機能を使用すると、kubelet 起動時に入力される引数を追加できます。kubelet ユーザー定義引数は、次のように設定してシステムに適用できます。

* ワーカーノードの `/etc/kubernetes/kubelet-user-args` ファイルに `KUBELET_USER_ARGS="ユーザー定義引数"` の形式でユーザー定義引数を入力します。
* `systemctl daemon-reload` コマンドを実行します。
* `systemctl restart kubelet` コマンドを実行します。
* `systemctl status kubelet` コマンドで kubelet が正常動作中であることを確認します。

> [注意]
> * この機能は、2023 年 11 月 28 日以降に新規作成されたクラスターでのみ動作します。
> * ユーザー定義引数を設定するワーカーノードごとに実行します。
> * 正しくない形式のユーザー定義引数を入力した場合、kubelet が正常に動作しません。
> * 設定されたユーザー定義引数は、システム再起動後も引き続き適用されます。

<a id="containerd-registry-config"></a>
### カスタム containerd レジストリ設定機能 (deprecated) { #containerd-registry-config }

> [注意]
> この機能は Kubernetes v1.34 以降では動作しません。
> containerd 2.2 を使用する Kubernetes v1.34 以降では、hosts.toml ファイルを使用してレジストリごとの設定を適用できます。
> 詳細については、[Registry Configuration](https://github.com/containerd/containerd/blob/main/docs/hosts.md) を参照してください。

v1.24.3 以降の NKS クラスターは、コンテナランタイムとして containerd v1.6 を使用します。NKS では、containerd のさまざまな設定のうち、レジストリに関連する項目をユーザー環境に合わせて設定できる機能を提供します。containerd v1.6 のレジストリ設定については、[Configure Image Registry](https://github.com/containerd/containerd/blob/release/1.6/docs/cri/registry.md) を参照してください。

ワーカーノードの初期化プロセス中に、カスタム containerd レジストリ設定ファイル (`/etc/containerd/registry-config.json`) が存在する場合、このファイルの内容を containerd 設定ファイル (`/etc/containerd/config.toml`) に適用します。カスタム containerd レジストリ設定ファイルが存在しない場合、containerd 設定ファイルにはデフォルトのレジストリ設定が適用されます。デフォルトのレジストリ設定の内容は次のとおりです。

```json
[
   {
      "registry": "docker.io",
      "endpoint_list": [
         "https://registry-1.docker.io"
      ]
   }
]
```

1 つのレジストリに対して設定できるキー/値の形式は次のとおりです。

```json
{
  "registry": "REGISTRY_NAME",
  "endpoint_list": [
     "ENDPOINT1",
     "ENDPOINT2"
  ],
  "tls": {
     "ca_file": "CA_FILEPATH",
     "cert_file": "CERT_FILEPATH",
     "key_file": "KEY_FILEPATH",
     "insecure_skip_verify": true_or_false
  },
  "auth": {
     "username": "USERNAME",
     "password": "PASSWORD",
     "auth": "AUTH",
     "identitytoken": "IDENTITYTOKEN"
  }
}
```

<a id="containerd-registry-config-example-1"></a>
#### 例 1

`docker.io` 以外に追加のレジストリを登録するには、次のように設定できます。

```json
[
   {
      "registry": "docker.io",
      "endpoint_list": [
         "https://registry-1.docker.io"
      ]
   },
   {
      "registry": "additional.registry.io",
      "endpoint_list": [
         "https://additional.registry.io"
      ]
   }
]
```

<a id="containerd-registry-config-example-2"></a>
#### 例 2

`docker.io` レジストリを削除し、HTTP をサポートするレジストリのみを登録するには、次のように設定できます。
```json
[
   {
      "registry": "user-defined.registry.io",
      "endpoint_list": [
         "http://user-defined.registry.io"
      ],
      "tls": {
         "insecure_skip_verify": true
      }
   }
]
```

<a id="containerd-registry-config-example-3"></a>
#### 例 3

ノード作成時にカスタム containerd レジストリ設定ファイルを例 2 の内容で作成するために、ユーザースクリプトを次のように設定できます。

```bash
mkdir -p /etc/containerd
echo '[ { "registry": "user-defined.registry.io", "endpoint_list": [ "http://user-defined.registry.io" ], "tls": { "insecure_skip_verify": true } } ]' > /etc/containerd/registry-config.json
```

> [注意]
> * containerd 設定ファイル (`/etc/containerd/config.toml`) は NKS によって管理されるファイルです。このファイルを任意に変更すると、NKS の機能動作にエラーが発生したり、任意に変更した内容が削除される場合があります。
> * カスタム containerd レジストリ設定機能で正しくないレジストリが設定されると、ワーカーノードが正常に動作しなくなる場合があります。
> * カスタム containerd レジストリ設定機能が containerd 設定ファイルに適用されるタイミングは、ワーカーノードの初期化プロセスです。ワーカーノードの初期化プロセスは、ワーカーノードの作成プロセスとワーカーノードグループのアップグレードプロセスに含まれます。
>     * ワーカーノード作成時にカスタム container レジストリ設定機能を適用するには、ユーザースクリプトでこの設定ファイルを作成するようにする必要があります。
>     * ワーカーノードグループのアップグレード時にカスタム container レジストリ設定機能を適用するには、すべてのワーカーノードにこのファイルを手動で設定した後、アップグレードを実行する必要があります。
> * カスタム containerd レジストリ設定ファイルが存在する場合、このファイルに設定された内容が containerd にそのまま適用されます。
>     * `docker.io` レジストリを使用するには、`docker.io` レジストリに関する設定も含める必要があります。`docker.io` レジストリの設定については、デフォルトのレジストリ設定を参照してください。
>     * `docker.io` レジストリを使用しない場合は、`docker.io` レジストリに関する設定を含めなければ問題ありません。ただし、1 つ以上のレジストリ設定が存在する必要があります。

<a id="constraints-on-cgroup"></a>
### Kubernetes バージョンと CGroup バージョンに関する制約事項 { #constraints-on-cgroup }
CGroup (Control Group) は Linux カーネルの機能で、プロセスグループの CPU、メモリ、ディスク I/O、ネットワークなどのシステムリソース使用量を制限・隔離・モニタリングできます。Kubernetes をはじめとするコンテナ技術の中核となる基盤の 1 つです。CGroup は最初のバージョン 1 (v1) から始まり、メモリ・I/O 制御機能を強化してバージョン 2 (v2) へと発展しました。Linux カーネルの機能であるため、CGroup v2 は Linux カーネルへの依存性を持ちます。そのため、比較的新しいディストリビューション/バージョンでのみ CGroup v2 がサポートされます。

NKS クラスター v1.34 以降では、ワーカーノードが CGroup v2 で動作する必要があります。これは、Kubernetes のエコシステムが今後 containerd 1.x の代わりに containerd 2.x を使用し、CGroup v1 の代わりに v2 を基盤として動作するという方針から生じた制約事項です。

NKS のワーカーノードは、次の場合に CGroup v2 で動作します。
* CGroup v2 に設定された OS イメージを使用してワーカーノードグループを作成した場合
* CGroup v1 に設定された OS イメージを使用してワーカーノードグループを作成した後、v1.34 へローリングアップグレードした場合

OS イメージのリリース日に応じて、デフォルトの CGroup バージョンを確認できます。
* 2026/03/10 以前にリリースされたイメージ: CGroup v1
* 2026/03/10 以降にリリースされたイメージ: CGroup v2

OS イメージのデフォルト CGroup バージョンが v1 であっても、v2 に変更できます。デフォルト CGroup バージョンが v1 の OS イメージを使用して作成したワーカーノードグループについて、次の場合にワーカーノードの CGroup バージョンを v1 から v2 に変更します。
* Kubernetes v1.34 へのローリングアップグレード時
* Kubernetes v1.34 へのローリングアップグレード後にワーカーノードを追加する場合

CGroup バージョンを v1 から v2 に変更できる OS イメージのディストリビューションの種類とバージョンは次のとおりです。
* Ubuntu 22.04 以降
* Rocky 9.0 以降

> [注意]
> * ワーカーノードの CGroup 設定を v1 から v2 に変更するプロセスには、**ワーカーノードの再起動**が含まれます。
> * grub.conf の無断変更など、ノードの再起動が不可能な状況の場合、CGroup バージョンの変更が失敗するだけでなく、インスタンスが起動しない状況になる可能性があります。
> * インスタンスの再起動に問題がない状態で、ワーカーノードグループの Kubernetes バージョンアップグレードを実行する必要があります。

デフォルト CGroup バージョンが v1 で、CGroup バージョンを v2 に変更できない OS イメージで作成したワーカーノードグループは、ローリングアップグレード方式で Kubernetes v1.34 にアップグレードすることはできません。この場合、Blue-Green 方式でワーカーノードグループをアップグレードできます。

<a id="worker-management-caution"></a>
### ワーカーノード管理の注意事項 { #worker-management-caution }
* ワーカーノードに pull されているコンテナイメージを任意に削除しないでください。NKS クラスターに必要な Pod が動作しなくなる場合があります。
* `shutdown`、`halt`、`poweroff` などのコマンドでシステムを任意に停止すると、コンソールから再起動できなくなります。ワーカーノードの起動/停止機能を使用してください。
* ワーカーノード内のさまざまな設定ファイルを任意に変更したり、システムサービスを任意に操作しないでください。NKS クラスターに深刻な問題が発生する可能性があります。

<a id="cni"></a>
## CNI (Container Network Interface) { #cni }
NHN Kubernetes Service (NKS) は、Addon 機能を通じてさまざまな種類の Container Network Interface (CNI) を提供します。クラスター作成時に Calico-VXLAN、Calico-eBPF、Cilium のいずれか 1 つの CNI を選択でき、デフォルト設定は Calico-VXLAN です。Calico-eBPF はコンテナワークロードを BGP ルーティングプロトコルで構成し、eBPF 技術を基盤として直接通信します。一部の区間 (NodePort など) は VXLAN を使用して通信します。Calico の eBPF に関する詳細は [about eBPF](https://docs.tigera.io/calico/latest/about/kubernetes-training/about-ebpf) を参照してください。Cilium は VXLAN オーバーレイネットワークを基盤とし、eBPF 技術を活用して高いネットワーク性能を提供します。Cilium の eBPF に関する詳細は [eBPF Datapath](https://docs.cilium.io/en/stable/network/ebpf/) を参照してください。

CNI ごとに選択できる OS の制約事項は次のとおりです。

| CNI | 使用可能な OS |
| :-: | :-: |
| Flannel | Centos, Rocky, Red Hat, Ubuntu |
| Calico-VXLAN | Centos, Rocky, Red Hat, Ubuntu |
| Calico-eBPF | Rocky, Ubuntu |
| Cilium | Rocky, Ubuntu |

<a id="calico-cni-types"></a>
### Calico CNI の種類 { #calico-cni-types }
NHN Kubernetes Service (NKS) が提供する Calico-VXLAN と Calico-eBPF には、次のような違いがあります。

|  | Calico-VXLAN | Calico-eBPF |
| :-: | :-: | :-: |
| コンテナネットワーク処理モジュール | Linux カーネルネットワークスタック | eBPF + Linux カーネルネットワークスタック |
| kube-proxy | 有効 | 無効 (eBPF が kube-proxy を代替) |
| ネットワーク方式 | VXLAN | 直接通信 |
| Pod to Pod 通信 | VXLAN カプセル化して通信 | 直接通信<sup>[1](#footnote_calico_1)</sup> |
| Service ClusterIP to Pod 通信 | VXLAN カプセル化して通信 | 直接通信 |
| Service NodePort to Pod 通信 | VXLAN カプセル化して通信 | VXLAN カプセル化して通信 |
| ネットワークポリシーの適用 | iptables ベース | eBPF ベース (カーネルレベル) |
| ネットワーク性能 | VXLAN カプセル化による性能低下 | 直接通信による高い性能 (低レイテンシー) |

注釈

* <a name="footnote_calico_1">1</a>: パケットの送信元 IP および宛先 IP が Pod IP に設定されます。強化されたセキュリティルールを使用する場合、このトラフィックに対するセキュリティルールを別途設定する必要があります。

> [注意事項]
> Calico v3.24.1 eBPF モードを使用するクラスターでは、Rocky 9.5 以降または Ubuntu 24.04 以降のイメージを使用するノードグループを作成することはできません。
> 該当イメージを使用するには、アドオン管理機能を通じて Calico を v3.28.2 以降にアップデートする必要があります。

<a id="security-group"></a>
## セキュリティグループ { #security-group }
クラスター作成時に強化されたセキュリティルールを True に設定すると、ワーカーノードのセキュリティグループ作成時に必須のセキュリティルールのみが作成されます。

<a id="mandatory-sg-rules"></a>
### クラスターワーカーノード必須セキュリティルール { #mandatory-sg-rules }

| 方向 | IP プロトコル | ポート範囲 | Ether | リモート | 説明 | 特記事項 |
| :-: | :-: | :-: | :-: | :-: | :-: | :-: |
| ingress | TCP | 10250 | IPv4 | ワーカーノード | kubelet ポート、方向: metrics-server(ワーカーノード) → kubelet(ワーカーノード) | |
| ingress | TCP | 10250 | IPv4 | NKS Control Plane | kubelet ポート、方向: kube-apiserver(NKS Control plane) → kubelet(ワーカーノード) | |
| ingress | TCP | 5473 | IPv4 | ワーカーノード | calico-typha ポート、方向: calico-node(ワーカーノード) → calico-typha(ワーカーノード) | CNI が Calico-VXLAN、Calico-eBPF の場合に作成されます |
| ingress | TCP | 179 | IPv4 | ワーカーノード | calico-node BGP ポート、方向: pod(ワーカーノード) → pod(ワーカーノード) | CNI が Calico-eBPF の場合に作成されます |
| ingress | TCP | 179 | IPv4 | NKS Control Plane | calico-node BGP ポート、方向: pod(NKS Control plane) → pod(ワーカーノード) | CNI が Calico-eBPF の場合に作成されます |
| ingress | UDP | 8472 | IPv4 | ワーカーノード | flannel vxlan overlay network ポート、方向: pod(ワーカーノード) → pod(ワーカーノード) | CNI が flannel の場合に作成されます |
| ingress | UDP | 8472 | IPv4 | ワーカーノード | flannel vxlan overlay network ポート、方向: pod(NKS Control plane) → pod(ワーカーノード) | CNI が flannel の場合に作成されます |
| ingress | UDP | 4789 | IPv4 | ワーカーノード | calico-node vxlan overlay network ポート、方向: pod(ワーカーノード) → pod(ワーカーノード) | CNI が Calico-VXLAN、Calico-eBPF の場合に作成されます |
| ingress | UDP | 4789 | IPv4 | NKS Control Plane | calico-node vxlan overlay network ポート、方向: pod(NKS Control plane) → pod(ワーカーノード) | CNI が Calico-VXLAN、Calico-eBPF の場合に作成されます |
| ingress | TCP | 4240 | IPv4 | ワーカーノード | cilium-agent health check ポート、方向: cilium-agent(ワーカーノード) → cilium-agent(ワーカーノード) | CNI が Cilium の場合に作成されます |
| ingress | ICMP | - | IPv4 | ワーカーノード | cilium ping health monitoring、方向: cilium-agent(ワーカーノード) → ワーカーノード | CNI が Cilium の場合に作成されます |
| ingress | UDP | 8472 | IPv4 | ワーカーノード | cilium vxlan overlay network ポート、方向: pod(ワーカーノード) → pod(ワーカーノード) | CNI が Cilium の場合に作成されます |
| ingress | UDP | 8472 | IPv4 | NKS Control Plane | cilium vxlan overlay network ポート、方向: pod(NKS Control plane) → pod(ワーカーノード) | CNI が Cilium の場合に作成されます |
| egress | TCP | 2379 | IPv4 | NKS Control Plane | etcd ポート、方向: calico-kube-controller(ワーカーノード) → etcd(NKS Control plane) | |
| egress | TCP | 6443 | IPv4 | Kubernetes API エンドポイント | kube-apiserver ポート、方向: kubelet、kube-proxy(ワーカーノード) → kube-apiserver(NKS Control plane) | |
| egress | TCP | 6443 | IPv4 | NKS Control Plane | kube-apiserver ポート、方向: default kubernetes service(ワーカーノード) → kube-apiserver(NKS Control plane) | |
| egress | TCP | 5473 | IPv4 | ワーカーノード | calico-typha ポート、方向: calico-node(ワーカーノード) → calico-typha(ワーカーノード) | CNI が Calico-VXLAN、Calico-eBPF の場合に作成されます |
| egress | TCP | 53 | IPv4 | ワーカーノード | DNS ポート、方向: ワーカーノード → 外部 | |
| egress | TCP | 443 | IPv4 | すべて許可 | HTTPS ポート、方向: ワーカーノード → 外部 | |
| egress | TCP | 80 | IPv4 | すべて許可 | HTTP ポート、方向: ワーカーノード → 外部 | |
| egress | TCP | 179 | IPv4 | ワーカーノード | calico-node BGP ポート、方向: pod(ワーカーノード) → pod(ワーカーノード) | CNI が Calico-eBPF の場合に作成されます |
| egress | TCP | 179 | IPv4 | NKS Control Plane | calico-node BGP ポート、方向: pod(NKS Control plane) → pod(ワーカーノード) | CNI が Calico-eBPF の場合に作成されます |
| egress | UDP | 8472 | IPv4 | ワーカーノード | flannel vxlan overlay network ポート、方向: pod(ワーカーノード) → pod(ワーカーノード) | CNI が flannel の場合に作成されます |
| egress | UDP | 8472 | IPv4 | NKS Control Plane | flannel vxlan overlay network ポート、方向: pod(ワーカーノード) → pod(NKS Control plane) | CNI が flannel の場合に作成されます |
| egress | UDP | 4789 | IPv4 | ワーカーノード | calico-node vxlan overlay network ポート、方向: pod(ワーカーノード) → pod(ワーカーノード) | CNI が Calico-VXLAN、Calico-eBPF の場合に作成されます |
| egress | UDP | 4789 | IPv4 | NKS Control Plane | calico-node vxlan overlay network ポート、方向: pod(ワーカーノード) → pod(NKS Control plane) | CNI が Calico-VXLAN、Calico-eBPF の場合に作成されます |
| egress | TCP | 4240 | IPv4 | ワーカーノード | cilium-agent health check ポート、方向: cilium-agent(ワーカーノード) → cilium-agent(ワーカーノード) | CNI が Cilium の場合に作成されます |
| egress | ICMP | - | IPv4 | ワーカーノード | cilium ping health monitoring、方向: ワーカーノード → cilium-agent(ワーカーノード) | CNI が Cilium の場合に作成されます |
| egress | UDP | 8472 | IPv4 | ワーカーノード | cilium vxlan overlay network ポート、方向: pod(ワーカーノード) → pod(ワーカーノード) | CNI が Cilium の場合に作成されます |
| egress | UDP | 8472 | IPv4 | NKS Control Plane | cilium vxlan overlay network ポート、方向: pod(ワーカーノード) → pod(NKS Control plane) | CNI が Cilium の場合に作成されます |
| egress | UDP | 53 | IPv4 | すべて許可 | DNS ポート、方向: ワーカーノード → 外部 | |

強化されたセキュリティルールを使用する場合、NodePort タイプのサービスと NHN Cloud NAS サービスで使用するポートのセキュリティルールは追加されていません。必要に応じて、以下のセキュリティルールを追加設定する必要があります。

| 方向 | IP プロトコル | ポート範囲 | Ether | リモート | 説明 |
| :-: | :-: | :-: | :-: | :-: | :-: |
| ingress、egress | TCP | 30000 - 32767 | IPv4 | すべて許可 | NKS service object NodePort、方向: 外部 → ワーカーノード |
| egress | TCP | 2049 | IPv4 | NHN Cloud NAS サービス IP アドレス | csi-nfs-node の rpc nfs ポート、方向: csi-nfs-node(ワーカーノード) → NHN Cloud NAS サービス |
| egress | TCP | 111 | IPv4 | NHN Cloud NAS サービス IP アドレス | csi-nfs-node の rpc portmapper ポート、方向: csi-nfs-node(ワーカーノード) → NHN Cloud NAS サービス |
| egress | TCP | 635 | IPv4 | NHN Cloud NAS サービス IP アドレス | csi-nfs-node の rpc mountd ポート、方向: csi-nfs-node(ワーカーノード) → NHN Cloud NAS サービス |

> [Calico-eBPF CNI 使用時の注意]
> Calico-eBPF CNI を使用する場合、Pod 間の通信およびノードから Pod への通信は、Pod に設定されたポートを通じて行われます。
> 強化されたセキュリティルールを使用する場合、該当する Pod ポートに対する ingress および egress のセキュリティルールを手動で追加する必要があります。

<a id="cilium-optional-security-group-rules"></a>
### Cilium CNI 選択的機能使用時の追加セキュリティグループルール { #cilium-optional-security-group-rules }

Cilium CNI を使用するクラスターで Hubble、Envoy、Prometheus などの選択的機能を有効にするには、該当機能に必要なセキュリティグループルールを追加設定する必要があります。

##### 選択的機能別の必要ポート

| 機能 | 方向 | IP プロトコル | ポート範囲 | リモート | 説明 |
| :-: | :-: | :-: | :-: | :-: | :-: |
| Hubble Observability | ingress、egress | TCP | 4244 | ワーカーノード | hubble server ポート、方向: hubble-relay(ワーカーノード) → hubble-server(ワーカーノード) |
| Hubble UI | ingress、egress | TCP | 4245 | ワーカーノード | hubble relay ポート、方向: hubble-ui(ワーカーノード) → hubble-relay(ワーカーノード) |
| Cilium Agent Metrics | ingress、egress | TCP | 9962 | ワーカーノード | cilium-agent prometheus metrics ポート |
| Cilium Operator Metrics | ingress、egress | TCP | 9963 | ワーカーノード | cilium-operator prometheus metrics ポート |
| Cilium Envoy Metrics | ingress、egress | TCP | 9964 | ワーカーノード | cilium-envoy prometheus metrics ポート |
| WireGuard 暗号化 | ingress、egress | UDP | 51871 | ワーカーノード | WireGuard transparent encryption ポート |
| IPsec 暗号化 | ingress、egress | UDP | 500 | ワーカーノード | IPsec IKE ポート |
| IPsec 暗号化 | ingress、egress | UDP | 4500 | ワーカーノード | IPsec NAT-T ポート |
| IPsec 暗号化 | ingress、egress | ESP (50) | - | ワーカーノード | IPsec ESP プロトコル |

> [注記]
> Cilium の基本インストールには上記の選択的機能は含まれていません。
> 選択的機能を使用するには、Cilium の設定変更および該当機能に必要なセキュリティグループルールを手動で追加する必要があります。

<a id="relaxd-sg-rules"></a>
### 強化されたセキュリティルールを使用しない場合に作成されるルール { #relaxd-sg-rules }

強化されたセキュリティルールを使用しない場合、NodePort タイプのサービスと外部ネットワーク通信に必要なセキュリティルールが追加で作成されます。

| 方向 | IP プロトコル | ポート範囲 | Ether | リモート | 説明 |
| :-: | :-: | :-: | :-: | :-: | :-: |
| ingress | TCP | 1 - 65535 | IPv4 | ワーカーノード | すべてのポート、方向: ワーカーノード → ワーカーノード |
| ingress | TCP | 1 - 65535 | IPv4 | NKS Control Plane | すべてのポート、方向: NKS Control plane → ワーカーノード |
| ingress | TCP | 30000 - 32767 | IPv4 | すべて許可 | NKS service object NodePort、方向: 外部 → ワーカーノード |
| ingress | UDP | 1 - 65535 | IPv4 | ワーカーノード | すべてのポート、方向: ワーカーノード → ワーカーノード |
| ingress | UDP | 1 - 65535 | IPv4 | NKS Control Plane | すべてのポート、方向: NKS Control plane → ワーカーノード |
| egress | 任意 | 1 - 65535 | IPv4 | すべて許可 | すべてのポート、方向: ワーカーノード → 外部 |
| egress | 任意 | 1 - 65535 | IPv6 | すべて許可 | すべてのポート、方向: ワーカーノード → 外部 |


<a id="addon-mgmt"></a>
## アドオン管理機能 { #addon-mgmt }
アドオンとは、Kubernetes クラスターの必須構成要素ではないものの、NKS クラスターの機能を拡張したり、特化した機能を提供するために用意された構成要素のことです。アドオンには、ネットワーキング、サービスディスカバリー、モニタリング、ストレージプロビジョニングなどの機能を担う構成要素が含まれる場合があります。ユーザーはアドオン管理機能を通じて、NHN Cloud が提供するアドオンをクラスターにインストール/変更/削除できます。

> [注意]
> NKS レジストリが有効になっていないクラスターでは、アドオン管理機能を使用できません。

<a id="addon-mgmt-operation"></a>
### 動作方式 { #addon-mgmt-operation }
アドオン管理機能の動作方式について説明します。

<a id="addon-mgmt-operation-server-side-apply"></a>
#### Server-side apply
アドオン管理機能を使用してクラスターにアドオンをインストール/変更する際は、Kubernetes の Server-side apply を使用します。Client-side apply は、クライアントがローカルでリソース状態を計算し、リソース全体を API サーバーに送信する方式です。一方 Server-side apply は、API サーバーがリソースのマージおよびフィールド所有権の管理を行い、リソースのマージと競合検出を実行できます。Server-side apply の詳細については、[Server-Side Apply](https://kubernetes.io/docs/reference/using-api/server-side-apply/) を参照してください。


<a id="addon-mgmt-operation-conflict-resolution-options"></a>
#### 競合処理オプション
ユーザーがアドオンの管理するフィールドを変更して使用している場合、アドオンのインストール/変更時に競合が発生する可能性があります。ユーザーはアドオンのインストール/変更時に適切な競合処理オプション (resolve-conflicts) を選択して、競合状況を管理できます。アドオン管理機能が提供する競合処理オプションは次のとおりです。

* なし (none): 競合発生時にインストール/変更が適用されず、インストール/変更リクエストは失敗として処理されます。
* 上書き (overwrite): 競合発生時に競合するフィールドをアドオンで定義されているデフォルト値で上書きします。
* 保持 (preserve): 競合発生時に競合するフィールドを既存の値で保持します。

> [バージョン変更時の注意事項]
> アドオンのバージョン変更時に、必須構成要素のデフォルト設定値が変更される場合があります。ユーザーが該当フィールドを直接変更していない場合でも競合が発生する可能性があり、競合処理オプションを「なし」または「保持」に選択した場合、アドオンのインストール/変更が失敗する可能性があります。競合処理オプションを「上書き」に選択することで競合を防ぐことができます。

> [保持オプションに関する注意事項]
> アドオンを構成するリソースのすべての変更を保持することはできません。
> 保持できないフィールドで競合が発生した場合、インストール/変更作業は失敗として処理されます。

<a id="addon-mgmt-operation-main-features"></a>
#### 提供機能
アドオン管理機能を使用して、アドオンをクラスターにインストール/変更/削除できます。

* インストール
    * クラスターにアドオンをインストールします。
    * アドオンバージョン、アドオンごとのオプションを指定してインストールします。
    * インストール時に競合処理オプションを指定してインストールします。
* 変更
    * クラスターにインストールされているアドオンを変更します。
    * アドオンバージョン、アドオンごとのオプションなどを変更できます。
        * アドオンによってはオプションの変更ができない場合があります。
    * 変更時に競合処理オプションを指定して変更します。
* 削除
    * クラスターからアドオンを構成するリソースをすべて削除します。
    * ただし、必須タイプは削除できません。

> [注意]
> Kubernetes バージョンアップグレード機能による CNI、coredns などのアップグレードは提供されなくなりました。
> 代わりに、アドオン変更機能を通じて各アドオンのバージョンを変更できます。

<a id="addon-mgmt-operation-enable-add-on-management"></a>
#### アドオン管理機能の有効化
アドオン管理機能が有効になっていない既存のクラスターでも、アドオン管理機能を使用できます。アドオンが設定されていないクラスターは、calico や coredns などが動作していても、アドオンがインストールされていないものとして表示されます。この状態で各アドオンをインストールすると、以降はアドオン管理機能を通じてアドオンを管理できます。アドオンを構成するリソースの設定を変更して使用している場合、競合処理オプションを「保持」に選択してインストールすることで、既存リソースの設定を維持できます。

<a id="addon-mgmt-types"></a>
### アドオンタイプ { #addon-mgmt-types }
アドオンタイプとは、クラスターにインストールされるアドオンを特性に応じて分類したものです。

| タイプ | 必須 | 説明 |
|---|---|---|
| CNI | O | クラスターにインストールされる CNI に該当するタイプです。 |
| kube-dns | O | NKS クラスター内で動作するデフォルト DNS サーバーです。 |
| cinder-csi-plugin | X | NHN Cloud のブロックストレージをプロビジョニングおよび管理できる CSI ドライバーです。 |
| metrics-server | X | オートスケーリングとモニタリングのために、ノードと Pod からリソース使用指標を収集する Kubernetes の構成要素です。 |
| snapshot-controller | X | ボリュームスナップショットの作成、削除、PVC 連携を含むライフサイクルを管理する Kubernetes の構成要素です。 |
| nfs-csi-plugin | X | NHN Cloud の NFS をプロビジョニングおよび管理できる CSI ドライバーです。 |

<a id="addon-mgmt-addon-list"></a>
### アドオン一覧 { #addon-mgmt-addon-list }

<a id="addon-mgmt-addon-calico"></a>

#### Calico
Calico は Kubernetes のネットワーキングとネットワークセキュリティを提供する CNI プラグインです。NHN Cloud が提供する Calico の詳細については、[Calico CNI の種類](#calico-cni-types) を参照してください。

* タイプ: CNI
* オプション
    * mode
        * Calico の動作モードを決定します。
        * サポートする動作モード: vxlan, ebpf
* ユーザー変更不可リソースおよびフィールド
    * Deployment/calico-kube-controllers、ネームスペース kube-system
        * .spec.template.spec.containers[name="calico-kube-controllers"].image 
    * Deployment/calico-typha、ネームスペース kube-system
        * .spec.template.spec.containers[name="calico-typha"].image
    * DaemonSet/calico-node、ネームスペース kube-system
        * .spec.template.spec.initContainers[name="install-cni"].image
        * .spec.template.spec.initContainers[name="mount-bpffs"].image
        * .spec.template.spec.containers[name="calico-node"].image
* サポートバージョン一覧
    * v3.28.2-nks1
    * v3.28.2-nks2: アドオン管理機能の安定性を強化しました。
    * v3.28.2-nks3: konnectivity 環境をサポートします。
    * v3.28.2-nks4: アドオン管理機能の安定性を強化しました。
    * v3.30.2-nks1
    * v3.30.2-nks2: アドオン管理機能の安定性を強化しました。
    * v3.30.2-nks3: konnectivity 環境をサポートします。
    * v3.30.2-nks4: アドオン管理機能の安定性を強化しました。
    * v3.31.4-nks1: データストアは KDD (Kubernetes Datastore Driver) であり、konnectivity 環境をサポートします。
    * v3.31.4-nks2: アドオン管理機能の安定性を強化しました。

> [注記]
> * konnectivity をサポートするプラットフォームバージョン (1.202605.0 以上) でインストール/アップデート可能な calico バージョンは次のとおりです。
>     * v3.28.2-nks3 以上
>     * v3.30.2-nks3 以上
>     * v3.31.4 以上

<a id="addon-mgmt-addon-calico-datastore"></a>
##### Calico のデータストア
calico は Pod IP、ノードごとの IP 帯域など、さまざまな情報をデータストアに保存します。これまで提供されていたバージョンではデータストアに etcd を使用していましたが、新たに提供されるバージョンではデータストアに KDD (Kubernetes Datastore Driver) を使用します。KDD は Kubernetes CRD を使用して各種情報を Kubernetes レベルのリソース/オブジェクトに保存します。KDD を使用するとネットワークトポロジーが単純になり、関連情報がすべて CR として公開されるため、管理上の利点があります。

以下のバージョンはデータストアに etcd を使用します。
* v3.28.2
* v3.30.2

以下のバージョンはデータストアに KDD を使用します。
* v3.31.4 以上

> [注意]
> * データストアを etcd → KDD に変更するアドオンアップデート時の競合オプションは「上書き (overwrite)」のみサポートします。
> * データストアを KDD → etcd に変更するアドオンアップデートはサポートされていません。

<a id="addon-mgmt-addon-cilium"></a>

#### Cilium
Cilium は Kubernetes のネットワーキングとネットワークセキュリティを提供する CNI プラグインです。

* タイプ: CNI
* オプション: なし
* ユーザー変更不可リソースおよびフィールド
    * DaemonSet/cilium、ネームスペース kube-system
        * .spec.template.spec.containers[name="cilium-agent"].image
        * .spec.template.spec.containers[name="cilium-envoy"].image
    * Deployment/cilium-operator、ネームスペース kube-system
        * .spec.template.spec.containers[name="cilium-operator"].image
* サポートバージョン一覧
    * v1.18.0-nks1
    * v1.18.0-nks2: アドオン管理機能の安定性を強化しました。

<a id="addon-mgmt-addon-coredns"></a>

#### CoreDNS
CoreDNS は Kubernetes クラスターのデフォルト DNS サーバーです。

* タイプ: kube-dns
* オプション: なし
* ユーザーが変更できないリソースおよびフィールド
    * Deployment/coredns、ネームスペース kube-system
        * .spec.template.spec.containers[name="coredns"].image'
* サポートバージョン一覧
    * 1.8.4-nks1
    * 1.8.4-nks2
        * アドオン管理機能の安定性を強化しました。
        * ユーザーが変更できないリソースおよびフィールドを調整しました。
            * Deployment/coredns、ネームスペース kube-system
                * .metadata.labels.k8s-app を削除
                * .metadata.labels.kubernetes.io/name を削除
                * .spec.template.spec.nodeSelector を削除
                * .spec.template.spec.serviceAccountName を削除
    * 1.8.4-nks3: アドオン管理機能の安定性を強化しました。

<a id="addon-mgmt-addon-cinder-csi-plugin">
<a id="addon-mgmt-addon-list-cinder-csi-plugin"></a>

#### Cinder CSI Plugin
Cinder CSI Plugin は NHN Cloud でブロックストレージをプロビジョニングおよび管理できる CSI ドライバーです。

* タイプ: cinder-csi-plugin
* オプション: なし
* ユーザー変更不可リソースおよびフィールド
    * StatefulSet/csi-cinder-controllerplugin、ネームスペース kube-system
        * .spec.template.spec.containers[name="csi-attacher"].image
        * .spec.template.spec.containers[name="csi-provisioner"].image
        * .spec.template.spec.containers[name="csi-snapshotter"].image
        * .spec.template.spec.containers[name="csi-resizer"].image
        * .spec.template.spec.containers[name="cinder-csi-plugin"].image

* サポートバージョン一覧
    * v1.27.101-nks1
    * v1.27.101-nks2: 内部コンテナバージョンが変更されました。
        * csi-attacher: v3.0.2 → v3.3.0
        * csi-provisioner: v2.0.4 → v2.2.2
        * csi-snapshotter: v3.0.2 → v3.0.3
        * csi-resizer: v1.0.1 → v1.3.0
        * csi-node-driver-registrar: v2.0.1 → v2.3.0
    * v1.27.101-nks3: アドオン管理機能の安定性を強化しました。
    * v1.27.102-nks1
    * v1.27.102-nks2: 内部コンテナバージョンが変更されました。
        * csi-attacher: v3.0.2 → v3.3.0
        * csi-provisioner: v2.0.4 → v2.2.2
        * csi-snapshotter: v3.0.2 → v3.0.3
        * csi-resizer: v1.0.1 → v1.3.0
        * csi-node-driver-registrar: v2.0.1 → v2.3.0
    * v1.27.102-nks3: アドオン管理機能の安定性を強化しました。
    * v1.27.102-nks4
        * アドオン管理機能の安定性を強化しました。
        * cinder-csi-nodeplugin DaemonSet の toleration から `effect: NoExecute` を削除しました。

<a id="adoon-mgmt-addon-metrics-server">
<a id="addon-mgmt-addon-list-metrics-server"></a>

#### Metrics Server
Metrics Server は、オートスケーリングとモニタリングのためにノードと Pod からリソース使用指標を収集する Kubernetes の構成要素です。

* タイプ: metrics-server
* オプション: なし
* ユーザーが変更不可能なリソースおよびフィールド
    * Deployment/metrics-server、ネームスペース kube-system
        * .spec.template.spec.containers[name="metrics-server"].image
* サポートバージョン一覧
    * v0.4.4-nks1
    * v0.4.4-nks2: アドオン管理機能の安定性を強化しました。
    * v0.4.4-nks3: アドオン管理機能の安定性を強化しました。

<a id="addon-mgmt-addon-snapshot-controller">
<a id="addon-mgmt-addon-list-snapshot-controller"></a>

#### Snapshot Controller
Snapshot Controller は、ボリュームスナップショットの作成、削除、PVC 連携を含むライフサイクルを管理する Kubernetes の構成要素です。

* タイプ: snapshot-controller
* オプション: なし
* ユーザー変更不可のリソースおよびフィールド
    * Deployment/snapshot-controller、ネームスペース kube-system
        * .spec.template.spec.containers[name="snapshot-controller"].image
* サポートバージョン一覧
    * v4.1.1-nks1
    * v4.1.1-nks2: アドオン管理機能の安定性を強化しました。
    * v4.1.1-nks3: アドオン管理機能の安定性を強化しました。

<a id="addon-mgmt-addon-nfs-csi-plugin">
<a id="addon-mgmt-addon-list-nfs-csi-plugin"></a>

#### NFS CSI Plugin
NFS CSI Plugin は NHN Cloud の NFS をプロビジョニングおよび管理できる CSI ドライバーです。

* タイプ: nfs-csi-plugin
* オプション: なし
* ユーザー変更不可リソースおよびフィールド
    * Deployment/csi-nfs-controller、ネームスペース kube-system
        * .spec.template.spec.containers[name="csi-provisioner"].image
        * .spec.template.spec.containers[name="csi-snapshotter"].image
        * .spec.template.spec.containers[name="liveness-probe"].image
        * .spec.template.spec.containers[name="nfs"].image
    * DaemonSet/csi-nfs-node、ネームスペース kube-system
        * .spec.template.spec.containers[name="liveness-probe"].image
        * .spec.template.spec.containers[name="node-driver-registrar"].image
        * .spec.template.spec.containers[name="nfs"].image
* サポートバージョン一覧
    * v1.0.1-nks1
    * v1.0.1-nks2
        * アドオン管理機能の安定性を強化しました。
        * ユーザー変更不可リソース/フィールドを検査しない問題を修正しました。
    * v1.0.1-nks3: アドオン管理機能の安定性を強化しました。
    * v1.0.2-nks1
        * オプション項目である snapshot 設定が必須として要求される問題を修正しました。
    * v1.0.2-nks2: アドオン管理機能の安定性を強化しました。
    * v1.0.3-nks1: reclaimPolicy が Delete の storageclass ベースの PVC 削除時に PV が削除されない問題を修正しました。

<a id="loadbalancer-service"></a>
## LoadBalancer サービス { #loadbalancer-service }
Kubernetes アプリケーションの基本実行単位である Pod は、CNI (container network interface) によってクラスターネットワークに接続されます。デフォルトでは、クラスター外部から Pod へアクセスすることはできません。Pod のサービスをクラスター外部に公開するには、Kubernetes の `LoadBalancer` サービス (Service) オブジェクト (object) を使用して、外部に公開するパスを作成する必要があります。LoadBalancer サービスオブジェクトを作成すると、クラスター外部に NHN Cloud Load Balancer が作成され、サービスオブジェクトと関連付けられます。

<a id="create-webserver-pod"></a>
### Webサーバー Pod の作成 { #create-webserver-pod }
次のように、2つの nginx Pod を実行するデプロイメント (deployment) オブジェクトのマニフェストファイルを作成し、オブジェクトを作成します。

```yaml
# nginx.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```

デプロイメントオブジェクトを作成すると、マニフェストに定義した Pod が自動的に作成されます。

```
$ kubectl apply -f nginx.yaml
deployment.apps/nginx-deployment created

$ kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE  
nginx-deployment-7fd6966748-pvrzs   1/1     Running   0          4m13s
nginx-deployment-7fd6966748-wv7rd   1/1     Running   0          4m13s
```

<a id="create-lb-service"></a>
### LoadBalancer サービスの作成 { #create-lb-service }
Kubernetes のサービスオブジェクトを定義するには、次の項目で構成されたマニフェストが必要です。

| 項目 | 説明 |
| --- | --- |
| metadata.name | サービスオブジェクトの名前 |
| spec.selector | サービスオブジェクトに関連付ける Pod の名前 |
| spec.ports | 外部ロードバランサーから受信するトラフィックを Pod に転送するインターフェイスの設定 |
| spec.ports.name | インターフェイスの名前 |
| spec.ports.protocol | インターフェイスで使用するプロトコル (例: TCP) |
| spec.ports.port | サービスオブジェクトの外部に公開するポート番号 |
| spec.ports.targetPort | サービスオブジェクトに関連付ける Pod のポート番号 |
| spec.type | サービスオブジェクトの種類 |

次のようにサービスマニフェストを作成します。この LoadBalancer サービスオブジェクトは、**spec.selector** に定義された名前に従い、`app: nginx` ラベルが付いた Pod に関連付けられます。また、**spec.ports** に定義されたとおり、TCP/8080 ポートで受信したトラフィックを Pod の TCP/80 ポートに転送します。

```yaml
# service.yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-svc
  labels:
    app: nginx
spec:
  ports:
  - port: 8080
    targetPort: 80
    protocol: TCP
  selector:
    app: nginx
  type: LoadBalancer
```

LoadBalancer サービスオブジェクトを作成すると、クラスターの外部にロードバランサーを作成して接続するまで少し時間がかかります。外部ロードバランサーに接続される前は、**EXTERNAL-IP** 項目が `<pending>` と表示されます。

```
$ kubectl apply -f service.yaml
service/nginx-svc created

$ kubectl get service
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
nginx-svc    LoadBalancer   10.254.134.18   <pending>     8080:30013/TCP   11s
```

外部ロードバランサーに接続されると、**EXTERNAL-IP** 項目に IP が表示されます。この IP は外部ロードバランサーのフローティング IP です。

```
$ kubectl get service
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)          AGE
nginx-svc    LoadBalancer   10.254.134.18   123.123.123.30   8080:30013/TCP   3m13s
```

> [注記]
> 作成されたロードバランサーは **Network > Load Balancer** ページで確認できます。
> ロードバランサーの IP は外部からアクセスできるフローティング IP です。**Network > Floating IP** ページで確認できます。

<a id="internet-test-via-service"></a>
### インターネットを介したサービステスト { #internet-test-via-service }
ロードバランサーに接続されたフローティング IP に HTTP リクエストを送信し、Kubernetes クラスターの Web サーバー Pod が応答するかどうかを確認します。サービスオブジェクトの TCP/8080 ポートを Pod の TCP/80 ポートに接続するよう設定しているため、TCP/8080 ポートにリクエストを送信する必要があります。外部ロードバランサーとサービスオブジェクト、Pod が正しく接続されていれば、Web サーバーは nginx のデフォルトページを返します。

```
$ curl http://123.123.123.30:8080
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

<a id="advanced-lb-configuration"></a>
### ロードバランサーの詳細オプション設定 { #advanced-lb-configuration }
Kubernetes のサービスオブジェクトを定義する際に、ロードバランサーのさまざまなオプションを設定できます。設定可能な項目は次のとおりです。

* ロードバランサー名の設定
* keep-alive タイムアウトの設定
* ロードバランサータイプの設定
* 静的ルートの設定
* セッション持続性の設定
* フローティング IP アドレスの保持設定
* ロードバランサー IP の設定
* フローティング IP の使用設定
* VPC の設定
* サブネットの設定
* メンバーサブネットの設定
* リスナー接続制限の設定
* リスナープロトコルの設定
* リスナープロキシプロトコル (Proxy Protocol) の設定
* ロードバランシング方式の設定
* ヘルスチェックプロトコルの設定
* ヘルスチェック周期の設定
* ヘルスチェック最大応答時間の設定
* ヘルスチェック最大再試行回数の設定
* ヘルスチェックポートの設定
* ヘルスチェックホストヘッダーの設定
* L7 ルールおよび条件

<a id="advanced-lb-configuration-global-setting-and-per-listener-setting"></a>
#### 全体設定とリスナーごとの設定
設定項目ごとに全体設定とリスナーごとの設定が可能です。全体設定とリスナーごとの設定がどちらもない場合は、設定ごとのデフォルト値を使用します。

* リスナーごとの設定: 対象リスナーにのみ適用される設定です。
* 全体設定: 対象リスナーにリスナーごとの設定がない場合に、この設定を適用します。

<a id="advanced-lb-configuration-format-of-per-listener-setting"></a>
#### リスナーごとの設定形式
リスナーごとの設定は、全体設定のキーにリスナーを示すプレフィックス (prefix) を付けて設定できます。リスナーを示すプレフィックスは、サービスオブジェクトのポートプロトコル (`spec.ports[].protocol`) とポート番号 (`spec.ports[].port`) をダッシュ (`-`) で連結したものです。例えば、プロトコルが TCP でポート番号が 80 の場合、プレフィックスは `TCP-80` です。このポートに関連付けられるリスナーにセッション持続性を設定したい場合は、.metadata.annotations 配下の TCP-80.loadbalancer.nhncloud/pool-session-persistence に設定できます。

次のマニフェストは、全体設定とリスナーごとの設定を混在させた例です。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: echosvr-svc
  labels:
    app: echosvr
  annotations:
    # グローバル設定
    loadbalancer.nhncloud/pool-lb-method: SOURCE_IP
    
    # リスナー別設定
    TCP-80.loadbalancer.nhncloud/pool-session-persistence: "SOURCE_IP"
    TCP-80.loadbalancer.nhncloud/listener-protocol: "HTTP"
    TCP-443.loadbalancer.nhncloud/pool-lb-method: LEAST_CONNECTIONS
    TCP-443.loadbalancer.nhncloud/listener-protocol: "TCP"
spec:
  ports:
  - name: tcp-80
    port: 80
    targetPort: 8080
    protocol: TCP
  - name: tcp-443
    port: 443
    targetPort: 8443
    protocol: TCP
  selector:
    app: echosvr
  type: LoadBalancer
```

このマニフェストを適用した場合、リスナーごとの設定は次の表のように設定されます。

| 項目 | TCP-80 リスナー | TCP-433 リスナー | 説明 |
| --- | --- | --- | --- |
| ロードバランシング方式 | SOURCE_IP | LEAST_CONNECTIONS | TCP-80 リスナーは全体設定に従い SOURCE_IP で設定<br>TCP-443 リスナーはリスナーごとの設定に従い LEAST_CONNECTIONS で設定 |
| セッション持続性 | SOURCE_IP | None | TCP-80 リスナーはリスナーごとの設定に従い SOURCE_IP で設定<br>TCP-443 リスナーはデフォルト値に従い None で設定 |
| リスナープロトコル | HTTP | TCP | TCP-80 リスナーと TCP-443 リスナーはいずれもリスナーごとの設定に従い設定 |

> [注記]
> 別途記載がない機能は、Kubernetes v1.19.13 以降のバージョンのクラスターにのみ適用可能です。
> Kubernetes v1.19.13 バージョンのクラスターは、2022年1月25日以降に作成されたクラスターに限りリスナーごとの設定が適用されます。
>

> [注意]
> 以下の機能の設定値はすべて文字列形式で入力する必要があります。YAML ファイルの入力形式において、入力値の形式に関わらず文字列形式で入力するには、入力値を二重引用符 (") で囲んでください。YAML ファイル形式の詳細については、[Yaml Cookbook](https://yaml.org/YAML_for_ruby.html) ドキュメントを参照してください。
>

<a id="loadbalancer-update-without-modification"></a>
#### 設定を変更せずにロードバランサーを更新する方法

証明書の更新など、ロードバランサーの設定変更なしにロードバランサーの更新が必要な場合は、次のコマンドを使用できます。

```
# 以下のコマンドでアノテーションを設定します
kubectl annotate svc <name> loadbalancer.nhncloud/force-reconcile=true
```
ロードバランサーの更新が開始されると、上記のコマンドで設定した annotation は自動的に削除されます。

> [注意]
> この機能は、プラットフォームバージョンが 1.202605.0 以上のクラスターで動作します。

<a id="advanced-lb-configuration-setting-load-balancer-name"></a>
#### ロードバランサー名の設定

ロードバランサーの名前を設定できます。

* 設定箇所は .metadata.annotations 配下の loadbalancer.nhncloud/loadbalancer-name です。
* リスナーごとの設定は適用できません。
* 英字、数字、`-`、`_` のみ入力可能です。
    * 無効な文字が含まれている場合、デフォルトのロードバランサー名の形式に従ってロードバランサー名が設定されます。
    * デフォルトのロードバランサー名の形式: `"kube_service_{CLUSTER_UUID}_{SERVICE_NAMESPACE}_{SERVICE_NAME}"`
* 最大長は 255 文字で、最大長を超えた場合はロードバランサー名が 255 文字で切り詰められます。

> [注意]
> 次の操作を行うと、ロードバランサーが重大な誤動作を起こす可能性があります。
> * サービスオブジェクト作成後にロードバランサー名を変更する
> * プロジェクト内に同じ名前のロードバランサーを作成する

<a id="advanced-lb-configuration-set-load-balancer-type"></a>
#### ロードバランサータイプの設定
ロードバランサーのタイプを設定できます。ロードバランサーの詳細については、[ロードバランサーコンソール使用ガイド](/Network/Load%20Balancer/ja/console-guide/)を参照してください。

* 設定箇所は .metadata.annotations 配下の loadbalancer.nhncloud/loadbalancer-type です。
* リスナーごとの設定は適用できません。
* 次のいずれかに設定できます。
    * shared: 「一般」タイプのロードバランサーを作成します。未設定時のデフォルト値です。
    * dedicated: 「専用」タイプのロードバランサーを作成します。

<a id="advanced-lb-configuration-set-static-routes"></a>
#### 静的ルートの設定
ロードバランサーの静的ルート適用の有無を設定できます。

* 設定箇所は .metadata.annotations 配下の loadbalancer.nhncloud/apply-subnet-host-routes です。
* リスナーごとの設定は適用できません。
* 次のいずれかに設定できます。
    * true: 静的ルートを適用します。
    * false: 静的ルートを適用しません。未設定時のデフォルト値です。

> [注意]
> 静的ルートの設定は、2024年8月27日以降に作成されたか、k8s バージョンをアップグレードしたクラスターで設定可能です。

<a id="advanced-lb-configuration-set-the-session-affinity"></a>
#### セッション持続性の設定
ロードバランサーのセッション持続性を設定できます。

* 設定箇所は .metadata.annotations 配下の loadbalancer.nhncloud/pool-session-persistence です。
* リスナーごとの設定を適用できます。
* 次のいずれかに設定できます。
    * 空文字列 (""): セッション持続性を「なし」に設定します。未設定時のデフォルト値です。
    * SOURCE_IP: セッション持続性を SOURCE_IP に設定します。
* ロードバランシング方式が SOURCE_IP の場合、セッション持続性の設定は無視され、セッション持続性は「なし」に設定されます。
* v1.17.6、v1.18.19 クラスター
    * ロードバランサー作成後は変更できません。
* v1.19.13 以降のクラスター
    * ロードバランサー作成後も変更できます。

<a id="advanced-lb-configuration-set-whether-to-keep-a-floating-ip-address"></a>
#### フローティング IP アドレスの保持設定
ロードバランサーにはフローティング IP が接続されています。ロードバランサーの削除およびフローティング IP の変更時に、ロードバランサーに接続されたフローティング IP を削除するか保持するかを設定できます。

* 設定箇所は .metadata.annotations 配下の loadbalancer.openstack.org/keep-floatingip です。
* リスナーごとの設定は適用できません。
* 次のいずれかに設定できます。
    * true: フローティング IP を保持します。
    * false: フローティング IP を削除します。未設定時のデフォルト値です。

> [注記]
> フローティング IP アドレスの保持が設定されていない場合 (デフォルト値 false)、ロードバランサーの削除またはフローティング IP の変更時に、次の条件をすべて満たすフローティング IP は自動的に削除されます。
> 
> * サービスオブジェクト作成時に自動的に作成されたフローティング IP である場合
> * フローティング IP に削除保護が設定されていない場合
> 
> 上記の条件に該当しないフローティング IP は、フローティング IP アドレスの保持設定に関わらず削除対象になりません。

> [注意]
> 2021年10月26日以前に作成された v1.18.19 クラスターでは、ロードバランサーが削除されてもフローティング IP が削除されない問題があります。カスタマーセンターの 1:1 お問い合わせよりご連絡いただければ、この問題を解決するための手順について詳しくご案内いたします。


<a id="advanced-lb-configuration-set-the-load-balancer-ip"></a>
#### ロードバランサー IP の設定
ロードバランサーを作成する際に、ロードバランサーの IP を設定できます。

* 設定箇所は .spec.loadBalancerIP です。
* リスナーごとの設定は適用できません。
* 次のいずれかに設定できます。
  * 空文字列 (""): ロードバランサーに自動的に作成されるフローティング IP を接続します。未設定時のデフォルト値です。
  * \<Floating_IP\>: ロードバランサーに既存のフローティング IP を接続します。すでに割り当て済みだが未接続のフローティング IP がある場合に使用できます。

次はロードバランサーにユーザー定義のフローティング IP を接続するマニフェストの例です。

```yaml
# service-fip.yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-svc-floatingIP
  labels:
    app: nginx
spec:
  loadBalancerIP: <Floating_IP>
  ports:
  - port: 8080
    targetPort: 80
    protocol: TCP
  selector:
    app: nginx
  type: LoadBalancer
```

<a id="advanced-lb-configuration-set-whether-to-use-the-floating-ip"></a>
#### フローティング IP 使用有無の設定
ロードバランサー作成時に、フローティング IP を使用するかどうかを設定できます。

* 設定場所は .metadata.annotaions 配下の service.beta.kubernetes.io/openstack-internal-load-balancer です。
* リスナーごとの設定は適用できません。
* 次のいずれかに設定できます。
  * true: フローティング IP を使用せず、VIP (Virtual IP) を使用します。
  * false: フローティング IP を使用します。未設定の場合のデフォルト値です。
* VIP を使用する場合、.spec.loadBalancerIP 項目を合わせて設定することで、ロードバランサーに自動的に作成される VIP を接続する代わりに、VIP を指定して接続できます。

以下は、ロードバランサーにユーザー定義の VIP を接続するマニフェストの例です。

```yaml
# service-vip.yaml
apiVersion: v1
kind: Service
metadata:
 name: nginx-svc-fixedIP
 labels:
   app: nginx
 annotations:
   service.beta.kubernetes.io/openstack-internal-load-balancer: "true"
spec:
 loadBalancerIP: <Virtual_IP>
 ports:
 - port: 8080
   targetPort: 80
   protocol: TCP
 selector:
   app: nginx
 type: LoadBalancer
```

フローティング IP 使用有無の設定とロードバランサー IP 設定の組み合わせにより、次のように動作します。

| フローティング IP 使用有無の設定 | ロードバランサー IP 設定 | 説明 |
| --- | --- | --- |
| false | 未設定 | ロードバランサーにフローティング IP を作成して接続します。 |
| false | 設定 | ロードバランサーに指定されたフローティング IP を接続します。 |
| true | 未設定 | ロードバランサーに接続される VIP を自動的に設定します。 |
| true | 設定 | ロードバランサーに指定された VIP を接続します。 |


<a id="advanced-lb-configuration-set-vpc"></a>
#### VPC 設定
ロードバランサー作成時に、ロードバランサーが接続される VPC を設定できます。

* 設定場所は .metadata.annotaions 配下の loadbalancer.openstack.org/network-id です。
* リスナーごとの設定は適用できません。
* 設定しない場合は、クラスター作成時に設定した VPC が使用されます。

<a id="advanced-lb-configuration-set-subnet"></a>
#### サブネット設定
ロードバランサー作成時に、ロードバランサーが接続されるサブネットを設定できます。設定されたサブネットにロードバランサーのプライベート IP が接続されます。メンバーサブネットの設定がない場合、このサブネットに接続されたワーカーノードがロードバランサーのメンバーとして追加されます。

* 設定場所は .metadata.annotaions 配下の loadbalancer.openstack.org/subnet-id です。
* リスナーごとの設定は適用できません。
* 設定しない場合は、クラスター作成時に設定したサブネットが使用されます。

以下は、ロードバランサーに VPC とサブネットを設定するマニフェストの例です。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-svc-vpc-subnet
  labels:
     app: nginx
  annotations:
    loadbalancer.openstack.org/network-id: "49a5820b-d941-41e5-bfc3-0fd31f2f6773"
    loadbalancer.openstack.org/subnet-id: "38794fd7-fd2e-4f34-9c89-6dd3fd12f548"
spec:
  ports:
  - port: 8080
    targetPort: 80
    protocol: TCP
  selector:
    app: nginx
  type: LoadBalancer
```

<a id="advanced-lb-configuration-set-member-subnet"></a>
#### メンバーサブネットの設定
ロードバランサー作成時に、ロードバランサーメンバーが接続されるサブネットを設定できます。このサブネットに接続されたワーカーノードがロードバランサーメンバーとして追加されます。

* 設定箇所は .metadata.annotaions 配下の loadbalancer.nhncloud/member-subnet-id です。
* リスナーごとの設定は適用できません。
* 設定しない場合は、ロードバランサーのサブネット設定値が適用されます。
* メンバーサブネットは、必ずロードバランサーのサブネットと同じ VPC に含まれている必要があります。
* 2つ以上のメンバーサブネットを設定するには、カンマ区切りのリストで入力します。

以下は、ロードバランサーに VPC、サブネット、メンバーサブネットを設定するマニフェストの例です。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx-svc-vpc-subnet
  labels:
     app: nginx
  annotations:
    loadbalancer.openstack.org/network-id: "49a5820b-d941-41e5-bfc3-0fd31f2f6773"
    loadbalancer.openstack.org/subnet-id: "38794fd7-fd2e-4f34-9c89-6dd3fd12f548"
    loadbalancer.nhncloud/member-subnet-id: "c3548a5e-b73c-48ce-9dc4-4d4c484108bf"
spec:
  ports:
  - port: 8080
    targetPort: 80
    protocol: TCP
  selector:
    app: nginx
  type: LoadBalancer
```

> [注意]
> ロードバランサーのサブネットとメンバーサブネットを異なる設定にする場合は、ネットワーク設定に注意が必要です。以下に例を示します。
>
> **例1.**
> 
> * ロードバランサーのサブネット: サブネット#1
> * ロードバランサーのメンバーサブネット: サブネット#2
> * インスタンスのネットワークインターフェイスサブネット設定
>     * eth0: サブネット#1
>     * eth1: サブネット#2（メンバー）
> 
> この場合、インスタンスの eth1 の IP アドレスがメンバーとして登録されます。ロードバランサーから送信されたヘルスチェックパケットはインスタンスの eth1 で受信し、eth0 を通じて送信を試みます。このとき、eth0 に送信するパケットのソース IP アドレスが eth0 の IP アドレスと異なります。eth0 のネットワークインターフェイスでソース/宛先確認機能が有効になっている場合、このパケットは送信されずに破棄されます。このような構成では、eth0 のネットワークインターフェイスでソース/宛先確認機能を無効にする必要があり、それによってメンバーが正常動作します。ソース/宛先確認機能については、[ソース/宛先確認の変更](/Network/Network%20Interface/ja/console-guide/#_4)を参照してください。
> 
> **例2.**
> 
> * ロードバランサーのサブネット: サブネット#1
> * ロードバランサーのメンバーサブネット: サブネット#2
> * インスタンスのネットワークインターフェイスサブネット設定
>     * eth0: サブネット#3
>     * eth1: サブネット#2（メンバー）
> 
> この場合、インスタンスの eth1 の IP アドレスがメンバーとして登録されます。ロードバランサーから送信されたヘルスチェックパケットはインスタンスの eth1 で受信します。応答パケットをロードバランサーの VIP に送信する必要がありますが、サブネット#1 が直接接続されたネットワークではないため、ルーティングテーブルによって送信インターフェイスが決定されます。ネットワークインターフェイスのソース/宛先確認機能を設定せずに通信できるようにするには、ロードバランサーの VIP 宛てのトラフィックを eth1 を通じて送信できるようにルーティングを設定する必要があります。


> [注意]
> メンバーサブネットは、2023年11月28日以降に v1.24.3 以上のバージョンにアップグレードされた、または新規作成されたクラスターで設定できます。


<a id="advanced-lb-configuration-set-the-listener-connection-limit"></a>
#### リスナー接続制限の設定
リスナーの接続制限を設定できます。

* 設定箇所は .metadata.annotations 配下の loadbalancer.nhncloud/connection-limit です。
* リスナーごとの設定を適用できます。
* v1.17.6、v1.18.19 クラスター
    * 最小値 1、最大値 60000 です。
    * 設定しない場合は -1 に設定され、実際のロードバランサーに適用される値は 2000 です。
* v1.19.13 以降のクラスター
    * 最小値 1、最大値 60000 です。
    * 設定しない場合、または範囲外の値を入力した場合は、デフォルト値の 60000 に設定されます。


<a id="advanced-lb-configuration-set-the-listener-protocol"></a>
#### リスナープロトコル設定
リスナーのプロトコルを設定できます。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/listener-protocol です。
* リスナーごとに設定を適用できます。
* 次のいずれかに設定できます。
    * TCP: 未設定時のデフォルト値です。
    * HTTP
    * HTTPS
    * TERMINATED_HTTPS: TERMINATED_HTTPS に設定します。SSL バージョン、証明書、秘密鍵の情報を追加設定する必要があります。

> [注意]
> リスナープロトコル設定は、サービスオブジェクトを変更してもロードバランサーに適用されません。
> リスナープロトコル設定を変更するには、サービスオブジェクトを削除してから再作成する必要があります。
> この場合、ロードバランサーが削除された後に再作成されますのでご注意ください。


SSL バージョンは次のように設定できます。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/listener-terminated-https-tls-version です。
* リスナーごとに設定を適用できます。
* 次のいずれかに設定できます。
    * TLSv1.3: 未設定時のデフォルト値です。
    * TLSv1.2
    * TLSv1.1
    * TLSv1.0_2016
    * TLSv1.0
    * SSLv3

> [注意]
> TLSv1.3 は 2022 年 3 月 29 日以降に作成されたクラスターで設定可能です。

証明書情報は次のように設定できます。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/listener-terminated-https-cert です。
* リスナーごとに設定を適用できます。
* 開始行および終了行を含める必要があります。

秘密鍵情報は次のように設定できます。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/listener-terminated-https-key です。
* リスナーごとに設定を適用できます。
* 開始行および終了行を含める必要があります。

次は、リスナープロトコルを TERMINATED_HTTPS に設定する場合のマニフェスト例です。証明書情報と秘密鍵情報は一部省略されています。
```yaml
metadata:
  name: echosvr-svc
  labels:
    app: echosvr
  annotations:
    loadbalancer.nhncloud/listener-protocol: TERMINATED_HTTPS
    loadbalancer.nhncloud/listener-terminated-https-tls-version: TLSv1.2
    loadbalancer.nhncloud/listener-terminated-https-cert: |
      -----BEGIN CERTIFICATE-----
      MIIDZTCCAk0CCQDVfXIZ2uxcCTANBgkqhkiG9w0BAQUFADBvMQswCQYDVQQGEwJL
      ...
      fnsAY7JvmAUg
      -----END CERTIFICATE-----
    loadbalancer.nhncloud/listener-terminated-https-key: |
      -----BEGIN RSA PRIVATE KEY-----
      MIIEowIBAAKCAQEAz+U5VNZ8jTPs2Y4NVAdUWLhsNaNjRWQm4tqVPTxIrnY0SF8U
      ...
      u6X+8zlOYDOoS2BuG8d2brfKBLu3As5VAcAPLcJhE//3IVaZHxod
      -----END RSA PRIVATE KEY-----
```

証明書情報と秘密鍵情報を manifest に登録する代わりに、Certificate Manager に登録された証明書を使用して TERMINATED_HTTPS タイプのリスナーを作成できます。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/listener-terminated-https-cert-manager-name です。
* 設定値は Certificate Manager に登録した証明書の名前です。
* リスナーごとに設定を適用できます。

次は、リスナープロトコルを TERMINATED_HTTPS に設定する際に Certificate Manager に登録された証明書を使用するマニフェスト例です。

```yaml
metadata:
  name: echosvr-svc
  labels:
    app: echosvr
  annotations:
    loadbalancer.nhncloud/listener-protocol: TERMINATED_HTTPS
    loadbalancer.nhncloud/listener-terminated-https-tls-version: TLSv1.2
    loadbalancer.nhncloud/listener-terminated-https-cert-manager-name: test
```

> [注意]
> Certificate Manager に登録された証明書を使用する方法は、2024 年 5 月 28 日以降に作成されたか、k8s バージョンをアップグレードしたクラスターで設定可能です。
> リスナーと連携している Certificate Manager の証明書を削除すると、ロードバランサーの動作に影響を与える可能性があります。

<a id="advanced-lb-configuration-set-the-listener-proxy-protocol"></a>
#### リスナープロキシプロトコル (Proxy Protocol) 設定
リスナープロトコルが TCP または HTTPS の場合、リスナーにプロキシプロトコルを設定できます。プロキシプロトコルの詳細については、[ロードバランサープロキシモード](/Network/Load%20Balancer/ja/overview/#_4)を参照してください。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/proxy-protocol です。
* リスナーごとに設定を適用できます。
* 次のいずれかに設定できます。
    * true: プロキシプロトコルを有効にします。
    * false: プロキシプロトコルを無効にします。未設定時のデフォルト値です。

<a id="advanced-lb-configuration-set-the-load-balancing-method"></a>
#### ロードバランシング方式設定
ロードバランシング方式を設定できます。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/pool-lb-method です。
* リスナーごとに設定を適用できます。
* 次のいずれかに設定できます。
    * ROUND_ROBIN: 未設定時のデフォルト値です。
    * LEAST_CONNECTIONS
    * SOURCE_IP


<a id="advanced-lb-configuration-set-the-health-check-protocol"></a>
#### ヘルスチェックプロトコル設定
ヘルスチェックプロトコルを設定できます。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/healthmonitor-type です。
* リスナーごとに設定を適用できます。
* 次のいずれかに設定できます。
    * HTTP: HTTP URL、HTTP メソッド、HTTP ステータスコードを追加設定する必要があります。
    * HTTPS: HTTP URL、HTTP メソッド、HTTP ステータスコードを追加設定する必要があります。
    * TCP: 未設定時のデフォルト値です。

HTTP URL は次のように設定できます。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/healthmonitor-http-url です。
* リスナーごとに設定を適用できます。
* 設定値は / で始まる必要があります。
* 設定しない場合や規則に合わない値を入力した場合は、デフォルト値の / に設定されます。

HTTP メソッドは次のように設定できます。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/healthmonitor-http-method です。
* リスナーごとに設定を適用できます。
* 現在 GET のみサポートしており、設定しない場合や他の値を入力した場合はデフォルト値の GET に設定されます。

HTTP ステータスコードは次のように設定できます。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/healthmonitor-http-expected-code です。
* リスナーごとに設定を適用できます。
* 単一値 (例: 200)、リスト (例: 200,202)、範囲 (例: 200-204) の形式で入力できます。
* 設定しない場合や規則に合わない値を入力した場合は、デフォルト値の 200 に設定されます。

<a id="advanced-lb-configuration-set-the-health-check-interval"></a>
#### ヘルスチェック間隔設定
ヘルスチェックの間隔を設定できます。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/healthmonitor-delay です。
* リスナーごとに設定を適用できます。
* 秒単位で設定します。
* 最小値 1、最大値 5000 です。
* 設定しない場合や範囲外の値を入力した場合は、デフォルト値の 60 に設定されます。

<a id="advanced-lb-configuration-set-the-health-check-maximum-response-time"></a>
#### ヘルスチェック最大応答時間設定
ヘルスチェックの最大応答時間を設定できます。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/healthmonitor-timeout です。
* リスナーごとに設定を適用できます。
* 秒単位で設定します。
* 最小値 1、最大値 5000 です。
* この設定は必ずヘルスチェック間隔設定の値より小さくする必要があります。
* 設定しない場合や範囲外の値を入力した場合は、デフォルト値の 30 に設定されます。
* ただし、入力値または設定値がヘルスチェック間隔設定より大きい場合は、ヘルスチェック間隔設定の 1/2 に設定されます。

<a id="advanced-lb-configuration-set-the-maximum-number-of-retries-for-a-health-check"></a>
#### ヘルスチェック最大リトライ回数設定
ヘルスチェックの最大リトライ回数を設定できます。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/healthmonitor-max-retries です。
* リスナーごとに設定を適用できます。
* 最小値 1、最大値 10 です。
* 設定しない場合や範囲外の値を入力した場合は、デフォルト値の 3 に設定されます。

<a id="advanced-lb-configuration-health-check-port-settings"></a>
#### ヘルスチェックポート設定
ヘルスチェックの対象となるメンバーポートを設定できます。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/healthmonitor-health-check-port です。
* リスナーごとに設定を適用できます。
* 最小値 0、最大値 65535 です。
* 0 を指定した場合、各メンバーに指定されたポート番号を対象にヘルスチェックを実行します。
* 設定しない場合や範囲外の値を入力した場合は、デフォルト値の 0 に設定されます。

<a id="advanced-lb-configuration-health-check-host-header-settings"></a>
#### ヘルスチェックホストヘッダー設定
ヘルスチェックに使用するホストヘッダーのフィールド値を設定できます。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/healthmonitor-http-host-header です。
* リスナーごとに設定を適用できます。
* ヘルスチェックプロトコルを TCP に設定した場合、このフィールドに設定した値は無視されます。

<a id="advanced-lb-configuration-setting-keep-alive-timeout"></a>
#### keep-alive タイムアウト設定
keep-alive タイムアウト値を設定できます。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/keepalive-timeout です。
* リスナーごとに設定を適用できます。
* 秒単位で設定します。
* 最小値 0、最大値 3600 です。
* 設定しない場合や範囲外の値を入力した場合は、デフォルト値の 300 に設定されます。

> [注意]
> keep-alive タイムアウトは、2023 年 11 月 28 日以降に v1.24.3 以上のバージョンにアップグレードされたか、新規作成されたクラスターで設定可能です。

<a id="advanced-lb-configuration-l7-rules"></a>
#### L7 ルール
リスナーごとに L7 ルールを設定できます。L7 ルールは次のように動作します。

* L7 ルールは、リスナーのプロトコルが HTTP または TERMINATED_HTTPS の場合にのみ作成できます。
* L7 ルールは、作業タイプに応じてブロック、URL への転送、メンバーグループへの転送の順に適用されます。
* 同じ作業タイプ内では、インデックス値が小さいほど優先度が高く設定されます。
* メンバーサブネットに接続されたノードを含むメンバーグループが作成され、このメンバーグループはリスナーのデフォルトメンバーグループとして設定されます。

L7 ルールは次のように設定できます。

* 1 つのリスナーに L7 ルールを最大 10 個まで設定できます。
* 各 L7 ルールを識別するために、設定位置に `l7policy-%d`（`%d` は 0 から始まるインデックス）の形式を使用します。

| 設定位置 | 意味 | 必須 | 値 |
| --- | --- | :-: | --- |
| {LISTENER_SPEC}.{L7POLICY}.loadbalancer.nhncloud/name | 名前 | O | 255 文字以下の文字列 |
| {LISTENER_SPEC}.{L7POLICY}.loadbalancer.nhncloud/description | 説明 | X | 255 文字以下の文字列 |
| {LISTENER_SPEC}.{L7POLICY}.loadbalancer.nhncloud/action | 作業タイプ | O | REDIRECT_TO_POOL（メンバーグループへの転送）、REDIRECT_TO_URL（URL への転送）、REJECT（ブロック）のいずれか |
| {LISTENER_SPEC}.{L7POLICY}.loadbalancer.nhncloud/redirect-url | リダイレクト先 URL | X（ただし、作業タイプが REDIRECT_TO_URL の場合は必須） | `HTTP://` または `HTTPS://` で始まる URL |

> [注記]
> * {LISTENER_SPEC} は `[TCP|UDP]-%d` の形式で、`%d` はポート番号です。（例: TCP-80）
> * {L7POLICY} は `l7policy-%d` の形式で、`%d` は 0 から始まるインデックスです。（例: l7policy-0）

L7 ルール設定には次の制約事項があります。

* L7 ルール設定に使用するインデックスは 0〜9 の整数値を使用できます。
* 1 つのリスナーに設定される L7 ルールは、互いに異なるインデックス値で設定する必要があります。
* 1 つのリスナーに設定される L7 ルールは、互いに異なる名前で設定する必要があります。

<a id="advanced-lb-configuration-l7-conditions"></a>
#### L7条件
L7ルールごとにL7条件を設定できます。L7条件は次のように動作します。

* L7ルールに属するすべてのL7条件が満たされた場合に、該当L7ルールが適用されます。
* L7条件間には優先順位がありません。

L7条件は次のように設定できます。

* 1つのL7ルールにL7条件を最大10個まで設定できます。
* 各L7条件を識別するために、設定位置に `rule-%d`（`%d` は0から始まるインデックス）の形式を使用します。

| 設定位置 | 意味 | 必須 | 値 |
| --- | --- | :-: | --- |
| {LISTENER_SPEC}.{L7POLICY}.{RULE}.loadbalancer.nhncloud/type | タイプ | O | HOST_NAME（ホスト名）、PATH（パス）、FILE_TYPE（ファイルタイプ）、HEADER（ヘッダー）、COOKIE（Cookie）のいずれか |
| {LISTENER_SPEC}.{L7POLICY}.{RULE}.loadbalancer.nhncloud/compare-type | 比較方式 | O | REGEX、STARTS_WITH、ENDS_WITH、CONTAINS、EQUAL_TO のいずれか <br>（ただし、タイプが FILE_TYPE の場合は EQUAL_TO、REGEX のみ使用可能） |
| {LISTENER_SPEC}.{L7POLICY}.{RULE}.loadbalancer.nhncloud/key | キー | X（ただし、タイプが HEADER、COOKIE の場合は必須） | 255文字以下の文字列 |
| {LISTENER_SPEC}.{L7POLICY}.{RULE}.loadbalancer.nhncloud/value | 値 | O | 255文字以下の文字列 |

> [注記]
> * {RULE} は `rule-%d` の形式で、`%d` は0から始まるインデックスです。（例: rule-0）

L7条件には次の制約があります。

* L7条件の設定に使用するインデックスは0〜9の整数値を使用できます。
* 1つのL7ルールに設定するL7条件は、それぞれ異なるインデックス値で設定する必要があります。
* 1つのL7ルールに同一仕様のL7条件（タイプ、比較方式、キー、値がすべて同じ条件）は追加できません。

> [注意]
> L7ルールおよびL7条件は、2024年7月23日以降にv1.24.3以上のバージョンにアップグレードされたか、新規作成されたクラスターで設定できます。

次はL7ルールおよび条件を設定する例です。

```yaml
apiVersion: v1
kind: Service
metadata:
  name: echosvr-svc
  labels:
    app: echosvr
  annotations:
    TCP-80.loadbalancer.nhncloud/listener-protocol: "HTTP"

    TCP-80.l7policy-0.loadbalancer.nhncloud/name: "reject-policy"
    TCP-80.l7policy-0.loadbalancer.nhncloud/description: "default reject policy"
    TCP-80.l7policy-0.loadbalancer.nhncloud/action: "REJECT"

    TCP-80.l7policy-0.rule-0.loadbalancer.nhncloud/type: "PATH"
    TCP-80.l7policy-0.rule-0.loadbalancer.nhncloud/compare-type: "CONTAINS"
    TCP-80.l7policy-0.rule-0.loadbalancer.nhncloud/value: "temp"

    TCP-80.l7policy-1.loadbalancer.nhncloud/name: "redirect-policy"
    TCP-80.l7policy-1.loadbalancer.nhncloud/description: "basic redirection policy"
    TCP-80.l7policy-1.loadbalancer.nhncloud/action: "REDIRECT_TO_POOL"

    TCP-80.l7policy-1.rule-0.loadbalancer.nhncloud/type: "PATH"
    TCP-80.l7policy-1.rule-0.loadbalancer.nhncloud/compare-type: "CONTAINS"
    TCP-80.l7policy-1.rule-0.loadbalancer.nhncloud/value: "incoming"

    TCP-80.l7policy-1.rule-1.loadbalancer.nhncloud/type: "HOST_NAME"
    TCP-80.l7policy-1.rule-1.loadbalancer.nhncloud/compare-type: "STARTS_WITH"
    TCP-80.l7policy-1.rule-1.loadbalancer.nhncloud/value: "Ubuntu"

    TCP-443.loadbalancer.nhncloud/listener-protocol: "TERMINATED_HTTPS"
    TCP-443.loadbalancer.nhncloud/listener-terminated-https-tls-version: TLSv1.2
    TCP-443.loadbalancer.nhncloud/listener-terminated-https-cert-manager-name: test

    TCP-443.l7policy-0.loadbalancer.nhncloud/name: "reject-policy"
    TCP-443.l7policy-0.loadbalancer.nhncloud/description: "default reject policy"
    TCP-443.l7policy-0.loadbalancer.nhncloud/action: "REJECT"

    TCP-443.l7policy-0.rule-0.loadbalancer.nhncloud/type: "PATH"
    TCP-443.l7policy-0.rule-0.loadbalancer.nhncloud/compare-type: "CONTAINS"
    TCP-443.l7policy-0.rule-0.loadbalancer.nhncloud/value: "temp"

    TCP-443.l7policy-1.loadbalancer.nhncloud/name: "redirect-policy"
    TCP-443.l7policy-1.loadbalancer.nhncloud/description: "basic redirection policy"
    TCP-443.l7policy-1.loadbalancer.nhncloud/action: "REDIRECT_TO_POOL"

    TCP-443.l7policy-1.rule-0.loadbalancer.nhncloud/type: "PATH"
    TCP-443.l7policy-1.rule-0.loadbalancer.nhncloud/compare-type: "CONTAINS"
    TCP-443.l7policy-1.rule-0.loadbalancer.nhncloud/value: "incoming"

    TCP-443.l7policy-1.rule-1.loadbalancer.nhncloud/type: "HOST_NAME"
    TCP-443.l7policy-1.rule-1.loadbalancer.nhncloud/compare-type: "STARTS_WITH"
    TCP-443.l7policy-1.rule-1.loadbalancer.nhncloud/value: "Ubuntu"

spec:
  ports:
  - name: tcp-80
    port: 80
    targetPort: 8080
    protocol: TCP
  - name: tcp-443
    port: 443
    targetPort: 8443
    protocol: TCP
  selector:
    app: echosvr
  type: LoadBalancer
```

<a id="ingress-controller"></a>
## Ingress Controller { #ingress-controller }
インgressコントローラー（ingress controller）は、Ingress オブジェクトに定義されたルールを参照し、クラスター外部から内部サービスへの HTTP および HTTPS リクエストをルーティングし、SSL/TLS 終端、仮想ホスティングなどを提供します。インgressコントローラーおよびIngressの詳細については、[インgressコントローラー](https://kubernetes.io/ko/docs/concepts/services-networking/ingress-controllers/)、[Ingress](https://kubernetes.io/ko/docs/concepts/services-networking/ingress/) のドキュメントを参照してください。

<a id="install-nginx-ingress-controller"></a>
### NGINX Ingress Controller のインストール { #install-nginx-ingress-controller }
NGINX Ingress Controller は、広く使用されているIngressコントローラーの1つです。詳細については、[NGINX Ingress Controller](https://kubernetes.github.io/ingress-nginx/) および [NGINX Ingress Controller for Kubernetes](https://www.nginx.com/products/nginx-ingress-controller/) のドキュメントを参照してください。NGINX Ingress Controller のインストールについては、[Installation Guide](https://kubernetes.github.io/ingress-nginx/deploy/) のドキュメントを参照してください。

<a id="uri-based-service-routing"></a>
### URI 기반 서비스 분기 { #uri-based-service-routing }
Ingressコントローラーは、URIを基にサービスを分岐できます。以下の図は、URIを基にサービスを分岐する簡単な例の構造を示しています。

![ingress-01.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/ingress-01.png)

<a id="uri-based-service-routing-create-services-and-pods"></a>
#### サービスとPodの作成
次のように、サービスとPodを作成するためのマニフェストを作成します。`tea-svc` サービスには `tea` Podを接続し、`coffee-svc` サービスには `coffee` Podを接続します。

```yaml
# cafe.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: coffee
spec:
  replicas: 3
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
  replicas: 2
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
  labels:
spec:
  ports:
  - port: 80
    targetPort: 8080
    protocol: TCP
    name: http
  selector:
    app: tea
```

マニフェストを適用し、デプロイメント、サービス、Podが作成されたことを確認します。Podは **Running** 状態である必要があります。

```
$ kubectl apply -f cafe.yaml
deployment.apps/coffee created
service/coffee-svc created
deployment.apps/tea created
service/tea-svc created

# kubectl get deploy,svc,pods
NAME                     READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/coffee   3/3     3            3           27m
deployment.apps/tea      2/2     2            2           27m

NAME                 TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
service/coffee-svc   ClusterIP   10.254.171.198   <none>        80/TCP    27m
service/kubernetes   ClusterIP   10.254.0.1       <none>        443/TCP   5h51m
service/tea-svc      ClusterIP   10.254.184.190   <none>        80/TCP    27m

NAME                          READY   STATUS    RESTARTS   AGE
pod/coffee-7c86d7d67c-pr6kw   1/1     Running   0          27m
pod/coffee-7c86d7d67c-sgspn   1/1     Running   0          27m
pod/coffee-7c86d7d67c-tqtd6   1/1     Running   0          27m
pod/tea-5c457db9-fdkxl        1/1     Running   0          27m
pod/tea-5c457db9-z6hl5        1/1     Running   0          27m
```

<a id="uri-based-service-routing-create-ingress"></a>
#### Ingressの作成
リクエストパスに応じてサービスを接続するIngressマニフェストを作成します。エンドポイントが `/tea` のリクエストは `tea-svc` サービスに接続し、`/coffee` のリクエストは `coffee-svc` サービスに接続します。

```yaml
# cafe-ingress-uri.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: cafe-ingress-uri
spec:
  ingressClassName: nginx
  rules:
  - http:
      paths:
      - path: /tea
        pathType: Prefix
        backend:
          service:
            name: tea-svc
            port:
              number: 80
      - path: /coffee
        pathType: Prefix
        backend:
          service:
            name: coffee-svc
            port:
              number: 80
```

Ingressを作成し、しばらく後に確認したとき、**ADDRESS** フィールドにIPが設定されている必要があります。

```
$ kubectl apply -f cafe-ingress-uri.yaml
ingress.networking.k8s.io/cafe-ingress-uri created

$ kubectl get ingress cafe-ingress-uri
NAME               CLASS   HOSTS   ADDRESS          PORTS   AGE
cafe-ingress-uri   nginx   *       123.123.123.44   80      23s
```

<a id="uri-based-service-routing-send-http-requests"></a>
#### HTTPリクエストの送信
外部ホストからIngressの **ADDRESS** フィールドに設定されたIPアドレスにHTTPリクエストを送信し、Ingressが正しく設定されていることを確認します。

エンドポイント `/coffee` へのリクエストは `coffee-svc` サービスに転送され、`coffee` Podが応答します。応答の **Server name** 項目を確認すると、`coffee` Podがラウンドロビン方式で交互に応答していることを確認できます。

```
$ curl 123.123.123.44/coffee
Server address: 10.100.24.21:8080
Server name: coffee-7c86d7d67c-sgspn
Date: 11/Mar/2022:06:28:18 +0000
URI: /coffee
Request ID: 3811d20501dbf948259f4b209c00f2f1

$ curl 123.123.123.44/coffee
Server address: 10.100.24.19:8080
Server name: coffee-7c86d7d67c-tqtd6
Date: 11/Mar/2022:06:28:27 +0000
URI: /coffee
Request ID: ec82f6ab31d622895374df972aed1acd

$ curl 123.123.123.44/coffee
Server address: 10.100.24.20:8080
Server name: coffee-7c86d7d67c-pr6kw
Date: 11/Mar/2022:06:28:31 +0000
URI: /coffee
Request ID: fec4a6111bcc27b9cba52629e9420076
```

同様に、エンドポイント `/tea` へのリクエストは `tea-svc` サービスに転送され、`tea` Podが応答します。

```
$ curl 123.123.123.44/tea
Server address: 10.100.24.23:8080
Server name: tea-5c457db9-fdkxl
Date: 11/Mar/2022:06:28:36 +0000
URI: /tea
Request ID: 11be1b7634a371a26e6bf2d3e72ab8aa
$ curl 123.123.123.44/tea
Server address: 10.100.24.22:8080
Server name: tea-5c457db9-z6hl5
Date: 11/Mar/2022:06:28:37 +0000
URI: /tea
Request ID: 21106246517263d726931e0f85ea2887
```

定義されていないURIにリクエストを送信すると、Ingressコントローラーが `404 Not Found` を返します。

```
$ curl 123.123.123.44/unknown
<html>
<head><title>404 Not Found</title></head>
<body>
<center><h1>404 Not Found</h1></center>
<hr><center>nginx</center>
</body>
</html>
```

<a id="uri-based-service-routing-delete-resources"></a>
#### リソースの削除
テストに使用したリソースは、作成時に使用したマニフェストを利用して削除できます。

```
$ kubectl delete -f cafe-ingress-uri.yaml
ingress.networking.k8s.io "cafe-ingress-uri" deleted

$ kubectl delete -f cafe.yaml
deployment.apps "coffee" deleted
service "coffee-svc" deleted
deployment.apps "tea" deleted
service "tea-svc" deleted
```

<a id="host-based-service-routing"></a>
### ホストベースのサービス分岐 { #host-based-service-routing }
Ingressコントローラーは、ホスト名を基にサービスを分岐できます。以下の図は、ホスト名を基にサービスを分岐する簡単な例の構造を示しています。

![ingress-02.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/ingress-02.png)

<a id="host-based-service-routing-create-services-and-pods"></a>
#### サービスとPodの作成
[URIベースのサービス分岐](/Container/NKS/ja/user-guide/#uri)と同じマニフェストを使用して、サービスとPodを作成します。

<a id="host-based-service-routing-create-ingress"></a>
#### Ingressの作成
ホスト名に応じてサービスを接続するIngressマニフェストを作成します。`tea.cafe.example.com` ホストへのリクエストは `tea-svc` サービスに接続し、`coffee.cafe.example.com` ホストへのリクエストは `coffee-svc` サービスに接続します。

```yaml
# cafe-ingress-host.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: cafe-ingress-host
spec:
  ingressClassName: nginx
  rules:
  - host: tea.cafe.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: tea-svc
            port:
              number: 80
  - host: coffee.cafe.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: coffee-svc
            port:
              number: 80
```

Ingressを作成し、しばらく後に確認したとき、**ADDRESS** フィールドにIPが設定されている必要があります。

```
$ kubectl apply -f cafe-ingress-host.yaml
ingress.networking.k8s.io/cafe-ingress-host created

$ kubectl get ingress
NAME                CLASS   HOSTS                                          ADDRESS          PORTS   AGE
cafe-ingress-host   nginx   tea.cafe.example.com,coffee.cafe.example.com   123.123.123.44   80      36s
```

<a id="host-based-service-routing-send-http-requests"></a>
#### HTTP Requestの送信
外部ホストからIngressのADDRESSに設定されたIPへHTTPリクエストを送信します。ただし、ホスト名を使用してサービスを分岐するようにIngressを構成しているため、ホスト名を使用してリクエストを送信する必要があります。

> [注記]
> 任意のホスト名を使用してテストするには、curl の `--resolve` オプションを使用します。`--resolve` オプションは `{ホスト名}:{ポート番号}:{IP}` の形式で入力します。これは、{ホスト名} 宛ての {ポート番号} へのリクエストを {IP} に解決 (resolve) するという意味です。
> `/etc/host` ファイルを開き、`{IP} {ホスト名}` の形式で追加することもできます。

ホスト `coffee.cafe.example.com` へリクエストを送信すると、`coffee-svc` サービスに転送され、`coffee` Podが応答します。

```
$ curl --resolve coffee.cafe.example.com:80:123.123.123.44 http://coffee.cafe.example.com/
Server address: 10.100.24.27:8080
Server name: coffee-7c86d7d67c-fqn6n
Date: 11/Mar/2022:06:40:59 +0000
URI: /
Request ID: 1efb60d29891d6d48b5dcd9f5e1ba66d
```

ホスト `tea.cafe.example.com` へリクエストを送信すると、`tea-svc` サービスに転送され、`tea` Podが応答します。

```
$ curl --resolve tea.cafe.example.com:80:123.123.123.44 http://tea.cafe.example.com/
Server address: 10.100.24.28:8080
Server name: tea-5c457db9-ngrxq
Date: 11/Mar/2022:06:41:39 +0000
URI: /
Request ID: 5a6cc490893636029766b02d2aab9e39
```

不明なホストへリクエストを送信すると、IngressコントローラーはÂ `404 Not Found` を応答します。

```
$ curl 123.123.123.44/unknown
<html>
<head><title>404 Not Found</title></head>
<body>
<center><h1>404 Not Found</h1></center>
<hr><center>nginx</center>
</body>
</html>
```

<a id="ingress-nginx-internal-communication"></a>
### ingress-nginxコントローラーの内部通信構造と注意事項 { #ingress-nginx-internal-communication }
ingress-nginxコントローラーを通じてサービスを外部に公開する場合、リクエストを送信するクライアントの位置（クラスター内部または外部）によって、リクエストがワークロードに転送される経路が異なります。

<a id="ingress-nginx-internal-communication-cluster-external-client"></a>
#### クラスター外部クライアント
クラスター外部クライアントが送信するリクエストは、ロードバランサーを通じてIngress Controllerに転送されます。ロードバランサーはIngress Controller Serviceの外部エンドポイントとして機能し、Ingress ControllerはIngressのルールに従ってリクエストを宛先のBackend Podにルーティングします。

```
クラスター外部クライアント → ロードバランサー → ingress-nginx Service → ingress-nginx Controller Pod → Backend Pod
```

<a id="ingress-nginx-internal-communication-cluster-internal-client"></a>
#### クラスター内部クライアント
クラスター内部のPodがIngressのアドレスへリクエストを送信する場合、トラフィックはロードバランサーを経由しません。リクエストはIngress Controller ServiceのClusterIPを通じて内部経路で直接転送され、この過程でCNIに応じて次の方式でルーティングされます。

- **Calico (VXLAN)**: kube-proxyのiptablesルールベース
- **Calico (eBPF)**: BPF MAPベースのデータパスを使用

どちらの方式もトラフィックは内部ネットワーク内のみで転送され、外部ロードバランサーを経由しません。
```
内部 Pod → ingress-nginx Service (ClusterIP) → ingress-nginx Controller Pod → Backend Pod
```

<a id="ingress-nginx-internal-communication-cautions"></a>
#### 注意事項

- 内部リクエストはロードバランサーのポリシーが適用されません。ロードバランサーのTLS設定、セキュリティポリシー、ファイアウォールルールなどは内部トラフィックに影響しません。
- IngressドメインをそのままクラスターÂ 内部から呼び出す場合、ロードバランサーを経由しないため、外部とは異なるTLSまたはリダイレクトの動作が発生する可能性があります。
- 内部通信の際は、IngressドメインではなくService DNSを使用することをお勧めします。内部Pod間の通信は直接Serviceを使用し、外部公開用のエンドポイントにのみIngressを活用することが望ましいです。


<a id="k8s-dashboard"></a>
## Kubernetes 대시보드 { #k8s-dashboard }
NHN Kubernetes Service(NKS) は、デフォルトの Web UI ダッシュボード (dashboard) を提供します。Kubernetes ダッシュボードの詳細については、[Web UI (ダッシュボード)](https://kubernetes.io/ko/docs/tasks/access-application-cluster/web-ui-dashboard/) のドキュメントを参照してください。

> [注意]
> * Kubernetes ダッシュボードは NKS v1.25.4 までのみデフォルトで提供されます。
> * NKS クラスターバージョンを v1.25.4 から v1.26.3 にアップグレードしても、動作中の Kubernetes ダッシュボード Pod および関連リソースはそのまま維持されます。
> * NHN Cloud コンソールで Kubernetes リソースを照会できます。

<a id="expose-dashboard"></a>
### ダッシュボードサービスの公開 { #expose-dashboard }
ユーザーの Kubernetes には、ダッシュボードを公開するための `kubernetes-dashboard` サービスオブジェクトがあらかじめ作成されています。

```
$ kubectl get svc kubernetes-dashboard -n kube-system
NAME                   TYPE        CLUSTER-IP    EXTERNAL-IP   PORT(S)   AGE
kubernetes-dashboard   ClusterIP   10.254.85.2   <none>        443/TCP   6h

$ kubectl describe svc kubernetes-dashboard -n kube-system
Name:              kubernetes-dashboard
Namespace:         kube-system
Labels:            k8s-app=kubernetes-dashboard
Annotations:       <none>
Selector:          k8s-app=kubernetes-dashboard
Type:              ClusterIP
IP Family Policy:  SingleStack
IP Families:       IPv4
IP:                10.254.85.2
IPs:               10.254.85.2
Port:              <unset>  443/TCP
TargetPort:        8443/TCP
Endpoints:         10.100.24.7:8443
Session Affinity:  None
Events:            <none>
```

ただし、`kubernetes-dashboard` サービスオブジェクトは ClusterIP タイプであるため、まだクラスター外部に公開されていません。ダッシュボードを外部に公開するには、サービスオブジェクトを LoadBalancer タイプに変更するか、Ingress コントローラーと Ingress オブジェクトを作成する必要があります。

<a id="expose-dashboard-change-into-loadbalancer"></a>
#### LoadBalancer サービスオブジェクトへの変更

`LoadBalancer` タイプにサービスオブジェクトを変更すると、クラスター外部に NHN Cloud Load Balancer が作成され、ロードバランサーとサービスオブジェクトが接続されます。ロードバランサーと接続されたサービスオブジェクトを照会すると、**EXTERNAL-IP** フィールドにロードバランサーの IP が表示されます。`LoadBalancer` タイプのサービスオブジェクトの説明については、[LoadBalancer サービス](/Container/NKS/ja/user-guide/#loadbalancer)を参照してください。次の図は、`LoadBalancer` タイプのサービスを使用してダッシュボードを外部に公開する構造を示しています。

![dashboard-01.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/dashboard-01.png)

次のように `kubernetes-dashboard` サービスオブジェクトのタイプを `LoadBalancer` に変更します。

```
$ kubectl -n kube-system patch svc/kubernetes-dashboard -p '{"spec":{"type":"LoadBalancer"}}'
service/kubernetes-dashboard patched
```

`kubernetes-dashboard` サービスオブジェクトが `LoadBalancer` タイプに変更されると、しばらくして **EXTERNAL-IP** フィールドでロードバランサーの IP を確認できます。

```
$ kubectl get svc -n kube-system
NAME                   TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)                  AGE
...
kubernetes-dashboard   LoadBalancer   10.254.95.176   123.123.123.81   443:30963/TCP            2d23h
```

> [注記]
> 作成されたロードバランサーは **Network > Load Balancer** ページで確認できます。
> ロードバランサーの IP は外部からアクセスできるフローティング IP です。**Network > Floating IP** ページで確認できます。

Web ブラウザーで `https://{EXTERNAL-IP}` にアクセスすると、Kubernetes ダッシュボードページが読み込まれます。ログインに必要なトークンについては、[ダッシュボードアクセストークン](/Container/NKS/ja/user-guide/#dashboard-access-token)を参照してください。

> [注記]
> Kubernetes ダッシュボードは自動生成されるプライベート証明書を使用するため、Web ブラウザーの種類とセキュリティ設定によっては、安全でないページとして表示される場合があります。

<a id="expose-dashboard-open-services-with-ingress"></a>
#### Ingress を使用したサービスの公開

Ingress は、クラスター内部の複数のサービスへのアクセスを提供するルーティングを実現するネットワークオブジェクトです。Ingress オブジェクトの設定は Ingress コントローラーによって動作します。`kubernetes-dashboard` サービスオブジェクトを Ingress 経由で公開できます。Ingress および Ingress コントローラーの説明については、[Ingress コントローラー](/Container/NKS/ja/user-guide/#ingress-controller)を参照してください。次の図は、Ingress を通じてダッシュボードを外部に公開する構造を示しています。

![dashboard-02.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/dashboard-02.png)

[NGINX Ingress Controller のインストール](/Container/NKS/ja/user-guide/#nginx-ingress-controller)を参照して `NGINX Ingress Controller` をインストールし、次のように Ingress オブジェクト作成のためのマニフェストを作成します。

```yaml
# kubernetes-dashboard-ingress-tls-passthrough.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: k8s-dashboard-ingress
  namespace: kube-system
  annotations:
    ingress.kubernetes.io/ssl-passthrough: "true"
    kubernetes.io/ingress.allow-http: "false"
    nginx.ingress.kubernetes.io/backend-protocol: HTTPS
    nginx.ingress.kubernetes.io/proxy-body-size: 100M
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.org/ssl-backend: kubernetes-dashboard
spec:
  ingressClassName: nginx
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: kubernetes-dashboard
            port:
              number: 443
  tls:
  - secretName: kubernetes-dashboard-certs
```

マニフェストを適用して Ingress を作成し、Ingress オブジェクトの **ADDRESS** フィールドを確認します。

```
$ kubectl apply -f kubernetes-dashboard-ingress-tls-passthrough.yaml
ingress.networking.k8s.io/k8s-dashboard-ingress created

$ kubectl get ingress -n kube-system
NAME                    CLASS   HOSTS   ADDRESS          PORTS     AGE
k8s-dashboard-ingress   nginx   *       123.123.123.44   80, 443   34s
```

Web ブラウザーで `https://{ADDRESS}` にアクセスすると、Kubernetes ダッシュボードページが読み込まれます。ログインに必要なトークンについては、[ダッシュボードアクセストークン](/Container/NKS/ja/user-guide/#dashboard-access-token)を参照してください。

<a id="dashboard-access-token"></a>
### ダッシュボードアクセストークン { #dashboard-access-token }
Kubernetes ダッシュボードにログインするにはトークンが必要です。トークンは次のコマンドで取得できます。

```
# SECRET_NAME=$(kubectl -n kube-system get secrets | grep "kubernetes-dashboard-token" | cut -f1 -d ' ')

$ kubectl describe secret $SECRET_NAME -n kube-system | grep -E '^token' | cut -f2 -d':' | tr -d " "
eyJhbGc...-QmXA
```

出力されたトークンをブラウザーのトークン入力欄に入力すると、クラスター管理者権限を付与されたユーザーとしてログインできます。

<a id="persistent-volume"></a>
## パシステントボリューム { #persistent-volume }
パシステントボリューム (Persistent Volume、PV) は、物理ストレージデバイス (volume) を表す Kubernetes のリソースです。1 つの PV は 1 つの NHN Cloud Block Storage と接続されます。詳細については、[パシステントボリューム](https://kubernetes.io/ko/docs/concepts/storage/persistent-volumes/) のドキュメントを参照してください。

PV を Pod に接続して使用するには、パシステントボリュームクレーム (Persistent Volume Claims、PVC) オブジェクトが必要です。PVC は、容量や読み取り/書き込みモードなど、必要なボリュームの要件を定義します。

PV と PVC を使用することで、ユーザーは使用したいボリュームの属性を定義し、システムはユーザーの要件に合ったボリュームリソースを割り当てる方式でリソースの使用と管理を分離します。

<a id="pv-lifecycle"></a>
### PV/PVC のライフサイクル { #pv-lifecycle }
PV と PVC は 4 段階のライフサイクル (life cycle) に従います。

* プロビジョニング (provisioning)
[ストレージクラス](https://kubernetes.io/ko/docs/concepts/storage/storage-classes/)を使用して、ユーザーが直接ボリュームを確保して PV を作成 (static provisioning) するか、動的に作成 (dynamic provisioning) できます。

* バインディング (binding)
PV と PVC を 1:1 でバインディングします。動的プロビジョニングで PV を作成した場合、バインディングも自動的に実行されます。

* 使用 (using)
PV を Pod にマウントして使用します。

* 解放 (reclaiming)
使用が終わったボリュームを回収します。回収方法には削除 (Delete)、保持 (Retain)、再利用 (Recycle) があります。

| 方法 | 説明 |
| --- | --- |
| 削除 (Delete) | PV を削除するとき、接続されたボリュームも一緒に削除します。 |
| 保持 (Retain) | PV を削除するとき、接続されたボリュームは削除しません。ボリュームはユーザーが直接削除するか、再利用できます。 |
| 再利用 (Recycle) | PV を削除するとき、接続されたボリュームを削除せず、再利用可能な状態にします。この方法は非推奨 (deprecated) です。 |

<a id="storageclass"></a>
### StorageClass { #storageclass }
プロビジョニングを行うには、まずストレージクラスが定義されている必要があります。ストレージクラスは、特定の特性に基づいてストレージを分類する方法を提供します。ストレージ提供者（provisioner）に関する情報をはじめ、メディアの種類や Availability Zone などを設定できます。

<a id="storageclass-storage-provider-provisioner"></a>
#### ストレージ提供者（provisioner）
ストレージの提供者情報を設定します。Kubernetes のバージョンによってサポートされるストレージ提供者情報は次のとおりです。

* v1.19.13 以前のバージョン: provisioner フィールドを必ず `kubernetes.io/cinder` に設定する必要があります。
* v1.20.12 以降のバージョン: provisioner フィールドを `cinder.csi.openstack.org` に設定して使用できます。

<a id="storageclass-parameters-parameter"></a>
#### パラメータ（parameter）
ストレージクラスを通じて次のパラメータを設定できます。

* ストレージ種類（type）: ストレージの種類を入力します。（未入力の場合は General HDD が設定されます）
    * **General HDD**: ストレージ種類が HDD に設定されます。
    * **General SSD**: ストレージ種類が SSD に設定されます。
* Availability Zone（availability）: Availability Zone を設定します。（未入力の場合はランダムに設定されます）
    * 板橋リージョン: **kr-pub-a** または **kr-pub-b**
    * 平村リージョン: **kr2-pub-a** または **kr2-pub-b**
    * 光州リージョン: **kr3-pub-a** または **kr3-pub-b**

<a id="storageclass-volume-binding-mode-volumebindingmode"></a>
#### ボリュームバインディングモード（VolumeBindingMode）
ボリュームバインディングモードは、ボリュームバインディングと動的プロビジョニングの開始タイミングを制御します。この設定は、ストレージ提供者が `cinder.csi.openstack.org` の場合にのみ設定可能です。

* **Immediate**: パシステントボリュームクレームが作成されると即座にボリュームバインディングと動的プロビジョニングが開始されます。パシステントボリュームクレームが作成される時点では、ボリュームを接続する Pod に関する事前情報がない状態です。そのため、ボリュームの Availability Zone と Pod がスケジューリングされるノードの Availability Zone が異なる場合、Pod が正常に動作しないことがあります。
* **WaitForFirstConsumer**: パシステントボリュームクレームが作成された時点では、ボリュームバインディングと動的プロビジョニングは行われません。このパシステントボリュームクレームが初めて Pod に接続されると、Pod がスケジューリングされたノードの Availability Zone 情報に基づいてボリュームバインディングと動的プロビジョニングを実行します。そのため、Immediate モードで発生するような、ボリュームの Availability Zone とインスタンスの Availability Zone が異なることで Pod が正常に動作しないケースは発生しません。

<a id="storageclass-allow-volume-expansion-allowvolumeexpansion"></a>
#### ボリューム拡張の許可（allowVolumeExpansion）
作成されたボリュームの拡張を許可するかどうかを設定します（未入力の場合は false が設定されます）。

* **True**: ボリュームの拡張を許可します。
* **False**: ボリュームの拡張を許可しません。

<a id="storageclass-example-1"></a>
#### 例1
以下のストレージクラスマニフェストは、v1.19.13 以前のバージョンを使用する Kubernetes クラスターで使用できます。パラメータを通じて Availability Zone とボリュームタイプを指定できます。

```yaml
# storage_class.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: sc-ssd
provisioner: kubernetes.io/cinder
parameters:
  type: General SSD
  availability: kr-pub-a
```

ストレージクラスを作成して確認します。

```
$ kubectl apply -f storage_class.yaml
storageclass.storage.k8s.io/sc-ssd created

$ kubectl get sc
NAME     PROVISIONER            RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
sc-ssd   kubernetes.io/cinder   Delete          Immediate           false                  3s
```

<a id="storageclass-example-2"></a>
#### 例2
以下のストレージクラスマニフェストは、v1.20.12 以降のバージョンを使用する Kubernetes クラスターで使用できます。ボリュームバインディングモードを WaitForFirstConsumer に設定し、パシステントボリュームクレームが Pod に接続されたときにボリュームバインディングと動的プロビジョニングを開始します。

```yaml
# storage_class_csi.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: csi-storageclass
provisioner: cinder.csi.openstack.org
volumeBindingMode: WaitForFirstConsumer
```

ストレージクラスを作成して確認します。

```
$ kubectl apply -f storage_class_csi.yaml
storageclass.storage.k8s.io/csi-storageclass created

$ kubectl get sc
NAME               PROVISIONER                RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
csi-storageclass   cinder.csi.openstack.org   Delete          WaitForFirstConsumer   false                  7s
```

<a id="static-provisioning"></a>
### 静的プロビジョニング { #static-provisioning }

静的プロビジョニング (static provisioning) では、ユーザーが自分でブロックストレージを準備する必要があります。NHN Cloud ウェブコンソールの **Storage > Block Storage** サービスページで **[ブロックストレージ作成]** ボタンをクリックし、PV と接続するブロックストレージを作成します。ブロックストレージガイドの[ブロックストレージ作成](/Storage/Block%20Storage/ja/console-guide/#_1)を参照してください。

PV を作成するには、ブロックストレージの ID が必要です。**Storage > Block Storage** サービスページのブロックストレージ一覧から使用するブロックストレージを選択します。下部の **[情報]** タブのブロックストレージ名項目から ID を確認できます。

ブロックストレージと接続する PV マニフェストを作成します。**spec.storageClassName** にはストレージクラス名を入力します。NHN Cloud Block Storage を使用するには、**spec.accessModes** を必ず `ReadWriteOnce` に設定する必要があります。**spec.presistentVolumeReclaimPolicy** は `Delete` または `Retain` に設定できます。

v1.20.12 以降のバージョンのクラスターは、**cinder.csi.openstack.org** ストレージプロバイダーを使用する必要があります。ストレージプロバイダーを定義するには、**spec.annotations** 配下に `pv.kubernetes.io/provisioned-by: cinder.csi.openstack.org` の値を指定し、**csi** 項目配下に `driver: cinder.csi.openstack.org` の値を指定します。

> [注意]
> Kubernetes バージョンに対応したストレージプロバイダーが定義されたストレージクラスを設定する必要があります。

```yaml
# pv-static.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  annotations: 
    pv.kubernetes.io/provisioned-by: cinder.csi.openstack.org
  name: pv-static-001
spec:
  capacity:
    storage: 10Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Delete
  storageClassName: sc-default
  csi:
    driver: cinder.csi.openstack.org
    fsType: "ext3"
    volumeHandle: "e6f95191-d58b-40c3-a191-9984ce7532e5" # UUID of Block Storage
```

PV を作成して確認します。

```
$ kubectl apply -f pv-static.yaml
persistentvolume/pv-static-001 created

$ kubectl get pv -o wide
NAME            CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE   VOLUMEMODE
pv-static-001   10Gi       RWO            Delete           Available           sc-default              7s    Filesystem
```

作成した PV を使用するための PVC マニフェストを作成します。**spec.volumeName** には PV の名前を指定する必要があります。その他の項目は PV マニフェストの内容と同じ設定にします。

```yaml
# pvc-static.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-static
  namespace: default
spec:
  volumeName: pv-static-001
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
  storageClassName: sc-default
```

PVC を作成して確認します。

```
$ kubectl apply -f pvc-static.yaml
persistentvolumeclaim/pvc-static created

$ kubectl get pvc -o wide
NAME         STATUS   VOLUME          CAPACITY   ACCESS MODES   STORAGECLASS   AGE   VOLUMEMODE
pvc-static   Bound    pv-static-001   10Gi       RWO            sc-default     7s    Filesystem
```

PVC を作成した後、PV の状態を照会すると、**CLAIM** 項目に PVC 名が指定され、**STATUS** 項目が `Bound` に変更されていることを確認できます。

```
$ kubectl get pv -o wide
NAME            CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                STORAGECLASS   REASON   AGE   VOLUMEMODE
pv-static-001   10Gi       RWO            Delete           Bound    default/pvc-static   sc-default              79s   Filesystem
```

<a id="dynamic-provisioning"></a>
### Dynamic Provisioning { #dynamic-provisioning }

動的プロビジョニング (dynamic provisioning) は、ストレージクラスに定義された属性を参照して、自動的にブロックストレージを作成します。動的プロビジョニングを使用するには、ストレージクラスのボリュームバインディングモードを設定しないか、**Immediate** に設定する必要があります。

```yaml
# storage_class_csi_dynamic.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: csi-storageclass-dynamic
provisioner: cinder.csi.openstack.org
volumeBindingMode: Immediate
```

動的プロビジョニングでは PV を作成する必要はありません。そのため、PVC マニフェストには **spec.volumeName** を設定しません。

```yaml
# pvc-dynamic.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-dynamic
  namespace: default
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
  storageClassName: csi-storageclass-dynamic
```

ボリュームバインディングモードを設定しないか、**Immediate** に設定して PVC を作成すると、PV が自動的に作成されます。PV に紐付けられたブロックストレージも自動的に作成され、NHN Cloud ウェブコンソールの **Storage > Block Storage** サービスページのブロックストレージ一覧で確認できます。

```
$ kubectl apply -f pvc-dynamic.yaml
persistentvolumeclaim/pvc-dynamic created

$ kubectl get sc,pv,pvc
NAME                                                   PROVISIONER                RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
storageclass.storage.k8s.io/csi-storageclass-dynamic   cinder.csi.openstack.org   Delete          Immediate           false                  50s

NAME                                                        CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                 STORAGECLASS               REASON   AGE
persistentvolume/pvc-1056949c-bc67-45cc-abaa-1d1bd9e51467   10Gi       RWO            Delete           Bound    default/pvc-dynamic   csi-storageclass-dynamic            5s

NAME                                STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS               AGE
persistentvolumeclaim/pvc-dynamic   Bound    pvc-1056949c-bc67-45cc-abaa-1d1bd9e51467   10Gi       RWO            csi-storageclass-dynamic   9s
```

> [注意]
> 動的プロビジョニングで作成されたブロックストレージは、ウェブコンソールから削除することはできません。また、クラスターを削除する際に自動的に削除されません。そのため、クラスターを削除する前に、すべての PVC を削除する必要があります。PVC を削除せずにクラスターを削除した場合、課金される可能性があります。動的プロビジョニングで作成された PV の reclaimPolicy はデフォルトで `Delete` に設定されているため、PVC を削除するだけで PV とブロックストレージも削除されます。

<a id="pod-pvc-mount"></a>
### Pod への PVC マウント { #pod-pvc-mount }

Pod に PVC をマウントするには、Pod マニフェストにマウント情報を定義する必要があります。`spec.volumes.persistenVolumeClaim.claimName` に使用する PVC 名を入力します。また、`spec.containers.volumeMounts.mountPath` にマウントするパスを入力します。

以下の例では、静的プロビジョニングで作成した PVC を Pod の `/usr/share/nginx/html` にマウントします。

```yaml
# pod-pvc.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-with-static-pv
spec:
  containers:
    - name: web
      image: nginx
      ports:
        - name: web
          containerPort: 80
          hostPort: 8082
          protocol: TCP
      volumeMounts:
        - name: html-volume
          mountPath: "/usr/share/nginx/html"
  volumes:
    - name: html-volume
      persistentVolumeClaim:
        claimName: pvc-static
```

Pod を作成し、ブロックストレージがマウントされているかどうかを確認します。

```
$ kubectl apply -f pod-static-pvc.yaml
pod/nginx-with-static-pv created

$ kubectl get pods
NAME                   READY   STATUS    RESTARTS   AGE
nginx-with-static-pv   1/1     Running   0          50s

$ kubectl exec -ti nginx-with-static-pv -- df -h
Filesystem      Size  Used Avail Use% Mounted on
...
/dev/vdc        9.8G   23M  9.7G   1% /usr/share/nginx/html
...
```

NHN Cloud ウェブコンソールの **Storage > Block Storage** サービスページでも、ブロックストレージの接続情報を確認できます。

<a id="volume-expansion"></a>
### Volume Expansion { #volume-expansion }
PersistentVolumeClaim (PVC) オブジェクトを編集して、既存のボリュームのサイズを変更できます。PVC オブジェクトの **spec.resources.requests.storage** 項目を変更することで、ボリュームサイズを変更できます。ボリュームの縮小はサポートされていません。ボリューム拡張機能を使用するには、StorageClass の **allowVolumeExpansion** 属性が **True** である必要があります。


<a id="volume-expansion-from-v11913-and-older"></a>
#### v1.19.13 以前のバージョンのボリューム拡張
v1.19.13 以前のバージョンのストレージプロバイダー **kubernetes.io/cinder** は、使用中のボリュームの拡張機能を提供しません。使用中のボリュームの拡張機能を使用するには、v1.20.12 以降のバージョンの **cinder.csi.openstack.org** ストレージプロバイダーを使用する必要があります。クラスターのアップグレード機能を使用して v1.20.12 以降のバージョンにアップグレードすることで、**cinder.csi.openstack.org** ストレージプロバイダーを使用できます。

v1.19.13 以前のバージョンの **kubernetes.io/cinder** ストレージプロバイダーの代わりに v1.20.12 以降のバージョンの **cinder.csi.openstack.org** ストレージプロバイダーを使用するには、PVC のアノテーションを次のように変更する必要があります。

* pv.kubernetes.io/bind-completed: "yes" > 削除
* pv.kubernetes.io/bound-by-controller: "yes" > 削除
* volume.beta.kubernetes.io/storage-provisioner: kubernetes.io/cinder > volume.beta.kubernetes.io/storage-provisioner:cinder.csi.openstack.org
* volume.kubernetes.io/storage-resizer: kubernetes.io/cinder > volume.kubernetes.io/storage-resizer: cinder.csi.openstack.org
* pv.kubernetes.io/provisioned-by:cinder.csi.openstack.org > 追加


以下は変更後の PVC の例です。

``` yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  annotations:
    pv.kubernetes.io/provisioned-by: cinder.csi.openstack.org
    volume.beta.kubernetes.io/storage-provisioner: cinder.csi.openstack.org
    volume.kubernetes.io/storage-resizer: cinder.csi.openstack.org
  creationTimestamp: "2022-07-18T06:13:01Z"
  finalizers:
  - kubernetes.io/pvc-protection
  labels:
    app: nginx
  name: www-web-0
  namespace: default
spec:
  accessModes:
  - ReadWriteOnce
  resources:
    requests:
      storage: 310Gi
  storageClassName: sc-ssd
  volumeMode: Filesystem
  volumeName: pvc-0da7cd55-bf29-4597-ab84-2f3d46391e5b
status:
  accessModes:
  - ReadWriteOnce
  capacity:
    storage: 300Gi
  phase: Bound
```

<a id="volume-expansion-from-v12012-and-older"></a>
#### v1.20.12 以降のバージョンのボリューム拡張
v1.20.12 以降のバージョンのストレージプロバイダー **cinder.csi.openstack.org** は、デフォルトで使用中のボリュームの拡張機能をサポートしています。PVC オブジェクトの **spec.resources.requests.storage** 項目を任意の値に変更することで、ボリュームサイズを変更できます。

<a id="service-integration"></a>
## NHN Cloud サービス連携 { #service-integration }

<a id="ncr-integration"></a>
### NHN Cloud Container Registry(NCR) サービス連携 { #ncr-integration }
NHN Cloud Container Registry に保存したイメージを使用できます。レジストリに保存されたイメージを使用するには、ユーザーレジストリにログインするためのシークレット (secret) を作成する必要があります。

NHN Cloud (Old) Container Registry を使用する場合は、次のようにシークレットを作成する必要があります。

```
$ kubectl create secret docker-registry registry-credential --docker-server={ユーザーレジストリアドレス} --docker-username={NHN Cloudアカウントのメールアドレス} --docker-password={サービスAppkeyまたは統合Appkey}
secret/registry-credential created

$ kubectl get secrets
NAME                  TYPE                             DATA   AGE
registry-credential   kubernetes.io/dockerconfigjson   1      30m
```


NHN Cloud Container Registry を使用する場合は、次のようにシークレットを作成する必要があります。

```
$ kubectl create secret docker-registry registry-credential --docker-server={ユーザーレジストリアドレス} --docker-username={User Access Key ID} --docker-password={Secret Access Key}
secret/registry-credential created

$ kubectl get secrets
NAME                  TYPE                             DATA   AGE
registry-credential   kubernetes.io/dockerconfigjson   1      30m
```


デプロイメントのマニフェストファイルにシークレット情報を追加し、イメージ名を変更することで、ユーザーレジストリに保存されたイメージを使用して Pod を作成できます。

```yaml
# nginx.yaml
...
spec:
  ...
  template:
    ...
    spec:
      containers:
      - name: nginx
        image: {ユーザーレジストリアドレス}/nginx:1.14.2
        ...
      imagePullSecrets:
      - name: registry-credential
```

> [注記]
> NHN Cloud Container Registry の使用方法については、[NHN Cloud Container Registry(NCR) ユーザーガイド](/Container/NCR/ja/user-guide) を参照してください。

<a id="nas-integration"></a>
### NHN Cloud NAS サービス連携 { #nas-integration }
NHN Cloud が提供する NAS ボリュームを PV として活用できます。NAS サービスを使用するには、v1.20 以降のバージョンのクラスターを使用する必要があります。NHN Cloud NAS の使用に関する詳細については、[NAS コンソール使用ガイド](/Storage/NAS%20(online)/ja/console-guide) を参照してください。

> [注記]
> NHN Cloud NAS サービスは、現時点 (2024年8月) において一部のリージョンでのみ提供されています。NHN Cloud NAS サービスのサポートリージョンに関する詳細については、[NAS サービス概要](/Storage/NAS%20(online)/ja/overview) を参照してください。

<a id="nas-integration-run-the-rpcbind-service-on-all-worker-nodes"></a>
#### すべてのワーカーノードで rpcbind サービスを実行
NAS ボリュームを使用するには、すべてのワーカーノードで rpcbind サービスを実行する必要があります。すべてのワーカーノードに接続した後、次のコマンドで rpcbind サービスを実行します。

rpcbind サービスの実行コマンドは、イメージの種類に関係なく同じです。

```
$ systemctl start rpcbind
```

強化されたセキュリティルールを使用しているクラスターの場合、セキュリティルールの追加が必要です。

| 方向 | IP プロトコル | ポート範囲 | Ether | リモート | 説明 |
| :-: | :-: | :-: | :-: | :-: | :-: |
| egress | TCP | 2049 | IPv4 | NAS IP アドレス | rpc の NFS ポート、方向: csi-nfs-node (ワーカーノード) → NAS |
| egress | TCP | 111 | IPv4 | NAS IP アドレス | rpc の portmapper ポート、方向: csi-nfs-node (ワーカーノード) → NAS |
| egress | TCP | 635 | IPv4 | NAS IP アドレス | rpc の mountd ポート、方向: csi-nfs-node (ワーカーノード) → NAS |

<a id="nas-integration-install-csi-driver-nfs"></a>
#### csi-driver-nfs のインストール
NHN Cloud NAS サービスを使用するために、クラスターに NHN Kubernetes Service (NKS) の Addon 機能として [nfs-csi-plugin](/Container/NKS/ja/user-guide/#addon-mgmt-addon-nfs-csi-plugin) をデプロイする必要があります。

csi-driver-nfs は、NFS ストレージに新しいサブディレクトリを作成する方式で動作する NFS ストレージプロビジョニングをサポートするドライバーです。
csi-driver-nfs は、ストレージクラスに NFS ストレージ情報を提供する方式で動作し、ユーザーが管理する対象を減らします。

csi-driver-nfs を使用して複数の PV を構成する場合、csi-driver-nfs が NFS ストレージ情報を StorageClass に登録するため、NFS-Provisioner Pod を構成する必要はありません。
<br>
![nfs-csi-driver-02.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/nfs-csi-driver-02.png)

<a id="nas-integration-how-to-use-existing-nhn-cloud-nas-volume-when-provisioning"></a>
#### プロビジョニング時に既存の NHN Cloud NAS ボリュームを使用する方法
PV マニフェスト作成時に NAS 情報を入力するか、StorageClass マニフェストに NAS 情報を入力することで、既存の NAS ボリュームを PV として使用できます。

##### 方法 1. PV マニフェスト作成時に NAS ボリューム情報を定義する
PV マニフェスト作成時に NHN Cloud NAS ボリューム情報を定義します。設定箇所は .spec 配下の **csi** です。

* driver: **nfs.csi.k8s.io** を入力します。
* readOnly: **false** を入力します。
* volumeHandle: クラスター内で重複しない一意の id を入力します。
* volumeAttributes: NAS ボリュームの接続情報を入力します。
  * server: NAS ボリュームの接続情報のうち、**ip** 部分の値を入力します。
  * share: NAS ボリュームの接続情報のうち、**ボリューム名**部分の値を入力します。

以下はマニフェストの例です。
``` yaml
# pv.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-onas
spec:
  capacity:
    storage: 300Gi
  accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Retain
  csi:
    driver: nfs.csi.k8s.io
    readOnly: false
    volumeHandle: unique-volumeid
    volumeAttributes:
      server: 192.168.0.98
      share: /onas_300gb
```

PV を作成し、確認します。
```
$ kubectl apply -f pv.yaml
persistentvolume/pv-onas created

$ kubectl get pv -o wide
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM                      STORAGECLASS   REASON   AGE    VOLUMEMODE
pv-onas                                    300Gi      RWX            Retain           Available                                                      101s   Filesystem
```

作成した PV を使用するための PVC マニフェストを作成します。**spec.volumeName** には PV の名前を指定する必要があります。その他の項目は PV マニフェストの内容と同じ設定にします。
```yaml
# pvc.yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-onas
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 300Gi
  volumeName: pv-onas
```

PVC を作成し、確認します。
```
$ kubectl apply -f pvc.yaml
persistentvolumeclaim/pvc-onas created

$ kubectl get pvc -o wide
NAME              STATUS   VOLUME    CAPACITY   ACCESS MODES   STORAGECLASS   AGE    VOLUMEMODE
pvc-onas   Bound    pv-onas   300Gi      RWX                           2m8s   Filesystem
```

PVC を作成した後に PV のステータスを照会すると、**CLAIM** 項目に PVC 名が指定され、STATUS 項目が `Bound` に変更されたことを確認できます。
```
$ kubectl get pv -o wide
NAME      CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                     STORAGECLASS   REASON   AGE     VOLUMEMODE
pv-onas   300Gi      RWX            Retain           Bound    default/pvc-onas                           3m20s   Filesystem
```

##### 方法 2. StorageClass マニフェスト作成時に NAS 情報を定義する
StorageClass マニフェスト作成時に、ストレージプロバイダー情報および NHN Cloud NAS ボリューム情報を定義します。

* provisioner: **nfs.csi.k8s.io** を入力します。
* parameters: 入力項目については以下の表を参照してください。

| 項目 | 説明 | 例 | 必須 | デフォルト値 |
| ------- |------- | --------------------------- | ---------------------------- | ------------- |
| server | NAS ボリュームの接続情報のうち **ip** を表します。 | 192.168.0.81 | O |  |
| share | NAS ボリュームの接続情報のうち**ボリューム名**を表します。 | /onas_300gb | O |  |
| mountPermissions | NAS ボリュームのマウントポイントディレクトリに設定する権限を指定します。 | "0700" | X | 0741 |
| uid | NAS ボリュームのマウントポイントディレクトリに設定する UID を入力します。 | 1000 | X | root(0) |
| gid | NAS ボリュームのマウントポイントディレクトリに設定する GID を入力します。 | 1000 | X | root(0) |

以下はマニフェストの例です。
``` yaml
# storageclass.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: onas-sc
provisioner: nfs.csi.k8s.io
parameters:
  server: 192.168.0.81
  share: /onas_300gb
  mountPermissions: "0700"
  uid: 1000
  gid: 1000
reclaimPolicy: Retain
volumeBindingMode: Immediate
```

StorageClass を作成し、確認します。
```
$ kubectl apply -f storageclass.yaml
storageclass.storage.k8s.io/onas-sc created

$ kubectl get sc
NAME      PROVISIONER      RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
onas-sc   nfs.csi.k8s.io   Retain          Immediate           false                  3s
```

PV を別途作成する必要がないため、PVC マニフェストのみ作成します。PVC マニフェストには **spec.volumeName** を設定しません。
```yaml
# pvc.yaml
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: pvc-onas-dynamic
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 300Gi
  storageClassName: onas-sc
```
ボリュームバインディングモードを設定しない場合、または Immediate に設定して PVC を作成すると、PV が自動的に作成されます。

```
$ kubectl apply -f pvc.yaml
persistentvolumeclaim/pvc-onas created

$ kubectl get sc,pv,pvc
NAME                                  PROVISIONER      RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
storageclass.storage.k8s.io/onas-sc   nfs.csi.k8s.io   Retain          Immediate           false                  25s

NAME                                                        CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                      STORAGECLASS   REASON   AGE
persistentvolume/pvc-71392e58-5d8e-43b2-9798-5b59de34b203   300Gi      RWX            Retain           Bound    default/pvc-onas   onas-sc                 3s

NAME                                     STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
persistentvolumeclaim/pvc-onas   Bound    pvc-71392e58-5d8e-43b2-9798-5b59de34b203   300Gi      RWX            onas-sc        4s
```

Pod に PVC をマウントするには、Pod マニフェストにマウント情報を定義する必要があります。**spec.volumes.persistenVolumeClaim.claimName** に使用する PVC 名を入力します。また、**spec.containers.volumeMounts.mountPath** にマウント先のパスを入力します。

以下は、作成した PVC を Pod の `/tmp/nfs` にマウントするマニフェストの例です。
```yaml
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: nginx
  namespace: default
spec:
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx
        volumeMounts:
          - name: onas-dynamic
            mountPath: "/tmp/nfs"
      volumes:
        - name: onas-dynamic
          persistentVolumeClaim:
            claimName: pvc-onas-dynamic
```

Pod を作成し、NAS ボリュームがマウントされていることを確認します。
```
$ kubectl apply -f deployment.yaml
deployment.apps/nginx created

$ kubectl get pods
NAME                             READY   STATUS    RESTARTS   AGE
nginx-5fbc846574-q28cf   1/1     Running   0          26s

$ kubectl exec -it nginx-5fbc846574-q28cf -- df -h
Filesystem                                                                 Size  Used Avail Use% Mounted on
...
192.168.0.45:/onas_300gb/pvc-71392e58-5d8e-43b2-9798-5b59de34b203  270G  256K  270G   1% /tmp/nfs
...
```

<a id="nas-integration-how-to-create-new-nhn-cloud-nas-volume-when-provisioning"></a>
#### プロビジョニング時に新しい NHN Cloud NAS ボリュームを作成する方法
StorageClass および PVC マニフェストの作成時に NAS 情報を入力することで、自動的に作成された NAS ボリュームを PV として使用できます。

StorageClass マニフェストに、ストレージプロバイダー情報および作成する NAS ボリュームのスナップショットポリシー、アクセス制御リスト (ACL)、サブネット情報を定義します。

* provisioner: **nfs.csi.k8s.io** を入力します。
* parameters: 入力項目については下記の表を参照してください。パラメータ値に複数の値を定義する場合は、**,** を使用して値を区切ります。

| 項目 | 説明 | 例 | 複数値 | 必須 | デフォルト値 |
| ------- |------- | --------------------------- | ---------------------------- | --------- | ------------- |
| maxscheduledcount | 保存可能なスナップショットの最大数です。最大数に達すると、自動作成されたスナップショットのうち最も古いものが削除されます。1〜20 の数値のみ入力可能です。 | "7" | X | X |  |
| reservepercent | 保存可能なスナップショットの最大容量です。スナップショット容量の合計が設定サイズを超えた場合、すべてのスナップショットのうち最も古いものが削除されます。0〜80 の数値のみ入力可能です。 | "80" | X | X |  |
| scheduletime | スナップショットが作成される時刻です。 | "09:00" | X | X |  |
| scheduletimeoffset | スナップショット作成時刻のオフセットです。UTC 基準で、KST で使用する場合は +09:00 を指定します。 | "+09:00" | X | X |  |
| scheduleweekdays | スナップショットの作成サイクルです。日曜日から土曜日をそれぞれ数値 0〜6 で表します。 | "6" | O | X |  |
| subnet | ストレージにアクセスするサブネットです。選択した VPC のサブネットのみ選択できます。 | "59526f1c-c089-4517-86fd-2d3dac369210" | X | O |  |
| acl | 読み取り・書き込み権限を許可する IP または IP 帯域のリストです。 | "0.0.0.0/0" | O | X | 0.0.0.0/0 |
| onDelete | PVC 削除時に NAS ボリュームを削除するかどうかです。 | "delete" / "retain" | X | X | delete |
| mountPermissions | NAS ボリュームのマウントポイントディレクトリに設定する権限を指定します。 | "0700" | X | X | 0741 |
| uid | NAS ボリュームのマウントポイントディレクトリに設定する UID を入力します。 | 1000 | X | X | root(0) |
| gid | NAS ボリュームのマウントポイントディレクトリに設定する GID を入力します。 | 1000 | X | X | root(0) |

> [注記]
> スナップショットパラメータを使用する場合は、関連するすべてのパラメータ値を定義する必要があります。スナップショット関連パラメータは以下のとおりです。
> + maxscheduledcount
> + reservepercent
> + scheduletime
> + scheduletimeoffset
> + scheduleweekdays

<br>

> [注意] 複数サブネット環境における制約事項
>
> NAS ボリュームは、StorageClass に定義されたサブネットに接続されます。
> Pod が NAS ボリュームと連携するには、すべてのワーカーノードグループがこのサブネットに接続されている必要があります。

以下はマニフェストの例です。
```yaml
# storage_class.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: sc-nfs
provisioner: nfs.csi.k8s.io
reclaimPolicy: Delete
volumeBindingMode: Immediate
parameters:
  maxscheduledcount : "7"
  reservepercent : "80"
  scheduletime : "09:00"
  scheduletimeoffset : "+09:00"
  scheduleweekdays : "6"
  subnet : "59526f1c-c089-4517-86fd-2d3dac369210"
  acl : ""
  mountPermissions: "0700"
  uid: 1000
  gid: 1000
```

PVC マニフェストの **Annotation** に、作成する NAS ボリュームの名前、説明、サイズを定義します。入力項目については下記の表を参照してください。

| 項目 | 説明 | 例 | 必須 |
| ---- | ------- | --------------------------- | --------- |
| nfs-volume-name | 作成されるストレージの名前です。ストレージ名を使用して NFS アクセスパスを作成します。名前は 100 文字以内の英字、数字、および一部の記号 ('-'、'_') のみ入力できます。 | "nas_sample_volume_300gb" | O |
| nfs-volume-description | 作成する NAS ボリュームの説明です。 | "nas sample volume" | X |
| nfs-volume-sizegb | 作成する NAS ボリュームのサイズです。GB 単位で設定されます。最小 300 から最大 10,000 まで入力できます。 | "300" | O |

以下はマニフェストの例です。
```yaml
# pvc.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-nfs
  annotations:
    nfs-volume-name: "nas_sample_volume_300gb"
    nfs-volume-description: "nas sample volume"
    nfs-volume-sizegb: "300"
spec:
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 50Gi
  storageClassName: sc-nfs
```

StorageClass および PVC を作成して確認します。
```
$ kubectl apply -f storage_class.yaml
storageclass.storage.k8s.io/sc-nfs created

$ kubectl get sc
NAME         PROVISIONER      RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
sc-nfs       nfs.csi.k8s.io   Delete          Immediate           false                  50s
```

PV を別途作成する必要がないため、PVC マニフェストのみ作成します。PVC マニフェストには **spec.volumeName** を設定しません。
ボリュームバインディングモードを設定しないか、Immediate に設定して PVC を作成すると、PV が自動的に作成されます。NAS ボリュームが作成されてから Bound 状態になるまで、約 1 分程度かかります。
NHN Cloud コンソールの **Storage > NAS** サービスページでも、作成された NAS ボリュームの情報を確認できます。

```
$ kubectl apply -f pvc.yaml
persistentvolumeclaim/pvc-nfs created

$ kubectl get pv,pvc
NAME                                                        CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM             STORAGECLASS   REASON   AGE
persistentvolume/pvc-a8ea2054-0849-4fe8-8207-ee0e43b8a103   50Gi       RWX            Delete           Bound    default/pvc-nfs   sc-nfs                  2s

NAME                            STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
persistentvolumeclaim/pvc-nfs   Bound    pvc-a8ea2054-0849-4fe8-8207-ee0e43b8a103   50Gi       RWX            sc-nfs         75s
```

Pod に PVC をマウントするには、Pod マニフェストにマウント情報を定義する必要があります。**spec.volumes.persistenVolumeClaim.claimName** に使用する PVC 名を入力します。また、**spec.containers.volumeMounts.mountPath** にマウントするパスを入力します。

以下は、作成した PVC を Pod の `/tmp/nfs` にマウントするマニフェストの例です。
```yaml
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: nginx
  namespace: default
spec:
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx
        volumeMounts:
          - name: nas
            mountPath: "/tmp/nfs"
      volumes:
        - name: nas
          persistentVolumeClaim:
            claimName: pvc-nfs
```

Pod を作成し、NAS ボリュームがマウントされているか確認します。
```
$ kubectl apply -f deployment.yaml
deployment.apps/nginx created

$ kubectl get pods
NAME                             READY   STATUS    RESTARTS   AGE
nginx-9f448b9f7-xw92w   1/1     Running   0          12s

$ kubectl exec -it nginx-9f448b9f7-xw92w -- df -h
Filesystem                                                                     Size  Used Avail Use% Mounted on
overlay                                                                         20G   16G  4.2G  80% /
tmpfs                                                                           64M     0   64M   0% /dev
tmpfs                                                                          1.9G     0  1.9G   0% /sys/fs/cgroup
192.168.0.57:nas_sample_volume_100gb/pvc-a8ea2054-0849-4fe8-8207-ee0e43b8a103   20G  256K   20G   1% /tmp/nfs
...
```

> [注記]
> csi-driver-nfs は、プロビジョニング時に NFS ストレージ内部に subdirectory を作成する方式で動作します。
> Pod に PV をマウントする際、subdirectory のみがマウントされるのではなく、NFS ストレージ全体がマウントされるため、アプリケーションがプロビジョニングされたサイズ分だけボリュームを使用するよう強制することはできません。

<a id="encrypted-block-storage-integration"></a>
### NHN Cloud 暗号化ブロックストレージ連動 { #encrypted-block-storage-integration }
NHN Cloud が提供する暗号化されたブロックストレージを PV として活用できます。NHN Cloud 暗号化ブロックストレージの詳細については、[暗号化ブロックストレージ](/Storage/Block%20Storage/ja/console-guide/#_2)を参照してください。

> [注記]
> 暗号化ブロックストレージサービス連動機能は、v1.24.3 以上のバージョンのクラスターで使用できます。
> 2023年11月28日以降に新規作成されたクラスターは、デフォルトで暗号化ブロックストレージ連動機能が内蔵されています。
> 2023年11月28日以前に作成されたクラスターは、v1.24.3 以上のバージョンにアップグレードするか、csi-cinder-controllerplugin ステートフルセットと csi-cinder-nodeplugin デーモンセットの cinder-csi-plugin イメージを最新バージョンに置き換えることで、暗号化ブロックストレージ連動機能を使用できます。

> [注意]
> v1.24.3 より前のバージョンのクラスターをアップグレードせずに cinder-csi-plugin コンテナイメージのみを置き換えて使用する場合、誤動作が発生する可能性があります。

<a id="encrypted-block-storage-integration-updating-cinder-csi-plugin-image-for-encrypted-block-storage-integration"></a>
#### 暗号化ブロックストレージ連動のための cinder-csi-plugin イメージ更新
以下のコマンドを実行して、現在クラスターにデプロイされている cinder-csi-plugin イメージのタグを確認できます。

```
$ kubectl -n kube-system get statefulset csi-cinder-controllerplugin -o=jsonpath="{$.spec.template.spec.containers[?(@.name=='cinder-csi-plugin')].image}"

> registry.k8s.io/provider-os/cinder-csi-plugin:v1.27.101
```

cinder-csi-plugin イメージのタグが v1.27.101 以上の場合、特別な対応なしに暗号化ブロックストレージを連動できます。
cinder-csi-plugin イメージのタグが v1.27.101 未満の場合、以下の手順で cinder-csi-plugin のイメージを更新した後、暗号化ブロックストレージを連動できます。

| リージョン | インターネット接続 | cinder-csi-plugin イメージ |
| --- | --- | --- |
| 韓国(板橋) リージョン | O | dfe965c3-kr1-registry.container.nhncloud.com/container_service/cinder-csi-plugin:v1.27.101 |
| | X | private-dfe965c3-kr1-registry.container.nhncloud.com/container_service/cinder-csi-plugin:v1.27.101 |
| 韓国(坪村) リージョン | O | 6e7f43c6-kr2-registry.container.cloud.toast.com/container_service/cinder-csi-plugin:v1.27.101 |
| | X | private-6e7f43c6-kr2-registry.container.cloud.toast.com/container_service/cinder-csi-plugin:v1.27.101 |
| 韓国(光州) リージョン | O | d6628457-kr3-registry.container.nhncloud.com/container_service/cinder-csi-plugin:v1.27.101 |
| | X | private-d6628457-kr3-registry.container.nhncloud.com/container_service/cinder-csi-plugin:v1.27.101 |

##### 1. container_image に正しい cinder-csi-plugin イメージの値を入力します。
```
$ container_image={cinder-csi-pluginイメージ}
```

##### 2. コンテナイメージを置き換えます。
```
$ kubectl -n kube-system patch statefulset csi-cinder-controllerplugin -p "{\"spec\": {\"template\": {\"spec\": {\"containers\": [{\"name\": \"cinder-csi-plugin\", \"image\": \"${container_image}\"}]}}}}"

$ kubectl -n kube-system patch daemonset csi-cinder-nodeplugin -p "{\"spec\": {\"template\": {\"spec\": {\"containers\": [{\"name\": \"cinder-csi-plugin\", \"image\": \"${container_image}\"}]}}}}"
```

> [注記]
> cinder-csi-plugin コンテナイメージは NHN Cloud NCR で管理されています。閉域網環境に構成されたクラスターはインターネットに接続されていないため、イメージを正常に取得するには Private URI を使用するための環境構成が必要です。Private URI の使用方法の詳細については、[NHN Cloud Container Registry(NCR) ユーザーガイド](/Container/NCR/ja/user-guide/#private-uri)を参照してください。


<a id="encrypted-block-storage-integration-static-provisioning"></a>
#### 静的プロビジョニング
PV を作成するには、暗号化ブロックストレージの ID が必要です。Storage > Block Storage サービスページのブロックストレージ一覧から使用するブロックストレージを選択します。下部の情報タブのブロックストレージ名の項目で ID を確認できます。

PV マニフェストの作成時に暗号化ブロックストレージの情報を入力します。設定箇所は **.spec.csi** の下です。

* driver: `cinder.csi.openstack.org` を入力します。
* fsType: `ext3` を入力します。
* volumeHandle: 作成した暗号化ブロックストレージの ID を入力します。

以下はマニフェストの例です。
```yaml
# pv-static.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  annotations:
    pv.kubernetes.io/provisioned-by: cinder.csi.openstack.org
  name: pv-static-encrypted-hdd
spec:
  capacity:
    storage: 10Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Delete
  csi:
    driver: cinder.csi.openstack.org
    fsType: ext3
    volumeHandle: 9f606b78-256b-4f74-8988-1331cd6d398b
```

PVC マニフェストの作成および Pod へのマウント手順は、通常のブロックストレージの静的プロビジョニングと同様です。詳細については、[静的プロビジョニング](/Container/NKS/ja/user-guide/#static-provisioning)を参照してください。

<a id="encrypted-block-storage-integration-dynamic-provisioning"></a>
#### 動的プロビジョニング
ストレージクラスのマニフェスト作成時に暗号化ブロックストレージの作成に必要な情報を入力することで、自動的に作成された暗号化ブロックストレージを PV として使用できます。

ストレージクラスのマニフェストに暗号化ブロックストレージの作成に必要な情報を入力します。設定箇所は **.parameters** の下です。

* ストレージ種類(type): ストレージの種類を入力します。
    * **Encrypted HDD**: ストレージ種類が暗号化された HDD に設定されます。
    * **Encrypted SSD**: ストレージ種類が暗号化された SSD に設定されます。
* 暗号化キー ID(volume_key_id): Secure Key Manager(SKM) サービスで作成した対称キーの ID を入力します。
* 暗号化アプリキー(volume_appkey): Secure Key Manager(SKM) サービスで確認した Appkey を入力します。

以下はマニフェストの例です。
```yaml
# storage_class.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: csi-storageclass-encrypted-hdd
provisioner: cinder.csi.openstack.org
volumeBindingMode: Immediate
allowVolumeExpansion: true
parameters:
  type: Encrypted HDD
  volume_key_id: "5530..."
  volume_appkey: "uaUW..."
```

PVC マニフェストの作成および Pod へのマウント手順は、通常のブロックストレージの動的プロビジョニングと同様です。詳細については、[動的プロビジョニング](/Container/NKS/ja/user-guide/#dynamic-provisioning)を参照してください。


<a id="etcd-encryption-with-skm"></a>
### 機密データの暗号化・復号化時の Secure Key Manager サービス連動 { #etcd-encryption-with-skm }

NKS クラスターは secret リソースをデータストア(etcd)に保存する際、データを暗号化して保存します。NKS はこのデータを暗号化するために 2 つの方式を提供します。

<a id="etcd-encryption-with-skm-standard"></a>
#### 基本方式

* クラスター作成時に対称キーを自動生成してコントロールプレーンに保存
* そのキーで etcd データを暗号化
* キー管理がクラスター内部で行われる

<a id="etcd-encryption-with-skm-skm-integration"></a>
#### SKM 連動方式

* ストレージ暗号化プロバイダーを Secure Key Manager(SKM) に設定
* etcd データの暗号化・復号化時に SKM API を通じて暗号化・復号化処理を実行
* 一元化されたキー管理と監査ログの記録が可能

> [注意]
> クラスターに連動している SKM 対称キーまたはローテーションされたキーバージョンを削除すると、クラスターが正常に動作しなくなります。
> * etcd データの復号化不可によりクラスターの起動が失敗
> * 暗号化されたリソースへのアクセス不可
> * 復旧不可能なデータ損失が発生する可能性があります

> 注記: 安全にローテーションされたキーバージョンを削除する方法
> すべての secret リソースデータを読み取り、新しいキーで再書き込みするよう強制すると、最新バージョンのキーでデータが再暗号化されます。
> 以下のコマンドですべての secret リソースデータを再暗号化した後、ローテーションされたキーバージョンを安全に削除できます。
> `kubectl get secrets --all-namespaces -o json | kubectl replace -f -`
