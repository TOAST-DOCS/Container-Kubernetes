## Container > NHN Kubernetes Service(NKS) > 使用ガイド

<a id="cluster-headings"></a>

## クラスター
クラスターは、ユーザーの Kubernetes を構成するインスタンスのグループです。

<a id="cluster-create"></a>
### クラスター作成
NHN Kubernetes Service(NKS) を使用するには、まずクラスターを作成する必要があります。

> [注意] クラスター使用のための権限設定<br>
> クラスターを作成するユーザーは、対象プロジェクトに対して基本インフラサービスの **Infrastructure ADMIN** または **Infrastructure LoadBalancer ADMIN** または **Infrastructure NKS ADMIN** 権限を持っている必要があります。
> この権限がある場合にのみ、基本インフラサービスを基盤とするクラスターを正常に作成・活用できます。そのうちの1つの権限を持った状態で別の権限が追加されても、使用上の問題はありません。
> 権限設定については、[プロジェクトメンバー管理](/nhncloud/ja/console-user-guide/#_3)を参照してください。

**Container > NHN Kubernetes Service(NKS)** ページで **[クラスター作成]** をクリックすると、クラスター作成ページが表示されます。クラスター作成に必要な項目は次のとおりです。

| 項目 | 説明 |
| --- | --- |
| クラスター名 | Kubernetes クラスターの名前。32 文字以内で英小文字・数字・「-」のみ入力可能です。英小文字で始まり、英小文字または数字で終わる必要があります。RFC 4122 標準の UUID 形式は使用できません。 |
| Kubernetes バージョン | 使用する Kubernetes バージョン |
| VPC | クラスターに接続する VPC ネットワーク |
| サブネット | VPC に定義されたサブネットのうち、クラスターを構成するインスタンスに接続するサブネット |
| NCRサービスゲートウェイ | NCR タイプのサービスゲートウェイ<br>（サブネットにインターネットゲートウェイが接続されていない場合のみ） |
| OBSサービスゲートウェイ | OBS タイプのサービスゲートウェイ<br>（サブネットにインターネットゲートウェイが接続されていない場合のみ） |
| K8sサービスネットワーク | クラスターの service object CIDR 設定 |
| Podネットワーク | クラスターの Pod ネットワーク設定 |
| Podサブネットサイズ | クラスターの Pod サブネットサイズ設定 |
| Kubernetes API エンドポイント | Public: エンドポイントにドメインアドレスを割り当て、フローティング IP を接続します<br>Private: エンドポイントを内部ネットワークアドレスに設定します |
| 強化されたセキュリティルール | ワーカーノードセキュリティグループ作成時に必須セキュリティルールのみ作成します。クラスターワーカーノード必須セキュリティルール項目を参照してください。<br>True: 必須セキュリティルールのみ作成<br>False: 必須セキュリティルールとすべてのポートを許可するセキュリティルールを作成 |
| イメージ | クラスターを構成するインスタンスに使用するイメージ |
| Availability Zone | デフォルトノードグループのインスタンスを作成するゾーン |
| インスタンスタイプ | デフォルトノードグループのインスタンス仕様 |
| ノード数 | デフォルトノードグループのインスタンス数 |
| キーペア | デフォルトノードグループへのアクセスに使用するキーペア |
| ブロックストレージタイプ | デフォルトノードグループインスタンスのブロックストレージの種類 |
| ブロックストレージサイズ | デフォルトノードグループインスタンスのブロックストレージサイズ |
| 追加ネットワーク | デフォルトワーカーノードグループに作成する追加ネットワーク/サブネット |

必要な情報を入力し、**[クラスター作成]** をクリックすると、クラスター作成が開始されます。クラスター一覧で状態を確認できます。作成には約 10 分かかります。クラスターの設定によってはさらに時間がかかる場合があります。

> [注意]
> VPC ネットワークサブネット、K8s サービスネットワーク、Pod ネットワークの CIDR は、以下の制約に該当しないように設定する必要があります。
>  - リンクローカルアドレス帯域 (169.254.0.0/16) と重複することはできません。
>  - VPC ネットワークサブネット、追加ネットワークサブネット、Pod ネットワーク、K8s サービスネットワーク帯域は互いに重複することはできません。
>  - NKS 内部で使用している IP 帯域 (198.18.0.0/19) と重複することはできません。
>  - /24 より大きい CIDR ブロックは入力できません（例: /26、/30 などの CIDR ブロックは使用できません）。
>  - v1.23.3 以下のクラスターの場合、Docker BIP (bridged IP range) と重複することはできません (172.17.0.0/16)。
>
> クラスター作成時に設定したサービスゲートウェイは削除しないでください。
>  - 選択したサブネットがインターネットゲートウェイに接続されていない場合、NCR サービスゲートウェイと OBS サービスゲートウェイの設定が必要です。
>  - これら 2 つのサービスゲートウェイは、NKS クラスターの構成および基本機能に必要なイメージ/バイナリを取得する際に使用されます。
>  - クラスター作成時に設定したサービスゲートウェイを削除すると、クラスターが正常に動作しなくなります。
>  - クラスター作成時に設定したサービスゲートウェイが削除された場合、クラスターごとにサービスゲートウェイを再設定する必要があります。
>  - クラスターのサービスゲートウェイは、クラスターの **[基本情報]** タブのサービスゲートウェイ照会結果の横にある **[変更]** ボタンをクリックして再設定できます。
>
> クラスター作成時に設定したサブネットのインターネットゲートウェイ接続状態を変更しないでください。
>  - クラスター作成時に設定したサブネットのインターネットゲートウェイ接続状態によって、イメージ/バイナリを取得するレジストリが異なります。
>  - クラスター作成後にサブネットのインターネットゲートウェイ接続状態が変更されると、設定されたレジストリに接続できなくなり、クラスターが正常に動作しなくなります。

> [作成可能な最大ノード数]
> クラスター作成時に作成可能な最大ノード数は、Pod ネットワークおよび Pod サブネットサイズの設定によって決まります。
> 計算式: 2 ^ (Pod サブネットサイズ - Pod ネットワークのホストビット数) - 3
> 例:
>  - Pod サブネットサイズ = 24
>  - Pod ネットワーク = 10.100.0.0/16
>  - 計算: 2 ^ (24 - 16) - 3 = 最大 253 ノード作成可能

> [各ノードあたりの Pod に割り当て可能な最大 IP 数]
> 1 つのノードで使用可能な最大 IP 数は、Pod サブネットサイズの設定によって決まります。
> 計算式: 2 ^ (32 - pods_network_subnet) - 2
> 例:
>  - Pod サブネットサイズ = 24
>  - 計算: 2 ^ (32 - 24) - 2 = 最大 254 個の IP が使用可能

> [クラスターで Pod に割り当て可能な最大 IP 数]
> 計算式: 各ノードあたりの Pod に割り当て可能な最大 IP 数 × 作成可能な最大ノード数
> 例:
>  - Pod サブネットサイズ = 24
>  - Pod ネットワーク = 10.100.0.0/16
>  - 計算: 254（各ノードあたりの Pod に割り当て可能な最大 IP 数）× 253（作成可能な最大ノード数）= 最大 64,262 個の IP が使用可能

<a id="cluster-show"></a>
### クラスター照会
作成したクラスターは **Container > NHN Kubernetes Service(NKS)** ページで確認できます。クラスター一覧には、各クラスターの概要情報が表示されます。

| 項目 | 説明 |
| --- | --- |
| クラスター名 | クラスターの名前 |
| ノード数 | クラスターの全ワーカーノード数 |
| Kubernetes バージョン | Kubernetes バージョン情報 |
| kubeconfigファイル | クラスターを操作するための kubeconfig ファイルのダウンロードボタン |
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

k8s API 状態のアイコンの意味は次のとおりです。

| アイコン | 意味 |
| --- | --- |
| 緑色のソリッドアイコン | 正常動作中 |
| 黄色のソリッドアイコン | 情報の有効期間（5 分）が残りわずかで、情報が正確でない可能性があります |
| 赤色のソリッドアイコン | Kubernetes API エンドポイントが正常に動作していないか、情報の有効期間が切れています |

k8s Node 状態のアイコンの意味は次のとおりです。

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
| Kubernetes 証明書 | クラスター証明書の有効期間および有効期限日 |
| CNI | 使用中の Kubernetes CNI の種類 |
| K8sサービスネットワーク | クラスターの service object CIDR 設定 |
| Podネットワーク | 使用中の Kubernetes Pod ネットワーク設定 |
| Podサブネットサイズ | 使用中の Kubernetes Pod サブネットサイズ設定 |
| VPC | クラスターに接続された VPC ネットワーク |
| サブネット | クラスターを構成するノードインスタンスに接続されたサブネット |
| API エンドポイント | クラスターにアクセスして操作するための API エンドポイント URI |
| 設定ファイル | クラスターにアクセスして操作するために必要な設定ファイルのダウンロードボタン |

<a id="cluster-delete"></a>
### クラスター削除
削除するクラスターを選択し、**[クラスター削除]** をクリックすると削除が開始されます。削除には約 5 分かかります。クラスターの状態によってはさらに時間がかかる場合があります。

<a id="change-keypair"></a>
### クラスターキーペアの変更

クラスターに属するすべてのワーカーノードのキーペアを変更します。設定するキーペアは、ログイン中のユーザーのキーペアから 1 つ選択します。キーペアを変更すると、以下の内容が適用されます。

* すべてのワーカーノード VM に選択したキーペアが設定されます。
* 設定したキーペアを使用して、すべてのワーカーノード VM に SSH で接続できます。
* 各ワーカーノードインスタンスのキーペアは `managed-by-nks` と表示されます。

キーペアが設定されたクラスターは、サービスユーザーの権限で動作します。サービスユーザーは NKS サービスレベルで管理される内部ユーザーであり、NKS の機能動作およびサービス連携がサービスユーザーの権限で動作します。サービスユーザーの権限で動作するクラスターは、オーナーを変更・管理する必要はありません。

> [注意]
> * 一般ユーザーがオーナーとして設定されたクラスターは、キーペア変更機能を使用してサービスユーザーの権限で動作するよう変更できます。
> * クラスターオーナー変更機能は提供されなくなりました。クラスターをサービスユーザーの権限で動作させるには、キーペア変更機能を使用してください。

<a id="nodegroup-headings"></a>

## ノードグループ
ノードグループは、Kubernetes を構成するワーカーノードインスタンスのグループです。

<a id="nodegroup-show"></a>

### ノードグループ照会
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
| オレンジ色のソリッドアイコン | 一部ノードの作業成功 |
| 灰色のソリッドアイコン | クラスターおよびノードグループ使用不可 |

k8s Node 状態のアイコンの意味は次のとおりです。

| アイコン | 意味 |
| --- | --- |
| 緑色のソリッドアイコン | ノードグループのすべてのノードが Ready 状態です |
| 黄色のソリッドアイコン | Kubernetes API エンドポイントが正常に動作していないか、ノードグループ内に NotReady 状態のノードが存在します |
| 赤色のソリッドアイコン | ノードグループのすべてのノードが NotReady 状態です |

ノードグループを選択すると、下部にノードグループ情報が表示されます。

**[基本情報]** タブでは、以下の情報を確認できます。

| 項目 | 説明 |
| --- | --- |
| ノードグループ名 | ノードグループ名と ID |
| クラスター名 | ノードグループが属するクラスターの名前と ID |
| Kubernetes バージョン | 使用中の Kubernetes バージョン |
| Availability Zone | ノードグループのインスタンスが作成されたゾーン |
| インスタンスタイプ | ノードグループのインスタンス仕様 |
| イメージタイプ | ノードグループのインスタンスに使用したイメージの種類 |
| ブロックストレージサイズ | ノードグループのインスタンスのブロックストレージサイズ |
| 作成日時 | ノードグループが作成された日時 |
| 更新日時 | ノードグループが最後に更新された日時 |

* ノード一覧
**[ノード一覧]** タブでは、ノードグループを構成するインスタンスの一覧を確認できます。

<a id="nodegroup-create"></a>

### ノードグループ作成
クラスターを作成するとデフォルトのノードグループが作成されますが、必要に応じて追加のノードグループを作成できます。デフォルトのノードグループのインスタンスよりも高い仕様のコンテナ実行環境が必要な場合や、スケールアウトのためにより多くのワーカーノードインスタンスが必要な場合は、追加のノードグループを作成して使用できます。ノードグループ一覧ページで **[ノードグループ作成]** ボタンをクリックすると、ノードグループ作成ページが表示されます。ノードグループ作成に必要な項目は次のとおりです。

| 項目 | 説明 |
| --- | --- |
| Availability Zone | クラスターを構成するインスタンスを作成するゾーン |
| ノードグループ名 | 追加ノードグループ名。32 文字以内で英小文字・数字・「-」のみ入力可能です。英小文字で始まり、英小文字または数字で終わる必要があります。RFC 4122 標準の UUID 形式は使用できません。 |
| インスタンスタイプ | 追加ノードグループのインスタンス仕様 |
| ノード数 | 追加ノードグループのインスタンス数 |
| キーペア | 追加ノードグループへのアクセスに使用するキーペア |
| ブロックストレージタイプ | 追加ノードグループのインスタンスのブロックストレージの種類 |
| ブロックストレージサイズ | 追加ノードグループのインスタンスのブロックストレージサイズ |
| 追加ネットワーク | デフォルトワーカーノードグループに作成する追加ネットワーク/サブネット |

必要な情報を入力し、**[ノードグループ作成]** ボタンをクリックすると、ノードグループの作成が開始されます。ノードグループ一覧で状態を確認できます。ノードグループの作成には約 5 分かかります。ノードグループの設定によってはさらに時間がかかる場合があります。

> [注意]
> 該当クラスターを作成したユーザーのみ、ノードグループを作成できます。

<a id="nodegroup-delete"></a>

### ノードグループ削除
ノードグループ一覧から削除するノードグループを選択し、**[ノードグループ削除]** ボタンをクリックすると削除が開始されます。ノードグループの削除には約 5 分かかります。ノードグループの状態によってはさらに時間がかかる場合があります。

ノードグループに含まれるすべてのノードは、以下の順序で削除されます。
* 該当ノードが LoadBalancer タイプの Service のメンバーである場合、該当 LB のメンバーを INACTIVE 状態にします（プラットフォームバージョン 1.202602.0 以上の場合のみサポート）。
* 該当ノードが drain されます。
* 該当ノードが Kubernetes ノードリソースから削除されます。
* 該当ノードがインスタンスレベルで削除されます。

<a id="nodegroup-scale-out"></a>

### ノードグループへのノード追加
動作中のノードグループにノードを追加できます。ノードグループ情報照会ページの **[ノード一覧]** タブをクリックすると、現在のノード一覧が表示されます。**[ノード追加]** ボタンをクリックし、ノード数を入力するとノードが追加されます。

> [注意]
> オートスケーラーが有効になっているノードグループは、手動でノードを追加することはできません。

<a id="nodegroup-scale-in"></a>

### ノードグループからのノード削除
動作中のノードグループからノードを削除できます。ノードグループ情報照会ページの **[ノード一覧]** タブをクリックすると、現在のノード一覧が表示されます。ノード一覧から削除するノードを選択し、**[ノード削除]** ボタンをクリックすると確認ダイアログが表示されます。削除するノード名を再度確認し、**[確認]** ボタンをクリックするとノードが削除されます。

ノードグループに含まれるすべてのノードは、以下の順序で削除されます。
* 該当ノードが LoadBalancer タイプの Service のメンバーである場合、該当 LB のメンバーを INACTIVE 状態にします（プラットフォームバージョン 1.202602.0 以上の場合のみサポート）。
* 該当ノードが drain されます。
* 該当ノードが Kubernetes ノードリソースから削除されます。
* 該当ノードがインスタンスレベルで削除されます。

> [注意]
> オートスケーラーが有効になっているノードグループは、手動でノードを削除することはできません。

<a id="node-start-stop"></a>

### ノードの停止と起動
ノードグループに属するノードの一部を停止し、停止されたノードを再起動できます。ノードグループ情報照会ページのノードリストタブをクリックすると、現在のノードリストが表示されます。停止するノードを選択し、ノード停止ボタンをクリックするとノードが停止されます。停止されたノードを選択し、ノード起動ボタンをクリックするとノードが再起動されます。

#### 動作の流れ

起動状態のノードを停止すると、次の順序で動作します。

* 該当ノードが LoadBalancer タイプの Service のメンバーである場合、該当 LB のメンバーを INACTIVE 状態にします。（プラットフォームバージョン 1.202602.0 以上の場合のみサポート）
* 該当ノードが drain されます。
* 該当ノードが Kubernetes ノードリソースから削除されます。
* 該当ノードをインスタンスレベルで SHUTDOWN 状態にします。

停止状態のノードを起動すると、次の順序で動作します。

* 該当ノードをインスタンスレベルで ACTIVE 状態にします。
* 該当ノードが Kubernetes ノードリソースに再追加されます。


#### 制約事項

ノードの停止と起動機能には、次の制約事項があります。

* 起動状態のノードを停止でき、停止状態のノードを起動できます。
* ワーカーノードグループ内のすべてのノードを停止することはできません。
* オートスケーラーが有効なノードグループはノードを停止できません。
* 停止されたノードが存在するノードグループはオートスケーラーを有効にできません。
* 停止されたノードが存在するノードグループはアップグレードできません。


#### 状態表示

ノードの状態に応じて、ノードリストタブの状態アイコンが表示されます。アイコンの色別の状態は次のとおりです。

* 緑色：起動状態のノード
* 灰色：停止状態のノード
* 赤色：異常状態のノード

<a id="use-gpu-nodegroup"></a>

### GPU ノードグループの使用
Kubernetes を通じた GPU ベースのワークロード実行が必要な場合、GPU インスタンスで構成されたノードグループを作成できます。
クラスターまたはノードグループの作成時にインスタンスタイプを選択する際、`g2` タイプを選択すると GPU ノードグループを作成できます。

> [注記]
> NHN Cloud GPU インスタンスで提供される GPU は NVIDIA 系です。（[使用可能な GPU 仕様を確認する](/Compute/GPU%20Instance/ja/overview/#gpu)）
> NVIDIA GPU を利用するために Kubernetes に必要な nvidia-device-plugin は、GPU ノードグループ作成時に自動的にインストールされます。

作成された GPU ノードの基本的な設定状態の確認および簡単な動作テストは、次の方法を利用します。

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

#### Kubernetes レベルの状態確認
`kubectl` コマンドを使用して、クラスターレベルで使用可能な GPU リソース情報を確認します。
以下は、各ノードで使用可能な GPU コア数を出力するコマンドと実行結果です。

```
$ kubectl get nodes -A -o custom-columns='NAME:.metadata.name,GPU Allocatable:.status.allocatable.nvidia\.com/gpu,GPU Capacity:.status.capacity.nvidia\.com/gpu'
NAME                                       GPU Allocatable   GPU Capacity
my-cluster-default-w-vdqxpwisjjsk-node-1   1                 1
```

#### GPU テスト用サンプルワークロードの実行
Kubernetes クラスターに属する GPU ノードは、CPU とメモリ以外に `nvidia.com/gpu` という名前のリソースを提供します。
GPU を使用する場合は、`nvidia.com/gpu` リソースを割り当てるよう、以下のサンプルファイルのように入力します。

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
> GPU が不要なワークロードが GPU ノードに割り当てられることを防ぎたい場合は、[Taint および Toleration の概要](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/)を参照してください。

<a id="autoscaler"></a>

### オートスケーラー
オートスケーラーは、ノードグループの利用可能なリソースが不足している場合、またはノードの使用率が一定水準以下に保たれている場合に、ノード数を自動的に調整する機能です。この機能はノードグループごとに設定でき、それぞれ独立して動作します。NKS では 2 種類のオートスケーラーをサポートしています。

* メトリクスベースのオートスケーラー
* クラスターオートスケーラー

オートスケーラー機能はノードグループごとに設定して動作します。機能は以下のパスで設定できます。

* クラスター作成時にデフォルトのノードグループに設定
* ノードグループ追加時に追加ノードグループに設定
* 作成済みのノードグループに設定

> [注意]
> オートスケーラーが有効なノードグループは、手動でノードを追加または削除できません。
> オートスケーラーは重複して有効にすることはできません。

**用語の整理**
オートスケーラー機能で使用する用語とその意味は次のとおりです。

| 用語 | 意味 |
| --- | --- |
| スケールアウト | ノード数を増加させることを指します |
| スケールイン | ノード数を減少させることを指します |

<a id="metric-base-autoscaler"></a>

#### メトリクスベースのオートスケーラー
メトリクスベースのオートスケーラーは、NHN Cloud の [Cloud Monitoring](/Monitoring/Cloud%20Monitoring/ja/overview/) サービスをベースに動作します。ワーカーノードにインストールされたメトリクス収集エージェントが 1 分周期でシステムメトリクスを Cloud Monitoring に送信し、収集されたメトリクスが設定した閾値を超過または下回った場合に、自動的にノードを追加または削除します。増設 (Scale Out) と削減 (Scale In) の機能はそれぞれ独立して有効化できます。

<a id="metric-base-autoscaler-set"></a>
##### メトリクスベースのオートスケーラーの設定
メトリクスベースのオートスケーラーを有効化すると、以下の項目を設定できます。

**増設設定**

| 設定項目 | 意味 | 有効範囲 | デフォルト値 |
| --- | --- | --- | --- |
| 最大ノード数 | 増設可能な最大ノード数 | 1〜10 | 10 |
| 有効化 | ノード増設オートスケーラーの有効/無効の設定 | 有効/無効 | 無効 |

**削減設定**

| 設定項目 | 意味 | 有効範囲 | デフォルト値 |
| --- | --- | --- | --- |
| 最小ノード数 | 削減可能な最小ノード数 | 1〜10 | 10 |
| 有効化 | ノード削減オートスケーラーの有効/無効の設定 | 有効/無効 | 無効 |

**共通設定**

| 設定項目 | 意味 | 有効範囲 | デフォルト値 | 単位 |
| --- | --- | --- | --- | --- |
| ルール演算子 | オートスケーリング発動条件間に適用する演算子の設定<br>AND：すべての条件を満たした場合に発動<br>OR：いずれか 1 つを満たした場合に発動 | AND/OR | OR | - |
| オートスケーリング待機時間 | 前回のスケール完了後、次のスケールまで待機する最小時間（増設・削減それぞれ個別に設定可能） | 1〜60 | 10 | 分 |
| ノードパフォーマンスメトリクス | モニタリング対象のメトリクス設定（下表参照） | メトリクスの種類 | 必須設定 | - |
| ノード調整数 | オートスケーリング発生時に追加/削除するノード数 | 1〜10 | 1 | 台 |
| 閾値設定 | 条件発動のためのメトリクス閾値 | メトリクスごと | 必須設定 | - |
| 閾値領域維持時間 | 閾値状態が設定時間（2〜60 分）以上継続した場合にスケール動作を実行 | 2〜60 | 必須設定 | 分 |

**ノードパフォーマンスメトリクス**

| システムリソース | 提供する統計データ | 単位 |
| --- | --- | --- |
| CPU 使用率 | ノードグループに属するすべてのノードの CPU 使用量の平均 | % |
| メモリ使用率 | ノードグループに属するすべてのノードのメモリ使用量の平均 | % |
| ディスク転送率（読み取り） | ノードグループに属するすべてのノードの 1 秒あたりのディスク読み取りデータ量の平均 | Bytes/s |
| ディスク転送率（書き込み） | ノードグループに属するすべてのノードの 1 秒あたりのディスク書き込みデータ量の平均 | Bytes/s |
| ネットワーク転送率（送信） | スケーリンググループに属するすべてのインスタンスの 1 秒あたりのネットワーク送信データ量の平均 | Bytes/s |
| ネットワーク転送率（受信） | スケーリンググループに属するすべてのインスタンスの 1 秒あたりのネットワーク受信データ量の平均 | Bytes/s |

<a id="metric-base-autoscaler-resize"></a>
##### 増設および削減の条件
以下の条件をすべて満たした場合にノードを増設します。

* 選択したノードパフォーマンスメトリクスが閾値超過の状態で閾値領域維持時間以上継続
* 現在のノード数 < 最大ノード数
* オートスケーリング待機時間の経過

以下の条件をすべて満たした場合にノードを削減します。

* ノードパフォーマンスメトリクスが閾値未満の状態で閾値領域維持時間以上継続
* 現在のノード数 > 最小ノード数
* オートスケーリング待機時間の経過

> [注記]
> オートスケーリング待機時間は、増設ポリシーと削減ポリシーそれぞれに指定できます。
> 通常、増設待機時間は短く設定することで、突発的な負荷の上昇に即座に対応できます。
> 反対に、削減待機時間は長く設定してインスタンスを段階的に削減することで、安定性を確保します。
> サービスの負荷状況を継続的にモニタリングし、適切なポリシーを設定することで、インスタンスの無駄を防ぐことができます。
> 特定のノード 1 台のみが条件を満たしている場合はポリシーが発動しません。ノードグループのすべてのノードの平均で算出されます。
> 指定されたパフォーマンスメトリクスが閾値領域維持時間の間、基準値を超え続けているかどうかを継続的に監視し、ポリシーの発動有無を判断します。
> 例えば、条件が「CPU 使用率が 90% 以上」で閾値領域維持時間が 5 分の場合、5 分間 CPU 使用率が 90% を下回らない状態が続いた場合にポリシーが発動されます。

> [ノード削減に関する注記]
> メトリクスベースのオートスケーラーが削減を実施する場合、最も新しく作成されたノードから順に削除します。

<a id="metric-base-autoscaler-example"></a>
##### 動作例

**増設ポリシー**

| 設定項目 | 設定値 |
| --- | --- |
| 最大ノード数 | 7 台 |
| 増設ノード調整数 | 3 台 |
| 増設後の待機時間 | 5 分 |
| 増設条件：メトリクス | CPU |
| 増設条件：閾値領域維持時間 | 5 分 |
| 増設条件：閾値 | 70% 以上 |

**削減ポリシー**

| 設定項目 | 設定値 |
| --- | --- |
| 最小ノード数 | 3 台 |
| 削減ノード調整数 | 1 台 |
| 削減後の待機時間 | 10 分 |
| 削減条件：メトリクス | CPU |
| 削減条件：閾値領域維持時間 | 2 分 |
| 削減条件：閾値 | 30% 以下 |

**動作概要**

* 現在のノードグループのノード数：5 台
* 5 台のノードの CPU 使用量の平均が 70% 以上の状況が 5 分間継続し、ノード増設がリクエストされる
* 増設ポリシーの増設ノード調整数は 3 台だが、最大ノード数が 7 台のため、実際には +2 台の増設となる（ノード数：5 → 7）
* ノード増設作業完了から 5 分後、7 台のノードの CPU 使用量の平均が 30% 以下の状況が 2 分間継続し、ノード削減がリクエストされる
* 削減後の待機時間が 10 分のため、リクエストは拒否される
* 10 分経過後にノード削減が実施される
* 削減ポリシーの削減ノード調整数が 1 台のため、ノード 1 台を削除（ノード数：7 → 6）
* 削減後の 10 分の待機中は追加の削減は発生しない

**動作過程の詳細**

| 時刻（分） | CPU 平均 | ノード数 | スケール状態 | 説明 |
| ------ | ------ | ---- | ------ | --- |
| 0〜3 | 65% | 5 | – | 閾値（70%）未満 |
| 4 | 72% | 5 | – | 増設条件の閾値以上 → 閾値領域維持時間 5 分の測定開始 |
| 4〜8 | 73% | 5 | – | 増設条件の閾値以上の状態が 5 分間維持され、増設条件を充足 |
| 8 | 76% | 5 → 7 | 増設リクエスト | 増設ノード調整数 3 台だが最大ノード数 7 台の制限により実際は +2 台<br>ノード追加作業開始 |
| 8〜13 | 65% | 7 | – | ノード追加作業完了<br>作業が完了した 13 分が「増設/削減後の待機」条件の開始時点として設定される |
| 13 | 28% | 7 | – | 増設条件の閾値以下 → 閾値領域維持時間 2 分の測定開始 |
| 15 | 27% | 7 | 削減リクエスト（拒否） | 削減条件の閾値以下の状態が 2 分間維持され、削減条件を充足<br>ただし削減後の待機 10 分（13→23）が進行中のため拒否 |
| 15〜23 | 27% | 7 | – | 削減後の待機時間が継続中 |
| 23 | 27% | 7 → 6 | 削減 | 削減後の待機時間 10 分が満了し、削減条件が引き続き充足<br>削減ノード調整数が 1 台のためノード 1 台を削除 |
| 24 | 28% | 6 | | ノード削減作業完了<br>作業が完了した 24 分が「増設/削減後の待機」条件の開始時点として設定される |
| 24〜 | 28% | 6 | – | 増設条件の閾値以下 → 閾値領域維持時間 2 分の測定開始<br>その後、削減後の待機 10 分（24→34）の条件が満たされると 1 台ずつ削減される |

<a id="cluster-autoscaler"></a>

#### クラスターオートスケーラー
クラスターオートスケーラーは、Kubernetes プロジェクトの公式サポート機能である cluster-autoscaler 機能をベースに動作します。詳細については、[Cluster Autoscaler](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler) を参照してください。

> [注記]
> NHN Kubernetes Service(NKS) に適用されている `cluster-autoscaler` のバージョンは `1.19.0` です。

<a id="cluster-autoscaler-set"></a>
##### クラスターオートスケーラーの設定
クラスターオートスケーラーを有効にすると、以下の項目を設定できます。

| 設定項目 | 説明 | 有効範囲 | デフォルト値 | 単位 |
| --- | --- | --- | --- | --- |
| 最小ノード数 | スケールダウン可能な最小ノード数 | 1〜10 | 1 | 台 |
| 最大ノード数 | スケールアップ可能な最大ノード数 | 1〜10 | 10 | 台 |
| 縮小 | ノード縮小の有効/無効設定 | 有効/無効 | 有効 | - |
| リソース使用量しきい値 | 縮小の基準となるリソース使用量のしきい値 | 1〜100 | 50 | % |
| しきい値維持時間 | 縮小対象となるノードのリソース使用量がしきい値以下を維持する時間 | 1〜1440 | 10 | 分 |
| スケールアップ後の縮小遅延時間 | ノードのスケールアップ後、縮小対象ノードとしてモニタリングを開始するまでの遅延時間 | 10〜1440 | 10 | 分 |

<a id="cluster-autoscaler-resize"></a>
##### スケールアップおよびスケールダウンの条件
以下の条件をすべて満たす場合、ノードをスケールアップします。

* Pod をスケジューリングできるノードが存在しない
* 現在のノード数 < 最大ノード数

以下の条件をすべて満たす場合、ノードをスケールダウンします。

* ノードのリソース使用量がしきい値以下の状態をしきい値維持時間の間維持している
* 現在のノード数 > 最小ノード数

特定のノードに以下の条件を満たす Pod が 1 つでも存在する場合、そのノードはスケールダウン候補から除外されます。

* `PodDisruptionBudget` によって制約されている Pod
* `kube-system` ネームスペースの Pod
* `deployment`、`replicaset` などの制御オブジェクトによって起動されていない Pod
* ローカルストレージを使用している Pod
* `node selector` などの制約により、他のノードへ移動できない Pod

スケールアップおよびスケールダウンの詳細な条件については、[Cluster Autoscaler FAQ](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md) を参照してください。

<a id="cluster-autoscaler-example"></a>
##### 動作例
オートスケーラーの動作を例を通して説明します。

**1. オートスケーラーの有効化**

対象クラスターの基本ノードグループのオートスケーラー機能を有効にします。この例では、基本ノードグループのノード数を 1 として作成し、オートスケーラーの設定項目を以下のように設定しました。

| 設定項目 | 設定値 |
| --- | --- |
| 最小ノード数 | 1 |
| 最大ノード数 | 5 |
| 縮小 | 有効 |
| リソース使用量しきい値 | 50 |
| しきい値維持時間 | 3 |
| スケールアップ後の縮小遅延時間 | 10 |

**2. Pod のデプロイ**

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

デプロイをリクエストした Pod の CPU リソスの合計がノード 1 台のリソースを超えるため、以下のようにいくつかの Pod が `Pending` 状態のままになります。この状況でノードのスケールアップが発生します。

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

**3. ノードのスケールアップの確認**

以下はスケールアップ前のノード一覧です。

```
# kubectl get nodes
NAME                                            STATUS   ROLES    AGE   VERSION
autoscaler-test-default-w-ohw5ab5wpzug-node-0   Ready    <none>   45m   v1.28.3
```

約 5〜10 分後、以下のようにノードがスケールアップされたことを確認できます。

```
# kubectl get nodes
NAME                                            STATUS   ROLES    AGE   VERSION
autoscaler-test-default-w-ohw5ab5wpzug-node-0   Ready    <none>   48m   v1.28.3
autoscaler-test-default-w-ohw5ab5wpzug-node-1   Ready    <none>   77s   v1.28.3
autoscaler-test-default-w-ohw5ab5wpzug-node-2   Ready    <none>   78s   v1.28.3
```

`Pending` 状態だった Pod がノードのスケールアップ後に正常にスケジューリングされたことを確認できます。

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

ノードのスケールアップに関するイベントは、以下のコマンドで確認できます。

```
# kubectl get events --field-selector reason="TriggeredScaleUp"
LAST SEEN   TYPE     REASON             OBJECT                                 MESSAGE
4m          Normal   TriggeredScaleUp   pod/nginx-deployment-756fd4cdf-64gtv   pod triggered scale-up: [{default-worker-bf5999ab 1->3 (max: 5)}]
4m          Normal   TriggeredScaleUp   pod/nginx-deployment-756fd4cdf-7bsst   pod triggered scale-up: [{default-worker-bf5999ab 1->3 (max: 5)}]
...
```

**4. Pod 削除後のノードスケールダウンの確認**

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

しばらくするとノードのスケールダウンが発生し、ノード数が 1 台に減少したことを確認できます。ノードのスケールダウンにかかる時間は設定によって異なる場合があります。

```
# kubectl get nodes
NAME                                            STATUS   ROLES    AGE   VERSION
autoscaler-test-default-w-ohw5ab5wpzug-node-0   Ready    <none>   71m   v1.28.3
```

ノードのスケールダウンに関するイベントは、以下のコマンドで確認できます。

```
# kubectl get events --field-selector reason="ScaleDown"
LAST SEEN   TYPE     REASON      OBJECT                                               MESSAGE
13m         Normal   ScaleDown   node/autoscaler-test-default-w-ohw5ab5wpzug-node-1   node removed by cluster autoscaler
13m         Normal   ScaleDown   node/autoscaler-test-default-w-ohw5ab5wpzug-node-2   node removed by cluster autoscaler
```

ノードグループごとのオートスケーラーのステータス情報は、`configmap/cluster-autoscaler-status` で確認できます。この ConfigMap はノードグループごとに異なるネームスペースに作成されます。オートスケーラーが作成するノードグループごとのネームスペースの命名規則は次のとおりです。

* 形式: nhn-ng-{ノードグループ名}
* {ノードグループ名} にはノードグループの名前が入ります。
* 基本ノードグループのノードグループ名は `default-worker` です。

基本ノードグループに対するオートスケーラーのステータス情報を確認する方法は次のとおりです。詳細については、[Cluster Autoscaler FAQ](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md) を参照してください。

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

> [注記]
> 状態情報の内容のうち、`Cluster-wide` 領域の内容は `NodeGroups` 領域の内容と同じです。

<a id="cluster-autoscaler-with-hpa"></a>
##### HPA(HorizontalPodAutoscale) 機能と連動した動作例
HPA (Horizontal Pod Autoscaler) 機能は、CPU 使用率などのリソース使用量を監視して、レプリケーションコントローラー (ReplicationController)、デプロイメント (Deployment)、レプリカセット (ReplicaSet)、ステートフルセット (StatefulSet) の Pod 数を自動的にスケールします。Pod 数を調整する過程で、ノードの利用可能なリソースが不足したり、リソースが過剰に余る状況が発生する場合があります。このとき、オートスケーラー機能と連動してノード数を増減できます。この例では、HPA 機能とオートスケーラー機能を連動して動作させる方法を示します。HPA の詳細については、[Horizontal Pod Autoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) のドキュメントを参照してください。

**1. オートスケーラーの有効化**
上記の例と同様に、オートスケーラーを有効にします。

**2. HPA の設定**
Web リクエストを受信すると一定時間 CPU 負荷を生成するコンテナをデプロイします。そのサービスを公開します。以下は `php-apache.yaml` ファイルの内容です。

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

次に HPA を設定します。先ほど作成した php-apache deployment オブジェクトに対して、最小 Pod 数を 1、最大 Pod 数を 30、目標 CPU 使用率を 50% に設定します。

```
# kubectl autoscale deployment php-apache --cpu-percent=50 --min=1 --max=30
horizontalpodautoscaler.autoscaling/php-apache autoscaled
```

HPA の状態を確認すると、設定値と現在の状態を確認できます。まだ CPU 負荷を発生させる Web リクエストを送信していないため、CPU 使用率は 0% です。

```
# kubectl get hpa
NAME         REFERENCE               TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   0%/50%    1         30        1          80s
```

**3. 負荷の印加**
新しいターミナルで負荷を発生させる Pod を実行します。この Pod は無限に Web リクエストを送信し続けます。`Ctrl+C` で停止できます。

```
# kubectl run -i --tty load-generator --rm --image=busybox --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://php-apache; done"
If you don't see a command prompt, try pressing enter.
OK!OK!OK!OK!OK!OK!OK!
```

`kubectl top nodes` コマンドを使用して、ノードの現在のリソース使用量を確認できます。負荷を発生させる Pod の実行後、時間の経過とともに CPU 負荷が増加することを確認できます。

```
# kubectl top nodes
NAME                                            CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
autoscaler-test-default-w-ohw5ab5wpzug-node-0   66m          6%     1010Mi          58%

(しばらく後)

# kubectl top nodes
NAME                                            CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
autoscaler-test-default-w-ohw5ab5wpzug-node-0   574m         57%    1013Mi          58%
```

HPA の状態を確認すると、CPU 使用率が増加し、それに合わせて REPLICAS (= Pod 数) が増加したことを確認できます。

```
# kubectl get hpa
NAME         REFERENCE               TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   250%/50%   1         30        5          2m44s
```

**4. オートスケーラーの動作確認**
Pod を確認すると、Pod 数が増加する中で一部の Pod は `node-0` にスケジューリングされて Running 状態になりましたが、一部は Pending 状態であることを確認できます。

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

Pod をスケジューリングできない状況が、オートスケーラーのノード増設条件となります。Cluster Autoscaler Pod が提供する状態情報を確認すると、ScaleUp が InProgress 状態になったことを確認できます。

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

しばらくすると、ノード (node-8) が 1 つ増加したことを確認できます。

```
# kubectl get nodes
NAME                                            STATUS     ROLES    AGE   VERSION
autoscaler-test-default-w-ohw5ab5wpzug-node-0   Ready      <none>   22d   v1.28.3
autoscaler-test-default-w-ohw5ab5wpzug-node-8   Ready      <none>   90s   v1.28.3
```

Pending 状態だったすべての Pod が正常にスケジューリングされ、Running 状態になったことを確認できます。

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

負荷のために実行していた Pod (`load-generator`) を `Ctrl+C` で停止すると、しばらくして負荷が下がります。負荷が下がると、Pod が占有していた CPU 使用量が減り、Pod の数が減少します。

```
# kubectl get hpa
NAME         REFERENCE               TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   0%/50%    1         30        1          31m
```

Pod の数が減少してノードのリソース使用量が下がると、最終的にノードのスケールインが発生します。新たに追加されていた node-8 が削減されたことを確認できます。

```
# kubectl get nodes
NAME                                            STATUS   ROLES    AGE   VERSION
autoscaler-test-default-w-ohw5ab5wpzug-node-0   Ready    <none>   22d   v1.28.3
```

<a id="user-script-old"></a>

### ユーザースクリプト (old)
クラスターを作成するときおよび追加ノードグループを作成するときに、ユーザースクリプトを登録できます。ユーザースクリプト機能には次のような特徴があります。

* 機能設定
    * この機能はワーカーノードグループごとに設定できます。
    * クラスター作成時に入力したユーザースクリプトは、デフォルトのワーカーノードグループに適用されます。
    * 追加ノードグループ作成時に入力したユーザースクリプトは、該当するワーカーノードグループに適用されます。
    * **ワーカーノードグループが作成された後は、ユーザースクリプトの内容を変更できません。**
* スクリプトの実行タイミング
    * ユーザースクリプトは、ワーカーノードの初期化プロセス中にインスタンスの初期化処理で実行されます。
    * ユーザースクリプトが実行された後、該当インスタンスを「ワーカーノードグループ」のワーカーノードとして設定および登録します。
* スクリプトの内容
    * ユーザースクリプトの 1 行目は必ず `#!` で始める必要があります。
    * スクリプトの最大サイズは 64KB です。
    * スクリプトは root 権限で実行されます。
    * スクリプトの実行ログは以下の場所に保存されます。
        * スクリプトの終了コード: `/var/log/userscript.exitcode`
        * スクリプトの標準出力および標準エラーストリーム: `/var/log/userscript.output`

<a id="user-script"></a>

### ユーザースクリプト
2022 年 7 月 26 日以降に作成されるノードグループには、新しいバージョンのユーザースクリプト機能が搭載されます。旧バージョンの機能と比較して、次のような特徴があります。

* **ワーカーノードグループが作成された後でも、ユーザースクリプトの内容を変更できます。**
    * ただし、変更された内容はユーザースクリプト変更後に作成されるノードにのみ適用されます。
* スクリプトの実行ログは以下の場所に保存されます。
    * スクリプトの終了コード: `/var/log/userscript_v2.exitcode`
    * スクリプトの標準出力および標準エラーストリーム: `/var/log/userscript_v2.output`

* 旧バージョンとの関係
    * 新バージョンの機能が旧バージョンの機能を置き換えます。
        * コンソールまたは API を通じてノードグループを作成する際に設定したユーザースクリプトは、新バージョンの機能として設定されます。
    * 旧バージョンのユーザースクリプトを設定したワーカーノードグループは、旧バージョンの機能と新バージョンの機能がそれぞれ独立して動作します。
        * 旧バージョンに設定したユーザースクリプトの内容は変更できません。
        * 新バージョンに設定したユーザースクリプトの内容は変更できます。
    * 旧バージョンと新バージョンにそれぞれユーザースクリプトを設定した場合、次の順序で実行されます。
        1. 旧バージョンのユーザースクリプト
        2. 新バージョンのユーザースクリプト

<a id="instance-flavor-update"></a>

### インスタンスタイプの変更
ワーカーノードグループのインスタンスタイプを変更します。ワーカーノードグループに属するすべてのワーカーノードのインスタンスタイプが変更されます。


#### 進行手順

インスタンスタイプの変更は、次の順序で進行します。

1. クラスターオートスケーラー機能を無効にします。
2. 該当するワーカーノードグループにバッファーノードを追加します。
3. ワーカーノードグループ内のすべてのワーカーノードに対して、順次以下の作業を実行します。
    1. 該当するワーカーノードで動作中の Pod を退避し、ノードをスケジュール不可能な状態に移行します。
    2. ワーカーノードのインスタンスタイプを変更します。
    3. ノードをスケジュール可能な状態に移行します。
4. バッファーノードで動作中の Pod を退避し、バッファーノードを削除します。
5. クラスターオートスケーラー機能を再度有効にします。

インスタンスタイプの変更は、ワーカーコンポーネントのアップグレードと同様の方法で進行します。バッファーノードの作成と削除、Pod の退避については、[クラスターアップグレード](/Container/NKS/ja/user-guide/#cluster-upgrade)を参照してください。


#### 制約事項

インスタンスの現在のタイプによって、変更できるタイプが異なります。

* m2、c2、r2、t2、x1 タイプのインスタンスは、m2、c2、r2、t2、x1 タイプに変更できます。
* m2、c2、r2、t2、x1、g2 タイプのインスタンスは、u2 タイプに変更できません。
* u2 タイプのインスタンスは、作成後にタイプを変更できません。同じ u2 タイプへの変更も不可です。

<a id="custom-image"></a>

### カスタムイメージをワーカーイメージとして活用

ユーザーのカスタムイメージをベースにしたワーカーノードグループを作成できます。カスタムイメージをワーカーノードイメージとして活用できるよう、NHN Cloud Image Builder サービスで追加作業（NKS ワーカーノード化）が必要です。Image Builder サービスで NHN Kubernetes Service（NKS）ワーカーノードアプリケーションを使用してイメージテンプレートを作成し、カスタムワーカーノードイメージを生成できます。Image Builder サービスの詳細については、[Image Builder ユーザーガイド](/Compute/Image%20Builder/ja/console-guide/#_1)を参照してください。

> [注意]
> NKS ワーカーノード化の作業にはパッケージのインストールおよび設定変更などが含まれており、正常に動作しないイメージで作業を進めると失敗する可能性があります。
> Image Builder サービスの使用に対して課金される場合があります。

#### 制約事項
サポートされている OS イメージおよび OS イメージごとに選択する必要があるアプリケーションバージョン情報は以下の表のとおりです。カスタムイメージを作成するベースインスタンスのイメージに合わせて、正しいバージョンのアプリケーションを選択する必要があります。

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

#### 進行手順

カスタムイメージをワーカーノードイメージとして活用するために、Image Builder サービスで以下の手順を実行します。

1. **[イメージテンプレートの作成]** をクリックします。
2. アプリケーションを選択した後、**[イメージテンプレート名]**、**[OS]**、**[最小ブロックストレージ（GB）]**、**[ユーザースクリプト]**、**[説明]** を入力します。
    * GPU Flavor を使用しないワーカーノードグループの場合、NHN Kubernetes Service（NKS）Worker Node アプリケーションを選択します。
    * GPU Flavor を使用するワーカーノードグループの場合、NHN Kubernetes Service（NKS）Worker Node（GPU）アプリケーションを選択します。
3. **[確認]** をクリックしてイメージテンプレートを作成します。
4. 作成されたイメージテンプレートを選択した後、**[イメージビルド]** を選択します。
5. **[イメージビルド]** 画面で **[個人イメージ]** タブを選択し、NKS ワーカーノード化を進めるカスタムイメージを選択します。
6. **[確認]** をクリックすると、NKS ワーカーノード化が実行された後、新しいイメージが作成されます。
7. **[クラスター作成]** または **[ノードグループ作成]** 画面で、作成されたカスタムイメージを選択します。

![nkscustom_image_1.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/nkscustom_image_1.png)

![nkscustom_image_2.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/nkscustom_image_2.png)

![nkscustom_image_3.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/nkscustom_image_3.png)

<a id="extra-volumes"></a>

### 追加ブロックストレージ
ノードグループに追加ブロックストレージを使用できます。クラスターおよびノードグループの作成時に追加ブロックストレージを指定して作成するか、既存のノードグループに追加ブロックストレージを作成して使用できます。追加ブロックストレージには以下の特徴があります。

* 追加ブロックストレージはノードグループあたり最大 3 個まで設定でき、ブロックストレージのサイズは 1〜2048 GB の範囲で指定できます。
* ノードグループの追加ブロックストレージ設定は、ノードグループに属するすべてのワーカーノードに同じく適用されます。
    * 追加ブロックストレージ変更時、ノードグループのすべてのワーカーノードに変更内容が反映されます。
* 追加ブロックストレージの変更は、サイズ変更およびマウントパスの変更のみサポートされます。
    * 作成済みの追加ブロックストレージの削除はできません。
    * 既存の設定値より小さいサイズへの変更はできません。
* 追加ブロックストレージの名前は `{クラスター名}-{ノードグループ名}-{ノード名}-extra-volume-{インデックス}` の形式で指定されます。
* マウントパスを入力した場合、追加ブロックストレージの作成後に指定したパスへのマウントを試みます。
    * 未入力の場合はマウントを行いません。
    * 正しくないマウントパスを入力してマウントに失敗すると、機能が動作しなくなります。

[注意]
> 追加ブロックストレージの設定変更は既存ボリュームのアンマウントを含むため、使用中のサービスに影響を与える可能性があります。

<a id="extra-security-groups"></a>

### 追加セキュリティグループ
ノードグループに追加セキュリティグループを設定できます。クラスターおよびノードグループの作成時に追加セキュリティグループを指定して作成するか、既存のノードグループに追加セキュリティグループを設定できます。追加セキュリティグループの特徴は以下のとおりです。

* 追加セキュリティグループはサブネットあたり最大 8 個まで設定できます。
* ノードグループの追加セキュリティグループ設定は、ノードグループに属するすべてのワーカーノードに同じく適用されます。
* 追加セキュリティグループを未入力の場合、クラスターのデフォルトセキュリティグループのみ適用されます。
* ユーザーが個別ノードに直接設定したセキュリティグループは、ノードグループの追加セキュリティグループ項目には表示されません。

[注記]
> コンソールでノードグループ作成時に指定した追加セキュリティグループは、デフォルトネットワークおよびすべての追加ネットワークに適用されます。個別ネットワークの追加セキュリティグループの変更は、ノードグループ作成後に行えます。

[注意]
> ノードグループに追加セキュリティグループを設定する際、既存インスタンスに割り当てられているセキュリティグループのうち、追加セキュリティグループに定義されていない対象は削除されます。
> 追加セキュリティグループ変更時にネットワーク設定が変更されるため、設定が適用される間、一時的に通信に影響が生じる場合があります。

<a id="fip-auto-bind"></a>

### フローティング IP 自動割り当て
ノードグループにフローティング IP 自動割り当て機能を使用できます。機能が有効化されたノードグループは、ノード作成時にフローティング IP を自動的に割り当てます。クラスターおよび追加ノードグループの作成時に機能の有効化を選択でき、設定したオプションは後から変更できます。フローティング IP 自動割り当て機能を有効化するために必要な項目は以下のとおりです。

| 項目 | 説明 |
| --- | --- |
| 接続するサブネット | フローティング IP を接続するネットワークインターフェースのサブネットを指定します。該当サブネットはクラスターのデフォルトサブネットであるか、ノードグループの追加サブネットに含まれている必要があります。 |
| フローティング IP ラベル | ノードに割り当てるフローティング IP を選別するための識別子です。入力しない場合、割り当て対象はすべてのフローティング IP となります。 |


フローティング IP 自動割り当て機能の特徴は以下のとおりです。

* フローティング IP を新規作成しません。
  * ユーザーが事前に作成したフローティング IP を割り当てる方式で動作します。利用可能なフローティング IP が十分でない場合、ノードの追加に失敗する可能性があります。
* フローティング IP 自動割り当て機能の有効化/無効化および設定変更は、既存ノードに影響を与えません。
  * 機能が有効化されていないノードグループで機能を有効化しても、既存ノードにフローティング IP は割り当てられません。
  * 機能が有効化されているノードグループで機能を無効化しても、既存ノードに割り当てられたフローティング IP は解除されません。

<a id="cluster-management"></a>

## クラスター管理
リモートホストからクラスターを操作・管理するには、Kubernetes が提供するコマンドラインツール（CLI）である `kubectl` が必要です。

<a id="kubectl-install"></a>

### kubectl のインストール
kubectl は特別なインストール手順なしに、実行ファイルをダウンロードしてすぐに使用できます。OS ごとのダウンロードコマンドは以下のとおりです。

> [注意]
> ワーカーノードでパッケージマネージャーを使用して kubeadm、kubelet、kubectl などの Kubernetes 関連コンポーネントをインストールすると、クラスターが正常に動作しなくなる可能性があります。ワーカーノードに kubectl をインストールする場合は、以下のダウンロードコマンドを参照してファイルをダウンロードしてください。

| OS | ダウンロードコマンド |
| --- | --- |
| Linux | curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.15.7/bin/linux/amd64/kubectl |
| MacOS | curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.15.7/bin/darwin/amd64/kubectl |
| Windows | curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.15.7/bin/windows/amd64/kubectl.exe |

その他のインストール方法やオプションなどの詳細については、[Install and Set Up kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/) のドキュメントを参照してください。

#### 権限の変更
ダウンロードしたファイルはデフォルトでは実行権限がありません。実行権限を追加する必要があります。

```
$ chmod +x kubectl
```

#### 配置場所の変更またはパスの指定
どのディレクトリからでも kubectl を実行できるよう、環境変数に指定されたパスに移動するか、kubectl が存在するパスを環境変数に追加します。

* 環境変数に指定されたパスへ移動
```
$ sudo mv kubectl /usr/local/bin/
```

* 環境変数にパスを追加
```
// kubectl が存在するパスで実行
$ export PATH=$PATH:$(pwd)
```

<a id="kubectl-set-kubeconfig"></a>

### 設定
kubectl で Kubernetes クラスターにアクセスするには、クラスター設定ファイル（kubeconfig）が必要です。NHN Cloud ウェブコンソールで **Container > NHN Kubernetes Service(NKS)** ページを開き、アクセスするクラスターを選択します。下部の **[基本情報]** タブで **[設定ファイル]** 項目の **[ダウンロード]** ボタンをクリックして設定ファイルをダウンロードします。ダウンロードした設定ファイルは任意の場所に移動し、kubectl 実行時に参照できるよう準備します。

> [注意]
> NHN Cloud ウェブコンソールからダウンロードした設定ファイルには、クラスター情報および認証用トークンなどが含まれています。設定ファイルがあれば、該当 Kubernetes クラスターにアクセスできる権限を持つことになります。設定ファイルを絶対に紛失しないよう注意してください。

kubectl は実行するたびにクラスター設定ファイルが必要です。そのため、毎回 `--kubeconfig` オプションを使用してクラスター設定ファイルを指定する必要があります。ただし、環境変数にクラスター設定ファイルのパスが保存されている場合は、毎回オプションを指定する必要はありません。

```
$ export KUBECONFIG={クラスター設定ファイルのパス}
```

クラスター設定ファイルのパスを環境変数に保存したくない場合は、kubectl のデフォルト設定ファイルである `$HOME/.kube/config` にコピーして使用することもできます。ただし、複数のクラスターを運用する場合は、環境変数の値を変更する方法が便利です。

<a id="kubectl-check-connection"></a>

### 接続確認
`kubectl version` コマンドで正常に設定されているか確認します。問題がなければ `Server Version` が出力されます。

```
$ kubectl version
Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.7", GitCommit:"6c143d35bb11d74970e7bc0b6c45b6bfdffc0bd4", GitTreeState:"clean", BuildDate:"2019-12-11T12:42:56Z", GoVersion:"go1.12.12", Compiler:"gc", Platform:"darwin/amd64"}
Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.7", GitCommit:"6c143d35bb11d74970e7bc0b6c45b6bfdffc0bd4", GitTreeState:"clean", BuildDate:"2019-12-11T12:34:17Z", GoVersion:"go1.12.12", Compiler:"gc", Platform:"linux/amd64"}
```

* Client Version: 実行した kubectl ファイルのバージョン情報
* Server Version: クラスターを構成している Kubernetes のバージョン情報

<a id="certificatesigningrequest"></a>

### CSR(CertificateSigningRequest)
KubernetesのCertificate APIを通じて、Kubernetes APIクライアント用のX.509証明書をリクエストおよび発行できます。CSRリソースは、証明書のリクエストと、そのリクエストに対する承認/拒否を決定できるようにします。詳細については、[Certificate Signing Requests](https://kubernetes.io/docs/reference/access-authn-authz/certificate-signing-requests/) のドキュメントを参照してください。

#### CSRリクエストと発行承認の例
まず、秘密鍵 (private key) を生成します。証明書の生成に関する詳細は、[Certificates](https://kubernetes.io/docs/tasks/administer-cluster/certificates/) のドキュメントを参照してください。

```
$ openssl genrsa -out dev-user1.key 2048
Generating RSA private key, 2048 bit long modulus
...........................................................................+++++
..................+++++
e is 65537 (0x010001)

$ openssl req -new -key dev-user1.key -subj "/CN=dev-user1" -out dev-user1.csr
```

生成した秘密鍵の情報を含むCSRリソースを作成し、証明書の発行をリクエストします。

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

この証明書発行リクエストを承認します。

```
$ kubectl certificate approve dev-user1
certificatesigningrequest.certificates.k8s.io/dev-user1 approved
```

CSRを再度確認すると、`Approved,Issued` 状態に変更されていることを確認できます。

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
> * 板橋 (Pangyo) リージョン: 2020年12月29日以降に作成したクラスター
> * 平村 (Pyeongchon) リージョン: 2020年12月24日以降に作成したクラスター

<a id="admission-controller"></a>

### admission controller プラグイン
admission controller は、Kubernetes API サーバーへのリクエストをインターセプトし、オブジェクトを変更したり、リクエストを拒否したりできます。admission controller の詳細については、[承認コントローラー](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/) を参照してください。また、admission controller の使用例については、[承認コントローラーガイド](https://kubernetes.io/blog/2019/03/21/a-guide-to-kubernetes-admission-controllers/) を参照してください。

クラスターのバージョンおよびクラスターの作成時点によって、admission controller に適用されるプラグインの種類が異なります。詳細については、リージョン別の作成時点に応じたプラグイン一覧を参照してください。

#### v1.19.13 以前のバージョン
板橋 (Pangyo) リージョンで 2021年2月22日以前に作成したクラスター、および平村 (Pyeongchon) リージョンで 2021年2月17日以前に作成したクラスターには、次のプラグインが適用されます。

* DefaultStorageClass
* DefaultTolerationSeconds
* LimitRanger
* MutatingAdmissionWebhook
* NamespaceLifecycle
* NodeRestriction
* ResourceQuota
* ServiceAccount
* ValidatingAdmissionWebhook

板橋 (Pangyo) リージョンで 2021年2月23日以降に作成したクラスター、および平村 (Pyeongchon) リージョンで 2021年2月18日以降に作成したクラスターには、次のプラグインが適用されます。

* DefaultStorageClass
* DefaultTolerationSeconds
* LimitRanger
* MutatingAdmissionWebhook
* NamespaceLifecycle
* NodeRestriction
* PodSecurityPolicy (新規追加)
* ResourceQuota
* ServiceAccount
* ValidatingAdmissionWebhook

#### v1.20.12 以降のバージョン
Kubernetesのバージョンごとのデフォルトの有効な admission controller はすべて有効化されます。デフォルトの有効な admission controller に加えて、以下のコントローラーが追加で有効化されます。

* NodeRestriction
* PodSecurityPolicy

<a id="cluster-upgrade"></a>

### クラスターアップグレード
NHN Kubernetes Service(NKS)は、動作中の Kubernetes クラスターの Kubernetes コンポーネントのアップグレードをサポートします。

#### Kubernetes バージョンの差異サポートポリシー
Kubernetes のバージョンは `x.y.z` で表されます。`x` はメジャーバージョン、`y` はマイナーバージョン、`z` はパッチバージョンです。機能が追加された場合はメジャーバージョンまたはマイナーバージョンを上げ、バグ修正など以前のバージョンと互換性のある機能を提供する場合はパッチバージョンを上げます。詳細については、[Semantic Versioning 2.0.0](https://semver.org/) を参照してください。

Kubernetes クラスターは、動作中の状態で Kubernetes コンポーネントをアップグレードできます。そのために、Kubernetes コンポーネントごとに Kubernetes バージョンの差異に応じた機能サポートの可否を定義しています。マイナーバージョンを基準に 1 段階のバージョン差異は相互の機能互換をサポートすることで、動作中のクラスターの Kubernetes コンポーネントのアップグレードをサポートします。また、コンポーネントの種類ごとにアップグレード順序を定義しています。詳細については、[Version Skew Policy](https://kubernetes.io/releases/version-skew-policy/) を参照してください。

<br>

#### NKS クラスターのバージョン管理
NKS クラスターは、クラスターのコントロールプレーンとワーカーノードグループごとに Kubernetes バージョンとプラットフォームバージョンを管理します。Kubernetes バージョンとプラットフォームバージョンには、次のような違いがあります。

##### Kubernetes バージョン
* アップストリーム Kubernetes で定義するバージョンです。
* NKS クラスターを構成する Kubernetes の主要コンポーネントのバージョンを決定します。
* Kubernetes バージョンに影響を受ける主要コンポーネントは次のとおりです。
    * kube-apiserver
    * kube-controller-manager
    * kube-scheduler
    * kubelet
    * kube-proxy

##### プラットフォームバージョン
* NKS サービスレベルで定義するバージョンです。
* NKS クラスターを構成するさまざまなコンポーネントを 1 つのバージョンとして定義して管理します。
* プラットフォームバージョンに影響を受ける主要コンポーネントは次のとおりです。
    * containerd、etcd などのコントロールプレーンおよびワーカーノードの主要コンポーネント
    * 各種システムコンポーネントおよびシステム管理ツールなど

<br>

クラスターの Kubernetes バージョンとプラットフォームバージョンの状態に応じたアップグレード対象は次のとおりです。

| Kubernetes バージョンの状態 | プラットフォームバージョンの状態 | アップグレード対象 |
| --- | --- | --- |
| 最新ではない | 最新ではない | Kubernetes バージョンおよびプラットフォームバージョン |
| 最新ではない | 最新 | Kubernetes バージョン |
| 最新 | 最新ではない | プラットフォームバージョン |
| 最新 | 最新 | なし |

コントロールプレーンの Kubernetes バージョンとプラットフォームバージョンはクラスター照会画面で確認でき、ワーカーノードグループの Kubernetes バージョンとプラットフォームバージョンは各ワーカーノードグループの照会画面で確認できます。

<br>

#### アップグレードルール
NKS クラスターのバージョン管理方式と Kubernetes バージョンの差異サポートポリシーにより、コンポーネントごとに順序に従ってアップグレードする必要があります。NKS クラスターのアップグレード機能に適用されるルールは次のとおりです。

* コントロールプレーンと各ワーカーノードグループごとにアップグレードコマンドを実行する必要があります。
* コントロールプレーンの Kubernetes バージョンとすべてのワーカーノードグループの Kubernetes バージョンが一致している場合にのみアップグレードできます。
* コントロールプレーンを先にアップグレードした後、ワーカーノードグループをアップグレードできます。
* Kubernetes バージョンは、現在の Kubernetes バージョンの次のバージョン（マイナーバージョン基準 +1）にアップグレードできます。
* プラットフォームバージョンは、NKS サービスで提供する最新バージョンにアップグレードできます。
* Kubernetes バージョンとプラットフォームバージョンはいずれもダウングレードをサポートしません。
* 他の機能の動作によりクラスターが更新中の状態では、アップグレードできません。
* Kubernetes バージョンを v1.25.4 から v1.26.3 にアップグレードする際、CNI が Flannel の場合は Calico-VXLAN に変更する必要があります。
* NKS レジストリが有効化されていないクラスターはアップグレードできません。

次の例は、Kubernetes バージョンのアップグレード過程におけるアップグレード可否を表で示したものです。例で使用した条件は次のとおりです。

* NHN Cloud がサポートする Kubernetes バージョンリスト: v1.31.4、v1.32.3、v1.33.4
* クラスターは v1.31.4 で作成

| 状態 | コントロールプレーンバージョン | コントロールプレーンのアップグレード可否 | ワーカーノードグループバージョン | ワーカーノードグループのアップグレード可否 |
| --- | :-: | :-: | :-: | :-: |
| 初期状態 | v1.31.4 | 可能 <sup>[1](#footnote_cluster_upgrade_rule_1)</sup> | v1.31.4 | 不可能 <sup>[2](#footnote_cluster_upgrade_rule_2)</sup> |
| コントロールプレーンアップグレード後の状態 | v1.32.3 | 不可能 <sup>[3](#footnote_cluster_upgrade_rule_3)</sup> | v1.31.4 | 可能 <sup>[4](#footnote_cluster_upgrade_rule_4)</sup> |
| ワーカーノードグループアップグレード後の状態 | v1.32.3 | 可能 <sup>[1](#footnote_cluster_upgrade_rule_1)</sup> | v1.32.3 | 不可能 <sup>[2](#footnote_cluster_upgrade_rule_2)</sup> |
| コントロールプレーンアップグレード後の状態 | v1.33.4 | 不可能 <sup>[3](#footnote_cluster_upgrade_rule_3)</sup> | v1.32.3 | 可能 <sup>[4](#footnote_cluster_upgrade_rule_4)</sup> |
| ワーカーノードグループアップグレード後の状態 | v1.33.4 | 不可能 <sup>[5](#footnote_cluster_upgrade_rule_5)</sup> | v1.33.4 | 不可能 <sup>[2](#footnote_cluster_upgrade_rule_2)</sup> |

注釈

* <a name="footnote_cluster_upgrade_rule_1">1</a>: コントロールプレーンとすべてのワーカーノードグループのバージョンが一致しているためアップグレード可能
* <a name="footnote_cluster_upgrade_rule_1">2</a>: ワーカーノードグループはコントロールプレーンがアップグレードされた後にアップグレード可能
* <a name="footnote_cluster_upgrade_rule_1">3</a>: コントロールプレーンとすべてのワーカーノードグループのバージョンが一致している場合にのみアップグレード可能
* <a name="footnote_cluster_upgrade_rule_1">4</a>: コントロールプレーンがアップグレードされたためアップグレード可能
* <a name="footnote_cluster_upgrade_rule_1">5</a>: NHN Cloud でサポートする最新バージョンを使用しているためアップグレード不可能

<br>

#### etcd バージョン変更に伴う注意事項
クラスターアップグレード作業を進める際、アップグレード対象のプラットフォームバージョンに定義された [etcd バージョン](/Container/NKS/ja/user-guide/#platform-version-etcd-version) が現在のクラスターの etcd バージョンと異なる場合に限り、etcd のアップグレード作業が同時に進行されます。該当作業を開始する前に、注意事項を必ず把握し、事前通知や点検時間の確保などの措置を取ることをお勧めします。

##### データ整合性確認のための頻繁なリソース変更の自制
etcd のアップグレード中にリソースのデプロイ/削除作業が頻繁に発生すると、データ整合性の確認に失敗してアップグレードが失敗する場合があります。安全なアップグレードのために、次のような環境でアップグレードすることをお勧めします。
* クラスターリソースの変更作業が少ない時間帯に実施
* 運用への影響が少ない時間帯（点検時間など）にアップグレードを実施
* アップグレード直前の大規模デプロイ/削除、バッチ処理の実行などを避け、トラフィックが安定した後に進行

##### etcd アップグレード失敗時の自動復旧中におけるクラスター運用の一時停止
etcd のアップグレードが失敗すると、クラスターを以前の状態に戻す自動復旧手順が動作し、この手順が進行中はクラスター運用（Kubernetes API の応答）が一時的に停止する場合があります。すでに実行中のワークロード（Pod）には影響はありませんが、kubectl の呼び出しが一時的に遅延したり、新規リソースの作成/変更作業が一時的に停止したりする場合があります。

<br>

#### アップグレード戦略
NKS クラスターは、Rolling Upgrade と Blue/Green Upgrade の 2 種類のアップグレード戦略を提供します。ユーザーは運用ポリシーに応じて適切な戦略を選択してクラスターをアップグレードできます。

<br>

**Rolling Upgrade**

![Rolling_Upgrade.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/Rolling_Upgrade.png)

Rolling Upgrade は、コントロールプレーンとワーカーノードグループを順次アップグレードして、クラスター全体を新しいバージョンに移行するアップグレード戦略です。以下は、Rolling Upgrade 戦略によるクラスターアップグレードの実施過程と各過程の説明です。

<br>

##### 1. クラスター照会画面のアップグレードボタンを使用してコントロールプレーンコンポーネントをアップグレードします。

NKS クラスターのコントロールプレーンは高可用性を保証します。コントロールプレーンはローリングアップデート方式でアップグレードされるため、クラスターの可用性が保証されます。この過程で Kubernetes API が一時的に失敗する場合があります。

##### 2. ノードグループ照会画面のアップグレードボタンを使用して、すべてのワーカーノードグループのワーカーコンポーネントをアップグレードします。

ワーカーノードグループごとにワーカーコンポーネントをアップグレードできます。ワーカーコンポーネントのアップグレードは次の順序で進行します。

1. クラスターオートスケーラー機能を無効にします。<sup>[1](#footnote_worker_component_upgrade_1)</sup>
2. 該当ワーカーノードグループにバッファノード<sup>[2](#footnote_worker_component_upgrade_2)</sup>を追加します。<sup>[3](#footnote_worker_component_upgrade_3)</sup>
3. ワーカーノードグループ内のすべてのワーカーノードに対して、順次以下の作業を実施します。<sup>[4](#footnote_worker_component_upgrade_4)</sup>
    1. 該当ワーカーノードで動作中の Pod を退避し、ノードをスケジュール不可能な状態に移行します。
    2. ワーカーコンポーネントをアップグレードします。
    3. ノードをスケジュール可能な状態に移行します。
4. バッファノードで動作中の Pod を退避し、バッファノードを削除します。
5. クラスターオートスケーラー機能を再度有効にします。<sup>[1](#footnote_worker_component_upgrade_1)</sup>

注釈

* <a name="footnote_worker_component_upgrade_1">1</a>: このステップは、アップグレード機能開始前にクラスターオートスケーラー機能が有効になっている場合にのみ有効です。
* <a name="footnote_worker_component_upgrade_2">2</a>: バッファノードとは、アップグレード過程中に既存のワーカーノードから退避された Pod が再スケジューリングできるように作成しておく予備ノードのことです。該当ワーカーノードグループで定義されたワーカーノードと同じスペックのノードとして作成され、アップグレード過程が終了すると自動的に削除されます。このノードは Instance の料金ポリシーに従って課金されます。
* <a name="footnote_worker_component_upgrade_3">3</a>: アップグレード時にバッファノード数を設定できます。デフォルト値は 1 で、0 に設定するとバッファノードを追加しません。最小値は 0 で、最大値は（ノードグループあたりの最大ノード数クォータ − 該当ワーカーノードグループの現在のノード数）です。
* <a name="footnote_worker_component_upgrade_4">4</a>: アップグレード時に設定した最大サービス不可ノード数ずつ作業を実施します。デフォルト値は 1 です。最小値は 1 で、最大値は該当ワーカーノードグループの現在のノード数です。

この過程で、次のようなことが発生する場合があります。

* サービス中の Pod が退避されて他のノードにスケジューリングされます（Pod の退避に関する詳細は、以下の Pod 退避関連の注意事項を参照してください）。
* オートスケーラー機能が動作しません。


> [Pod 退避関連の注意事項]
> 1. DaemonSet コントローラーによる Pod は退避されません。
> DaemonSet コントローラーは各ワーカーノードごとに Pod を実行するため、DaemonSet コントローラーによって実行された Pod は退避されても他のノードで実行することはできません。ワーカーノードグループのアップグレード過程において、DaemonSet コントローラーによって実行された Pod は退避されません。
> 2. ローカルストレージを使用する Pod は退避される際に使用していたデータを失います。
> `emptyDir` を使用してノードのローカルストレージを使用する Pod は、退避される際に使用していたデータを失います。ノードのローカルに保存されたストレージは他のノードに移動できないためです。
> 3. 他のノードへの複製が不可能な Pod は他のノードに移動されません。
> ReplicationController、ReplicaSet、Job、DaemonSet、StatefulSet などのコントローラーによって実行された Pod が退避されると、コントローラーによって他のノードにスケジューリングされます。ただし、このようなコントローラーを使用していない Pod は、退避後に他のノードにスケジューリングされません。
> 4. PodDisruptionBudgets (PDB) の設定により、退避が失敗したり遅延したりする場合があります。
> PodDisruptionBudgets (PDB) の設定で維持する必要がある Pod 数を定義できます。この機能の設定により、アップグレード過程で Pod の退避が不可能になる場合や、Pod の退避時間が長くなる場合があります。Pod の退避に失敗するとアップグレードが失敗します。そのため、PDB が設定されている場合は、適切な PDB 設定で Pod の退避がスムーズに動作するように設定する必要があります。PDB の設定に関する詳細は[こちら](https://kubernetes.io/docs/tasks/run-application/configure-pdb/)を参照してください。


安全な Pod 退避に関する詳細な説明は、[Safely Drain a Node](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/) を参照してください。


<br>

**Blue/Green Upgrade**

![Blue_Green.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/Blue_Green.png)

Blue/Green Upgrade は、アップグレード過程においてクラスター内に 2 つの異なるバージョンの環境を構成することで、アプリケーションの可用性を高め、デプロイ失敗時のロールバックプロセスを単純化してアップグレードリスクを低減できるアップグレード戦略です。一方の環境（Blue）はアップグレード前のバージョンのノードグループで構成され、もう一方の環境（Green）はアップグレードするバージョンのノードグループで構成されます。Green 環境でのテストが完了したら、アプリケーショントラフィックを Green 環境に移動し、Blue 環境を廃棄します。この過程を通じて、クラスター全体を次のバージョンにアップグレードできます。以下は、Blue/Green Upgrade 戦略によるクラスターアップグレードの実施過程と各過程の説明です。

<br>

##### 1. クラスター照会画面のアップグレードボタンを使用してコントロールプレーンコンポーネントをアップグレードします。
NKS クラスターのコントロールプレーンは高可用性を保証します。コントロールプレーンはローリングアップデート方式でアップグレードされるため、クラスターの可用性が保証されます。この過程で Kubernetes API が一時的に失敗する場合があります。

##### 2. ノードグループを作成します。
新規ノードグループを作成して、テスト用の Green 環境を作成します。コントロールプレーンコンポーネントのアップグレード後に作成される新規ノードグループは、コントロールプレーンの Kubernetes バージョンと同じバージョンで作成されます。Green 環境に Blue 環境（既存のノードグループ）と同じリソースをデプロイして、アップグレード後の環境の検証を実施できます。この際、Blue 環境が既存クラスターの運用に影響を与えないよう、アプリケーショントラフィックを分離する必要があります。

##### 3. Green 環境（新規ノードグループ）の検証後、アプリケーショントラフィックを Green 環境に切り替えます。
新たに構築した Green 環境で、既存ユーザーが運用していたリソースが次のバージョンの Kubernetes と正常に互換性があるかを検証し、検証が完了したらアプリケーショントラフィックを既存の Blue 環境から新たに構築した Green 環境に切り替えます。Green 環境での検証段階で問題が発生した場合は、トラフィックを切り替えずに Blue 環境を削除することで簡単にロールバックできます。

##### 4. Blue 環境（以前のバージョンのすべてのワーカーノードグループ）を廃棄します。
Blue 環境のリソースをすべて廃棄すると、コントロールプレーンとすべてのワーカーノードグループのバージョンがすべて一致します。

<a id="api-endpoint-ipacl"></a>

### クラスター API エンドポイント IP アクセス制御の適用
クラスター API エンドポイントに IP アクセス制御を適用または解除できます。
IP アクセス制御機能の詳細については、[IP アクセス制御](/Network/Load%20Balancer/ja/overview/#ip) ドキュメントを参照してください。

#### IP アクセス制御対象ルール
クラスター API エンドポイントの IP アクセス制御対象を追加する場合、以下のルールが適用されます。

* IP アクセス制御タイプが **許可** に設定されている場合、クラスターのデフォルトサブネット CIDR がアクセス制御対象に自動的に追加されます。
* IP アクセス制御タイプが **許可** に設定されている場合、NKS コンソールのダッシュボード、ネームスペース、ワークロード、サービス＆ネットワーク、ストレージ、設定、イベントタブが無効になります。
* IP アクセス制御タイプが **遮断** に設定されている場合、クラスターのデフォルトサブネット CIDR 帯域と重複する IP 帯域がアクセス制御対象リストに存在するとリクエストが拒否されます。
* 最大設定可能な IP アクセス制御対象数は 100 件です。
* IP アクセス制御対象は 1 件以上存在する必要があります。

<a id="rotate-certificate"></a>

### クラスター証明書の更新
Kubernetes は、コンポーネント間の TLS 認証に PKI 証明書が必要です。PKI 証明書の詳細については、[PKI 証明書および要件](https://kubernetes.io/ko/docs/setup/best-practices/certificates/)を参照してください。NKS クラスターを作成する場合、クラスターに必要な証明書が自動的に生成され、この証明書のデフォルトの有効期間は 5 年に設定されています。

証明書の有効期間が満了すると、API サーバー、コントローラーマネージャー、etcd などのクラスターの主要コンポーネントが動作しなくなり、クラスターを使用できなくなります。
証明書が満了する前に、証明書更新機能を使用して有効期間を更新できます。クラスターの証明書有効期間および証明書更新ボタンは、クラスター照会画面 > **[基本情報]** > **[Kubernetes 証明書]** で確認できます。

証明書更新機能の使用方法は以下のとおりです。
1. **[証明書の更新]** をクリックします。
2. 更新期間を選択します。
    * 証明書の有効期間を最大 5 年まで設定できます。
    * 更新は 1 年単位でのみ可能です。
3. 証明書の更新を進めるには、**[確認]** をクリックします。
4. 対象クラスターの状態を確認します。
    * 証明書更新作業が進行中のクラスターの状態は `UPDATE_IN_PROGRESS` であり、作業が正常に完了した場合は `UPDATE_COMPLETE` 状態に移行します。
    * 作業に問題が発生した場合は `UPDATE_FAILED` 状態に移行し、正常化されるまでクラスター構成変更作業（ノード追加など）は許可されません。
        * クラスター状態を正常化するために、証明書の更新をもう一度実行します。
5. クラスター照会画面で、証明書の有効期限が正常に更新されたことを確認します。
6. kubeconfigファイルを新たにダウンロードします。
    * クラスターにアクセスするための kubeconfigファイルには証明書が含まれています。
    * 証明書が更新されると、既存の kubeconfig ではクラスターにアクセスできなくなります。
7. CA 証明書を使用している Pod を再起動します。
    * 証明書更新の過程には、ユーザーが作成した Pod を再起動する機能は含まれていません。
    * 証明書の設定が含まれている Pod が存在する場合、更新された CA 証明書を適用するために再起動が必要です。

> [注記]
> 証明書更新機能は、バージョン 1.24 以上の Calico-VXLAN CNI または Cilium CNI を使用するクラスターで使用できます。


> [注意]
> 証明書更新機能には、新しい証明書の生成および設定反映のため、システムコンポーネントおよびクラスター作成時に初期デプロイされたすべての kube-system ネームスペースの Pod の再起動が伴います。
> そのため、証明書の更新が進行中の間は、一時的にクラスターのノード状態が Not Ready に変更されたり、クラスターの一部のコンポーネントが正常に動作しない場合があります。
> このような作業影響を最小限に抑えるには、証明書更新作業の進行中は新しい Pod の作成などの作業を実行しないでください。

<a id="k8s-component"></a>

### Kubernetes コンポーネント設定機能

Kubernetes コンポーネントのさまざまなオプションを設定できます。クラスター作成時に設定でき、設定したオプションはクラスター作成完了後に変更することもできます。

動作領域別に設定をサポートするコンポーネントとオプションは次のとおりです。各項目の詳細については、[Kubernetes 公式ドキュメント](https://kubernetes.io/docs/)を参照してください。

> [注意]
> * コントロールプレーンで動作するコンポーネントの設定を変更した場合、コントロールプレーンのコンポーネントが再起動されます。
> * ワーカーノードで動作するコンポーネントの設定を変更した場合、ワーカーノードのコンポーネントが再起動されます。
> * ワーカーノードで動作するコンポーネントの設定は、ワーカーノードグループごとに設定できます。（プラットフォームバージョン 1.202602.0 以降のバージョンに限ります）

### コントロールプレーンオプション

| コンポーネント | オプション | 説明 |
| --- | --- | --- |
| kube-apiserver | default-not-ready-toleration-seconds | ノードが NotReady 状態のとき、そのノードで実行中の Pod をどのくらいの間許容するかを定義します。<br>（単位: 秒、デフォルト値: 300、最小値: 0、最大値: 86400） |
| kube-apiserver | default-unreachable-toleration-seconds | ノードがネットワークに接続されていないとき、そのノードで実行中の Pod をどのくらいの間許容するかを定義します。<br>（単位: 秒、デフォルト値: 300、最小値: 0、最大値: 86400） |
| kube-controller-manager | node-monitor-grace-period | ノードが異常状態のとき、そのノードを異常とみなすまでの待機時間を定義します。<br>（単位: 秒、デフォルト値: 40、最小値: 0、最大値: 86400） |
| kube-controller-manager | unhealthy-zone-threshold | Availability Zone を異常とみなす NotReady ノード比率のしきい値を定義します。<br>（単位: パーセント、デフォルト値: 55、最小値: 0、最大値: 100） |

### ワーカーノードオプション

| コンポーネント | オプション | 説明 |
| --- | --- | --- |
| kubelet | node-status-update-frequency | kubelet のノード状態報告周期を定義します。<br>（単位: 秒、デフォルト値: 10、最小値: 0、最大値: 86400） |
| kubelet | max-pods | ノードで実行可能な最大 Pod 数を定義します。<br>（デフォルト値: 110、最小値: 1、最大値: Podネットワークおよびサブネットサイズの設定に基づいて計算された最大作成可能 Pod IP 数）<br>プラットフォームバージョン 1.202602.0 以降のバージョンでサポートします。 |

<a id="k8s-label"></a>

### Kubernetes ラベル設定機能
ノードグループごとに Kubernetes ラベル設定機能を使用できます。この機能を使用すると、ラベルが設定されたノードグループはノード作成時にユーザーが設定したラベルを自動的に追加します。ラベルは Pod、ノードなどのオブジェクトに付与されたキーと値のペアであり、オブジェクトの特性を識別するために使用されます。ラベルの詳細については、[Labels and Selectors](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/)を参照してください。

Kubernetes ラベルはキーと値のペアで構成され、有効なラベルキーと値はそれぞれ以下のルールに従う必要があります。

#### ラベルキー
ラベルキーはスラッシュ（/）で区切られた接頭辞と名前の構造を持つことができ、接頭辞は省略可能です。

* 接頭辞
    * 253 文字以下である必要があります。
    * DNS のサブドメイン形式である必要があります。
    * 事前定義された接頭辞は使用できません。
        * ["kubernetes.io", "k8s.io", "magnum.openstack.org"]
* 名前
    * 63 文字以下である必要があります。
    * アルファベット大文字・小文字、数字、ダッシュ（-）、アンダースコア（_）、ピリオド（.）のみ許可され、英数字で始まり、英数字で終わる必要があります。


#### ラベル値
* 空白または 63 文字以下である必要があります。
* アルファベット大文字・小文字、数字、ダッシュ（-）、アンダースコア（_）、ピリオド（.）のみ許可され、英数字で始まり、英数字で終わる必要があります。

> [注記]
> * Kubernetes ラベルは最大 20 個まで指定できます。
> * Kubernetes ラベルの設定を変更すると、以降に新規作成されるノードから変更された設定が適用されます。

<a id="oidc-auth"></a>

### OIDC 認証設定機能

OIDC（OpenID Connect）は、OAuth 2.0 フレームワークを基盤とした相互運用可能な認証プロトコルです。OIDC を使用すると、外部認証サービスを通じてユーザーを認証できます。OIDC の詳細な動作方式については、[What is OpenID Connect](https://openid.net/developers/how-connect-works/)を参照してください。

NKS クラスターは、OIDC を使用した認証を処理するように設定できます。OIDC 認証に関する設定項目は次のとおりです。

| 項目 | 必須有無 | 説明 |
| --- | --- | --- |
| Issuer URL | O | 「https://」で始まる OIDC プロバイダー URL |
| Client ID | O | OIDC プロバイダーのクライアント ID |
| Username claim | X | username として使用する claim。デフォルト値: 「sub」<br>email 以外の claim にはプロバイダー URL が接頭辞として連結されます。 |
| Groups claim | X | groups として使用する claim |
| Username prefix | X | 競合を防ぐために username claim に付与する接頭辞（prefix）。<br>設定しない場合、email を除く username claim にはプロバイダー URL が接頭辞として連結されます。<br>接頭辞を使用しない場合は「-」を入力します。 |
| Groups prefix | X | 競合を防ぐために groups claim に付与する接頭辞（prefix） |
| Required claim | X | ID トークンで確認が必要なキー/値のペア |
| CA File | X | OIDC プロバイダーの Web 証明書に署名した CA の証明書ファイル |
| Signing Algs | X | 許可された JOSE 非対称署名アルゴリズムのリスト。デフォルト値: 「RS256」 |

<a id="control-plane-k8s-log"></a>

### コントロールプレーン Kubernetes コンポーネントログの保存
NHN Kubernetes Service（NKS）は、コントロールプレーンで実行中の主要な Kubernetes コンポーネントのログを提供します。これにより、クラスター内で発生するさまざまなイベントや動作をより明確に把握でき、サービス状態の診断および問題解決に役立てることができます。

コントロールプレーン Kubernetes コンポーネントログ保存機能の特徴は次のとおりです。

* Log & Crash Search、Object Storage の 2 つのサービスのいずれか一方にログを転送できます。
* 転送されるログレベルは `INFO` に固定されます。
* ログを提供する Kubernetes コンポーネントは以下のとおりです。
    * kube-apiserver
    * kube-scheduler
    * kube-controller-manager


> [注記]
> ログの転送先は 1 つのみ設定できます。Log & Crash Search と Object Storage の両方でログを管理するには、まず転送先を Log & Crash Search に設定した後、「ログ外部保管」機能を使用して該当ログを Object Storage に追加保存できます。
> 別のプロジェクトの Log & Crash Search または Object Storage にも転送できます。

<a id="control-plane-k8s-log-lncs"></a>
#### Log & Crash Search への転送

<a id="control-plane-k8s-log-lncs-forward"></a>
##### ログ転送周期
ログの転送は、ログ生成時点からユーザーが指定した転送周期の経過後に実行されます。転送周期は 1 分から 60 分の間で設定できます。

> [注記]
> 転送周期より前にログ容量が 300KB を超えた場合、即座に Log & Crash Search へ転送されます。

<a id="control-plane-k8s-log-lncs-labels"></a>
##### Log & Crash Search ラベル情報
Log & Crash Search へのログ転送時に設定されるラベル情報は次のとおりです。

| ラベル | 説明 
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
> 選択したフィールド項目からラベルを追加して直接登録し、確認することができます。

<a id="control-plane-k8s-log-obs"></a>
#### Object Storage への転送

<a id="control-plane-k8s-log-obs-forward"></a>
##### ログ転送周期
ユーザーが指定した転送周期ごとにログを収集して転送します。転送周期は 1 分から 60 分の間で設定できます。

> [注記]
> Object Storage に保存されるファイルの容量が 300KB を超えると、分割して保存されます。
> ログファイルは 300KB を超えた時点で即座に転送されます。
> 400KB 以下: _index0 サフィックスが付いた単一ファイルとして保存
> 400KB 超: _index1、_index2 などのサフィックスが付いた複数のファイルに分割して保存

<a id="control-plane-k8s-log-obs-compression"></a>
##### ファイル圧縮
ストレージに保管する際、gzip 形式で圧縮して保存するかどうかを選択できます。

<a id="control-plane-k8s-log-obs-authorization"></a>
##### ストレージアクセス権限の付与
コンソールの NKS ページで **[NKS システムアカウント情報]** をクリックすると、NKS が使用するテナント ID とユーザー ID が表示されます。コントロールプレーンログの保存先タイプを OBS（Object Storage）に設定した場合、この NKS システムアカウントに該当コンテナへの書き込み権限を必ず付与する必要があります。そうしないと、NKS システムアカウントはユーザーの OBS にデータを書き込むことができません。

設定方法

* NHN Cloud > Object Storage コンソールにアクセスします。
* コントロールプレーンログを保存するコンテナを選択します。
* 下部の基本情報 > アクセスポリシー設定変更をクリックします。
* ロールベースのアクセスポリシーで使用をクリックします。
* 上記で確認した NKS システムアカウント情報のテナント ID とユーザー ID を入力し、Write 権限を付与します。

> [注意]
> コントロールプレーンログの転送中に Object Storage のコンテナが削除されたり、コンテナから Write 権限が削除されたりすると、ログの転送に失敗します。

<a id="control-plane-k8s-log-path"></a>
##### コントロールプレーンログの保存パス
コントロールプレーンログの保存パスは、OBS endpoint、AUTH tenant、Container、Path の情報を基に、以下の形式で構成されます。

* {OBS_https_endpoint}/{AUTH_OBS_TENANT}/{Container}/{Path}

例えば、設定値が以下の場合

* OBS https endpoint: https://kr1-api-object-storage.nhncloudservice.com/v1
* AUTH_OBS_TENANT: AUTH_e670167936434f85a03694184000ffe6
* Container: nks_log_container
* 希望する保存パス: example/my/folder

実際のコントロールプレーンログの保存パスは次のとおりです。

* https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_e670167936434f85a03694184000ffe6/nks_log_container/example/my/folder

> [注記]
> obs_api_url に設定された OBS endpoint、AUTH_tenant、Container 情報が存在しない場合、設定リクエストに失敗します。

実際のログは上記 URL の配下に、次の構造で保存されます。

* ${ユーザー設定 OBS コンテナ名}/NKS/${クラスター UUID}/${マスターノード名}/${K8S コンポーネント名}/${年}/${月}/${年月日-時分秒}-index${index_count}.gz

例えば、設定値が以下の場合

* Container: nks_log_container
* クラスター UUID: f31dd18f-4dab-49fa-97bb-8feba31cb30b
* クラスター名: nks-test
* コンポーネント: kube-apiserver
* 保存時刻: 2025-04-28 10:15:00

OBS コンテナにログが生成されるパスは次のとおりです。

* nks_log_container/NKS/f31dd18f-4dab-49fa-97bb-8feba31cb30b/
  nks-test-master-0/kube-apiserver/2025/04/20250428-101500-index0.gz

<a id="k8s-taint"></a>

### Kubernetes テイント設定機能
ノードグループごとに Kubernetes テイント（taint）設定機能を使用できます。この機能によって作成されたノードグループは、ユーザーが設定したテイントが適用された状態で初期化されます。Taint の詳細については、[Taints and Tolerations](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/) を参照してください。

Kubernetes テイントはキー、値、エフェクト（effect）で構成され、各項目は次のルールに従う必要があります。

#### テイントキー

テイントキーはスラッシュ（/）で区切られるプレフィックスと名前の構造を持つことができ、プレフィックスは省略可能です。

* プレフィックス
    * 253 文字以下である必要があります。
    * DNS のサブドメイン形式である必要があります。
    * 事前定義されたプレフィックスは使用できません。
        * ["kubernetes.io", "k8s.io", "magnum.openstack.org"]
* 名前
    * 63 文字以下である必要があります。
    * アルファベット大文字・小文字、数字、ハイフン（-）、アンダースコア（_）、ドット（.）のみ使用でき、英数字で始まり英数字で終わる必要があります。

#### テイント値

* 空白、または 63 文字以下である必要があります。
* アルファベット大文字・小文字、数字、ハイフン（-）、アンダースコア（_）、ドット（.）のみ使用でき、英数字で始まり英数字で終わる必要があります。

#### テイントエフェクト（Effect）

次の 3 つの値のいずれかを指定する必要があります。

* NoSchedule
    * 該当 Taint を許容（Toleration）しない Pod はノードにスケジューリングされません。
    * 既存の実行中の Pod には影響しません。
* PreferNoSchedule
    * 可能であれば該当ノードを避けてスケジューリングしますが、不可能な場合はスケジューリングされる場合があります。
* NoExecute
    * 該当 Taint を許容しない既存の Pod はノードから即座に退去（eviction）され、新規 Pod もスケジューリングされません。

[注記]
* Kubernetes テイントはノードグループあたり最大 30 個まで指定できます。
* Kubernetes テイント設定を変更すると、以降に新規作成されるノードから変更後の設定が適用されます。

<a id="konnectivity-description"></a>

### konnectivity

Konnectivity は、Kubernetes においてコントロールプレーン（API サーバー）とワーカーノード間のネットワーク通信を安全にプロキシするコンポーネントです。従来は API サーバーがノードの kubelet や Pod に直接アクセスする必要があり、ネットワーク構成が複雑になる問題がありました。

Konnectivity はこの問題を解決するために、2 つの部分で構成されます。
* Konnectivity Server: コントロールプレーンに存在し、API サーバーから受け取ったリクエストを Konnectivity Agent に転送します。
* Konnectivity Agent: ワーカーノードに存在し、Konnectivity Server から受け取ったリクエストを対象 Pod に転送し、その応答を再び Konnectivity Server に返します。

Konnectivity Server と Konnectivity Agent が先に接続を確立してトンネルを生成し、API サーバーはこのトンネルを通じて Pod と通信します。

> [注意]
> 以下のリソースは Konnectivity Agent に関連するリソースです。対象リソースへの設定変更やリソース削除などは、クラスターの動作に重大な影響を与える可能性があります。
> 
> | 種類 | ネームスペース | 名前 |
> | --- | --- | --- |
> | ServiceAccount | kube-system | konnectivity-agent |
> | ClusterRoleBinding | kube-system | konnectivity-server-auth-delegator |
> | Deployment | kube-system | konnectivity-agent |

> [注記] 
> Konnectivity はプラットフォームバージョン 1.202605.0 以上で提供されます。

<a id="worker-node-management"></a>

## ワーカーノード管理

<a id="container-management"></a>
### コンテナ管理

#### Kubernetes v1.24.3 以前のバージョンのクラスター
Kubernetes v1.24.3 以前のバージョンのクラスターは、Docker を使用してコンテナランタイムを構成します。ワーカーノードで docker CLI を使用して、コンテナ状態の照会やコンテナイメージの照会などの作業を行えます。docker CLI の詳細な説明と使用方法については、[Use the Docker command line](https://docs.docker.com/engine/reference/commandline/cli/) を参照してください。

#### Kubernetes v1.24.3 以降のバージョンのクラスター

Kubernetes v1.24.3 以降のバージョンのクラスターは、containerd を使用してコンテナランタイムを構成します。ワーカーノードで docker CLI の代わりに nerdctl を使用して、コンテナ状態の照会やコンテナイメージの照会などの作業を行えます。nerdctl の詳細な説明と使用方法については、[nerdctl: Docker-compatible CLI for containerd](https://github.com/containerd/nerdctl#nerdctl-docker-compatible-cli-for-containerd) を参照してください。

<a id="network-management"></a>
### ネットワーク管理

#### 基本ネットワークインターフェイス
すべてのワーカーノードは、クラスター作成時に入力した VPC/サブネットに接続されるネットワークインターフェイスを持っています。この基本ネットワークインターフェイスの名前は「eth0」であり、ワーカーノードはこのネットワークインターフェイスを通じてコントロールプレーンと接続されます。

#### 追加ネットワークインターフェイス
クラスターまたはワーカーノードグループの作成時に追加ネットワークを設定すると、該当するワーカーノードグループのワーカーノードに追加ネットワークインターフェイスが作成されます。追加ネットワークインターフェイスは、追加ネットワーク設定に入力した順番でインターフェイス名が設定されます（eth1、eth2、...）。

#### Default Route の設定
ワーカーノードに複数のネットワークインターフェイスが存在する場合、各ネットワークインターフェイスごとにデフォルトルートが設定されます。1 つのシステムに複数のデフォルトルートが設定されている場合、メトリック値が最も低いデフォルトルートがシステムのデフォルトルートとして動作します。ネットワークインターフェイスごとのデフォルトルートは、インターフェイス番号が小さいほど低いメトリック値が設定されています。そのため、動作中のネットワークインターフェイスのうち、最も小さい番号のネットワークインターフェイスがシステムのデフォルトルートとして動作します。

システムのデフォルトルートを追加ネットワークインターフェイスに設定するには、以下の作業が必要です。

##### 1. ネットワークインターフェイスごとのメトリック設定の変更
ワーカーノードのすべてのネットワークインターフェイスは、DHCP サーバーを通じて IP アドレスを割り当てられます。DHCP サーバーから IP アドレスを割り当てられる際に、ネットワークインターフェイスごとのデフォルトルートが設定されます。このとき、各デフォルトルートのメトリック値はインターフェイスごとにあらかじめ設定されています。Linux ディストリビューションごとの保存場所および設定項目は次のとおりです。

* CentOS
    * 設定ファイルの場所: /etc/sysconfig/network-scripts/ifcfg-{ネットワークインターフェイス名}
    * メトリック値の設定項目: METRIC
* Ubuntu
    * 設定ファイルの場所: /etc/systemd/network/toastcloud-{ネットワークインターフェイス名}.network
    * メトリック値の設定項目: DHCP セクションの RouteMetric

> [注意]
> デフォルトルートごとのメトリック値は、デフォルトルートが設定されるタイミングで決定されます。
> そのため、変更された設定は次回のデフォルトルート設定時に適用されます。
> 現在システムに適用されているルートごとのメトリック値を変更するには、以下の「現在のルートのメトリック値の変更」を参照してください。

##### 2. 現在のルートのメトリック値の変更

システムのデフォルトルートを変更するために、ネットワークインターフェイスごとのデフォルトルートのメトリック値を調整できます。以下は、route コマンドを使用して各デフォルトルートのメトリック値を調整する例です。

以下は作業実行前の状態です。インターフェイス番号が小さいほどメトリック値が小さく設定されていることを確認できます。
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

eth1 についても、まず既存のルートを削除し、eth1 のメトリックを 0 に設定します。
```
# route del -net 0.0.0.0/0 dev eth1
# route add -net 0.0.0.0/0 gw 192.168.0.1 dev eth1 metric 0
```

再度ルートを照会すると、メトリック値が変更されていることを確認できます。
```
# route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         192.168.0.1     0.0.0.0         UG    0      0        0 eth1
0.0.0.0         10.0.0.1        0.0.0.0         UG    100    0        0 eth0
0.0.0.0         172.16.0.1      0.0.0.0         UG    200    0        0 eth2
...
```

#### ユーザースクリプト機能を使用したデフォルトルート設定の変更
ユーザースクリプト機能を使用すると、ノードの追加などによりノードが新たに初期化される際にも、上記の設定を維持できます。次のユーザースクリプトは、CentOS を使用するワーカーノードで eth0 のメトリック値を 100 に、eth1 のメトリック値を 0 に設定する例です。これにより、現在システムに適用されているデフォルトルートごとのメトリック値も変更され、ワーカーノードの再起動後も維持されます。
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
### kubelet カスタム引数設定機能
kubelet はすべてのワーカーノードで動作するノードエージェントです。kubelet はコマンドライン引数を使用してさまざまな設定を受け取ります。NKS が提供する kubelet カスタム引数設定機能を使用すると、kubelet 起動時に入力される引数を追加できます。kubelet カスタム引数は、次のように設定してシステムに適用できます。

* ワーカーノードの `/etc/kubernetes/kubelet-user-args` ファイルに `KUBELET_USER_ARGS="カスタム引数"` の形式でカスタム引数を入力します。
* `systemctl daemon-reload` コマンドを実行します。
* `systemctl restart kubelet` コマンドを実行します。
* `systemctl status kubelet` コマンドで kubelet が正常に動作していることを確認します。

> [注意]
> * この機能は、2023 年 11 月 28 日以降に新規作成されたクラスターでのみ動作します。
> * カスタム引数を設定する対象のワーカーノードごとに実行します。
> * 不正な形式のカスタム引数を入力した場合、kubelet が正常に動作しません。
> * 設定されたカスタム引数は、システム再起動後も引き続き適用されます。

<a id="containerd-registry-config"></a>
### カスタム containerd レジストリ設定機能（deprecated）

> [注意]
> この機能は Kubernetes v1.34 以降では動作しません。
> containerd 2.2 を使用する Kubernetes v1.34 以降では、hosts.toml ファイルを使用してレジストリごとの設定を適用できます。
> 詳細については、[Registry Configuration](https://github.com/containerd/containerd/blob/main/docs/hosts.md) を参照してください。

v1.24.3 以降の NKS クラスターは、コンテナランタイムとして containerd v1.6 を使用します。NKS では、containerd のさまざまな設定のうち、レジストリに関連する項目をユーザー環境に合わせて設定できる機能を提供しています。containerd v1.6 のレジストリ設定については、[Configure Image Registry](https://github.com/containerd/containerd/blob/release/1.6/docs/cri/registry.md) を参照してください。

ワーカーノードが初期化される過程で、カスタム containerd レジストリ設定ファイル（`/etc/containerd/registry-config.json`）が存在する場合、このファイルの内容を containerd 設定ファイル（`/etc/containerd/config.toml`）に適用します。カスタム containerd レジストリ設定ファイルが存在しない場合、containerd 設定ファイルにはデフォルトのレジストリ設定が適用されます。デフォルトのレジストリ設定の内容は次のとおりです。

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

#### 例1

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

#### 例2

`docker.io` レジストリを削除し、HTTP をサポートするレジストリのみ登録するには、次のように設定できます。
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

#### 例3

ノード作成時にカスタム containerd レジストリ設定ファイルを例2の内容で作成するには、ユーザースクリプトを次のように設定できます。

```bash
mkdir -p /etc/containerd
echo '[ { "registry": "user-defined.registry.io", "endpoint_list": [ "http://user-defined.registry.io" ], "tls": { "insecure_skip_verify": true } } ]' > /etc/containerd/registry-config.json
```

> [注意]
> * containerd 設定ファイル（`/etc/containerd/config.toml`）は NKS によって管理されるファイルです。このファイルを任意に変更すると、NKS の機能動作にエラーが発生したり、任意に変更した内容が削除されたりする場合があります。
> * カスタム containerd レジストリ設定機能で不正なレジストリが設定された場合、ワーカーノードが正常に動作しない可能性があります。
> * カスタム containerd レジストリ設定機能が containerd 設定ファイルに適用されるタイミングは、ワーカーノードの初期化過程です。ワーカーノードの初期化過程は、ワーカーノードの作成過程およびワーカーノードグループのアップグレード過程に含まれます。
>     * ワーカーノード作成時にカスタム container レジストリ設定機能を適用するには、ユーザースクリプトでこの設定ファイルを作成するようにする必要があります。
>     * ワーカーノードグループのアップグレード時にカスタム container レジストリ設定機能を適用するには、すべてのワーカーノードにこのファイルを手動で設定してからアップグレードを実施する必要があります。
> * カスタム containerd レジストリ設定ファイルが存在する場合、このファイルに設定された内容が containerd にそのまま適用されます。
>     * `docker.io` レジストリを使用するには、`docker.io` レジストリに関する設定も含める必要があります。`docker.io` レジストリの設定については、デフォルトのレジストリ設定を参照してください。
>     * `docker.io` レジストリを使用しない場合は、`docker.io` レジストリに関する設定を含めなければ問題ありません。ただし、1 つ以上のレジストリ設定が存在する必要があります。

<a id="constraints-on-cgroup"></a>
### Kubernetes バージョンと CGroup バージョンによる制約事項
CGroup（Control Group）は Linux カーネルの機能であり、プロセスグループの CPU、メモリ、ディスク I/O、ネットワークなどのシステムリソース使用量を制限、分離、モニタリングできます。Kubernetes を含むコンテナ技術の中核基盤の 1 つです。CGroup は最初のバージョン 1（v1）から始まり、メモリ・I/O 制御機能を強化してバージョン 2（v2）へと発展しました。Linux カーネルの機能であるため、CGroup v2 は Linux カーネルに依存します。そのため、比較的新しいディストリビューション/バージョンでのみ CGroup v2 がサポートされます。

NKS クラスター v1.34 以降では、ワーカーノードが CGroup v2 で動作する必要があります。これは、Kubernetes コミュニティが今後 containerd 1.x の代わりに containerd 2.x を使用し、CGroup v1 の代わりに v2 を基盤として動作するという方針から生まれた制約事項です。

NKS のワーカーノードは、次の場合に CGroup v2 で動作します。
* CGroup v2 に設定された OS イメージを使用してワーカーノードグループを作成した場合
* CGroup v1 に設定された OS イメージを使用してワーカーノードグループを作成した後、v1.34 へローリングアップグレードした場合

OS イメージのリリース日付に応じて、デフォルトの CGroup バージョンを確認できます。
* 2026/03/10 以前のリリースイメージ: CGroup v1
* 2026/03/10 以降のリリースイメージ: CGroup v2

OS イメージのデフォルト CGroup バージョンが v1 であっても、v2 に設定を変更できます。デフォルト CGroup バージョンが v1 の OS イメージを使用して作成したワーカーノードグループについて、次の場合にワーカーノードの CGroup バージョンを v1 から v2 に変更します。
* Kubernetes v1.34 へローリングアップグレード時
* Kubernetes v1.34 へローリングアップグレード後にワーカーノードを追加する際

CGroup バージョンを v1 から v2 に変更できる OS イメージのディストリビューションの種類とバージョンは次のとおりです。
* Ubuntu 22.04 以上
* Rocky 9.0 以上

> [注意]
> * ワーカーノードの CGroup 設定を v1 から v2 に変更する過程で、**ワーカーノードの再起動**が含まれます。
> * grub.conf の無断変更などによりノードの再起動ができない状況の場合、CGroup バージョンの変更が失敗するだけでなく、インスタンスが起動しない状況になる可能性があります。
> * インスタンスの再起動に問題がない状態で、ワーカーノードグループの Kubernetes バージョンのアップグレードを実施する必要があります。

デフォルト CGroup バージョンが v1 であり、CGroup バージョンを v2 に変更できない OS イメージで作成したワーカーノードグループは、ローリングアップグレード方式で Kubernetes v1.34 へアップグレードすることはできません。この場合、Blue-Green 方式でワーカーノードグループをアップグレードできます。

<a id="worker-management-caution"></a>
### ワーカーノード管理の注意事項
* ワーカーノードに pull されているコンテナイメージを任意に削除しないでください。NKS クラスターに必要な Pod が動作しない可能性があります。
* `shutdown`、`halt`、`poweroff` などのコマンドでシステムを任意に停止すると、コンソールから再起動できません。ワーカーノードの起動/停止機能を使用してください。
* ワーカーノード内のさまざまな設定ファイルを任意に変更したり、システムサービスを任意に操作したりしないでください。NKS クラスターに深刻な問題が発生する可能性があります。

<a id="cni"></a>

## CNI (Container Network Interface)
NHN Kubernetes Service (NKS) は、Addon 機能を通じてさまざまな種類の Container Network Interface (CNI) を提供します。クラスター作成時に Calico-VXLAN、Calico-eBPF、Cilium のいずれか 1 つの CNI を選択できます。デフォルトの設定は Calico-VXLAN です。Calico-eBPF は、コンテナワークロードを BGP ルーティングプロトコルで構成し、eBPF 技術をベースに直接通信します。一部の区間 (NodePort など) は VXLAN を使用して通信します。Calico の eBPF に関する詳細は [about eBPF](https://docs.tigera.io/calico/latest/about/kubernetes-training/about-ebpf) を参照してください。Cilium は VXLAN オーバーレイネットワークをベースとし、eBPF 技術を活用して高いネットワーク性能を提供します。Cilium の eBPF に関する詳細は [eBPF Datapath](https://docs.cilium.io/en/stable/network/ebpf/) を参照してください。

CNI ごとに選択可能な OS の制約は次のとおりです。

| CNI | 使用可能な OS |
| :-: | :-: |
| Flannel | Centos, Rocky, Red Hat, Ubuntu |
| Calico-VXLAN | Centos, Rocky, Red Hat, Ubuntu |
| Calico-eBPF | Rocky, Ubuntu |
| Cilium | Rocky, Ubuntu |

<a id="calico-cni-types"></a>
### Calico CNI の種類
NHN Kubernetes Service (NKS) が提供する Calico-VXLAN と Calico-eBPF の相違点は次のとおりです。

|  | Calico-VXLAN | Calico-eBPF |
| :-: | :-: | :-: |
| コンテナネットワーク処理モジュール | Linux カーネルネットワークスタック | eBPF + Linux カーネルネットワークスタック |
| kube-proxy | 有効 | 無効 (eBPF が kube-proxy を代替) |
| ネットワーク方式 | VXLAN | 直接通信 |
| Pod to Pod 通信 | VXLAN カプセル化により通信 | 直接通信<sup>[1](#footnote_calico_1)</sup> |
| Service ClusterIP to Pod 通信 | VXLAN カプセル化により通信 | 直接通信 |
| Service NodePort to Pod 通信 | VXLAN カプセル化により通信 | VXLAN カプセル化により通信 |
| ネットワークポリシーの適用 | iptables ベース | eBPF ベース (カーネルレベル) |
| ネットワーク性能 | VXLAN カプセル化による性能低下 | 直接通信による高い性能 (低レイテンシー) |

注釈

* <a name="footnote_calico_1">1</a>: パケットの送信元 IP と宛先 IP が Pod IP に設定されます。強化されたセキュリティルールを使用する場合、このトラフィックに対するセキュリティルールを別途設定する必要があります。

> [注意事項]
> Calico v3.24.1 の eBPF モードを使用するクラスターでは、Rocky 9.5 以降または Ubuntu 24.04 以降のイメージを使用するノードグループを作成できません。
> 該当イメージを使用するには、アドオン管理機能を通じて Calico を v3.28.2 以降にアップデートする必要があります。

<a id="security-group"></a>

## セキュリティグループ
クラスター作成時に強化されたセキュリティルールを True に設定すると、ワーカーノードのセキュリティグループ作成時に必須のセキュリティルールのみが作成されます。

<a id="mandatory-sg-rules"></a>
### クラスターワーカーノードの必須セキュリティルール

| 方向 | IP プロトコル | ポート範囲 | Ether | リモート | 説明 | 特記事項 |
| :-: | :-: | :-: | :-: | :-: | :-: | :-: |
| ingress | TCP | 10250 | IPv4 | ワーカーノード | kubelet ポート、方向: metrics-server (ワーカーノード) → kubelet (ワーカーノード) | |
| ingress | TCP | 10250 | IPv4 | NKS Control Plane | kubelet ポート、方向: kube-apiserver (NKS Control plane) → kubelet (ワーカーノード) | |
| ingress | TCP | 5473 | IPv4 | ワーカーノード | calico-typha ポート、方向: calico-node (ワーカーノード) → calico-typha (ワーカーノード) | CNI が Calico-VXLAN、Calico-eBPF の場合に作成される |
| ingress | TCP | 179 | IPv4 | ワーカーノード | calico-node BGP ポート、方向: pod (ワーカーノード) → pod (ワーカーノード) | CNI が Calico-eBPF の場合に作成される |
| ingress | TCP | 179 | IPv4 | NKS Control Plane | calico-node BGP ポート、方向: pod (NKS Control plane) → pod (ワーカーノード) | CNI が Calico-eBPF の場合に作成される |
| ingress | UDP | 8472 | IPv4 | ワーカーノード | flannel vxlan overlay network ポート、方向: pod (ワーカーノード) → pod (ワーカーノード) | CNI が flannel の場合に作成される |
| ingress | UDP | 8472 | IPv4 | ワーカーノード | flannel vxlan overlay network ポート、方向: pod (NKS Control plane) → pod (ワーカーノード) | CNI が flannel の場合に作成される |
| ingress | UDP | 4789 | IPv4 | ワーカーノード | calico-node vxlan overlay network ポート、方向: pod (ワーカーノード) → pod (ワーカーノード) | CNI が Calico-VXLAN、Calico-eBPF の場合に作成される |
| ingress | UDP | 4789 | IPv4 | NKS Control Plane | calico-node vxlan overlay network ポート、方向: pod (NKS Control plane) → pod (ワーカーノード) | CNI が Calico-VXLAN、Calico-eBPF の場合に作成される |
| ingress | TCP | 4240 | IPv4 | ワーカーノード | cilium-agent health check ポート、方向: cilium-agent (ワーカーノード) → cilium-agent (ワーカーノード) | CNI が Cilium の場合に作成される |
| ingress | ICMP | - | IPv4 | ワーカーノード | cilium ping health monitoring、方向: cilium-agent (ワーカーノード) → ワーカーノード | CNI が Cilium の場合に作成される |
| ingress | UDP | 8472 | IPv4 | ワーカーノード | cilium vxlan overlay network ポート、方向: pod (ワーカーノード) → pod (ワーカーノード) | CNI が Cilium の場合に作成される |
| ingress | UDP | 8472 | IPv4 | NKS Control Plane | cilium vxlan overlay network ポート、方向: pod (NKS Control plane) → pod (ワーカーノード) | CNI が Cilium の場合に作成される |
| egress | TCP | 2379 | IPv4 | NKS Control Plane | etcd ポート、方向: calico-kube-controller (ワーカーノード) → etcd (NKS Control plane) | |
| egress | TCP | 6443 | IPv4 | Kubernetes API エンドポイント | kube-apiserver ポート、方向: kubelet、kube-proxy (ワーカーノード) → kube-apiserver (NKS Control plane) | |
| egress | TCP | 6443 | IPv4 | NKS Control Plane | kube-apiserver ポート、方向: default kubernetes service (ワーカーノード) → kube-apiserver (NKS Control plane) | |
| egress | TCP | 5473 | IPv4 | ワーカーノード | calico-typha ポート、方向: calico-node (ワーカーノード) → calico-typha (ワーカーノード) | CNI が Calico-VXLAN、Calico-eBPF の場合に作成される |
| egress | TCP | 53 | IPv4 | ワーカーノード | DNS ポート、方向: ワーカーノード → 外部 | |
| egress | TCP | 443 | IPv4 | すべて許可 | HTTPS ポート、方向: ワーカーノード → 外部 | |
| egress | TCP | 80 | IPv4 | すべて許可 | HTTP ポート、方向: ワーカーノード → 外部 | |
| egress | TCP | 179 | IPv4 | ワーカーノード | calico-node BGP ポート、方向: pod (ワーカーノード) → pod (ワーカーノード) | CNI が Calico-eBPF の場合に作成される |
| egress | TCP | 179 | IPv4 | NKS Control Plane | calico-node BGP ポート、方向: pod (NKS Control plane) → pod (ワーカーノード) | CNI が Calico-eBPF の場合に作成される |
| egress | UDP | 8472 | IPv4 | ワーカーノード | flannel vxlan overlay network ポート、方向: pod (ワーカーノード) → pod (ワーカーノード) | CNI が flannel の場合に作成される |
| egress | UDP | 8472 | IPv4 | NKS Control Plane | flannel vxlan overlay network ポート、方向: pod (ワーカーノード) → pod (NKS Control plane) | CNI が flannel の場合に作成される |
| egress | UDP | 4789 | IPv4 | ワーカーノード | calico-node vxlan overlay network ポート、方向: pod (ワーカーノード) → pod (ワーカーノード) | CNI が Calico-VXLAN、Calico-eBPF の場合に作成される |
| egress | UDP | 4789 | IPv4 | NKS Control Plane | calico-node vxlan overlay network ポート、方向: pod (ワーカーノード) → pod (NKS Control plane) | CNI が Calico-VXLAN、Calico-eBPF の場合に作成される |
| egress | TCP | 4240 | IPv4 | ワーカーノード | cilium-agent health check ポート、方向: cilium-agent (ワーカーノード) → cilium-agent (ワーカーノード) | CNI が Cilium の場合に作成される |
| egress | ICMP | - | IPv4 | ワーカーノード | cilium ping health monitoring、方向: ワーカーノード → cilium-agent (ワーカーノード) | CNI が Cilium の場合に作成される |
| egress | UDP | 8472 | IPv4 | ワーカーノード | cilium vxlan overlay network ポート、方向: pod (ワーカーノード) → pod (ワーカーノード) | CNI が Cilium の場合に作成される |
| egress | UDP | 8472 | IPv4 | NKS Control Plane | cilium vxlan overlay network ポート、方向: pod (ワーカーノード) → pod (NKS Control plane) | CNI が Cilium の場合に作成される |
| egress | UDP | 53 | IPv4 | すべて許可 | DNS ポート、方向: ワーカーノード → 外部 | |

強化されたセキュリティルールを使用する場合、NodePort タイプのサービスと NHN Cloud NAS サービスで使用するポートのセキュリティルールは追加されていません。必要に応じて、以下のセキュリティルールを追加設定する必要があります。

| 方向 | IP プロトコル | ポート範囲 | Ether | リモート | 説明 |
| :-: | :-: | :-: | :-: | :-: | :-: |
| ingress, egress | TCP | 30000 - 32767 | IPv4 | すべて許可 | NKS service object NodePort、方向: 外部 → ワーカーノード |
| egress | TCP | 2049 | IPv4 | NHN Cloud NAS サービス IP アドレス | csi-nfs-node の rpc nfs ポート、方向: csi-nfs-node (ワーカーノード) → NHN Cloud NAS サービス |
| egress | TCP | 111 | IPv4 | NHN Cloud NAS サービス IP アドレス | csi-nfs-node の rpc portmapper ポート、方向: csi-nfs-node (ワーカーノード) → NHN Cloud NAS サービス |
| egress | TCP | 635 | IPv4 | NHN Cloud NAS サービス IP アドレス | csi-nfs-node の rpc mountd ポート、方向: csi-nfs-node (ワーカーノード) → NHN Cloud NAS サービス |

> [Calico-eBPF CNI 使用時の注意]
> Calico-eBPF CNI を使用する場合、Pod 間の通信およびノードから Pod への通信は、Pod に設定されたポートを通じて行われます。
> 強化されたセキュリティルールを使用する場合、該当 Pod ポートに対する ingress、egress のセキュリティルールを手動で追加する必要があります。

<a id="cilium-optional-security-group-rules"></a>
### Cilium CNI の選択的機能使用時の追加セキュリティグループルール

Cilium CNI を使用するクラスターで Hubble、Envoy、Prometheus などの選択的機能を有効にするには、該当機能に必要なセキュリティグループルールを追加で設定する必要があります。

##### 選択的機能ごとに必要なポート

| 機能 | 方向 | IP プロトコル | ポート範囲 | リモート | 説明 |
| :-: | :-: | :-: | :-: | :-: | :-: |
| Hubble Observability | ingress, egress | TCP | 4244 | ワーカーノード | hubble server ポート、方向: hubble-relay (ワーカーノード) → hubble-server (ワーカーノード) |
| Hubble UI | ingress, egress | TCP | 4245 | ワーカーノード | hubble relay ポート、方向: hubble-ui (ワーカーノード) → hubble-relay (ワーカーノード) |
| Cilium Agent Metrics | ingress, egress | TCP | 9962 | ワーカーノード | cilium-agent prometheus metrics ポート |
| Cilium Operator Metrics | ingress, egress | TCP | 9963 | ワーカーノード | cilium-operator prometheus metrics ポート |
| Cilium Envoy Metrics | ingress, egress | TCP | 9964 | ワーカーノード | cilium-envoy prometheus metrics ポート |
| WireGuard 暗号化 | ingress, egress | UDP | 51871 | ワーカーノード | WireGuard transparent encryption ポート |
| IPsec 暗号化 | ingress, egress | UDP | 500 | ワーカーノード | IPsec IKE ポート |
| IPsec 暗号化 | ingress, egress | UDP | 4500 | ワーカーノード | IPsec NAT-T ポート |
| IPsec 暗号化 | ingress, egress | ESP (50) | - | ワーカーノード | IPsec ESP プロトコル |

> [注記]
> Cilium の基本インストールには、上記の選択的機能は含まれていません。
> 選択的機能を使用するには、Cilium の設定変更および該当機能に必要なセキュリティグループルールを手動で追加する必要があります。

<a id="relaxd-sg-rules"></a>
### 強化されたセキュリティルールを使用しない場合に作成されるルール

強化されたセキュリティルールを使用しない場合、NodePort タイプのサービスおよび外部ネットワーク通信に必要なセキュリティルールが追加で作成されます。

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

## アドオン管理機能
アドオンとは、Kubernetes クラスターの必須コンポーネントではありませんが、NKS クラスターの機能を拡張したり、特化した機能を提供するために提供されるコンポーネントのことです。アドオンには、ネットワーキング、サービスディスカバリー、モニタリング、ストレージプロビジョニングなどの機能を担うコンポーネントが含まれる場合があります。ユーザーはアドオン管理機能を通じて、NHN Cloud が提供するアドオンをクラスターにインストール/変更/削除できます。

> [注意]
> NKS レジストリが有効化されていないクラスターでは、アドオン管理機能を使用できません。

<a id="addon-mgmt-operation"></a>
### 動作方式
アドオン管理機能の動作方式について説明します。

#### Server-side apply
アドオン管理機能を使用してクラスターにアドオンをインストール/変更する際は、Kubernetes の Server-side apply を使用します。Client-side apply は、クライアントがローカルでリソースの状態を計算し、リソース全体を API サーバーに送信する方式です。一方、Server-side apply は API サーバーがリソースのマージおよびフィールドの所有権管理を行い、API サーバーがリソースのマージと競合検出を実行できます。Server-side apply の詳細については、[Server-Side Apply](https://kubernetes.io/docs/reference/using-api/server-side-apply/) を参照してください。


#### 競合処理オプション
ユーザーがアドオンの管理対象フィールドを変更して使用している場合、アドオンのインストール/変更時に競合が発生する可能性があります。ユーザーはアドオンのインストール/変更時に適切な競合処理オプション (resolve-conflicts) を選択して競合状況を管理できます。アドオン管理機能で提供する競合処理オプションは次のとおりです。

* なし (none): 競合が発生した場合、インストール/変更は適用されず、インストール/変更リクエストは失敗として処理されます。
* 上書き (overwrite): 競合が発生した場合、競合しているフィールドをアドオンで定義されているデフォルト値で上書きします。
* 保持 (preserve): 競合が発生した場合、競合しているフィールドを既存の値で保持します。

> [バージョン変更時の注意事項]
> アドオンのバージョン変更時に、必須コンポーネントのデフォルト設定値が変更される場合があります。ユーザーが該当フィールドを直接変更していない場合でも競合が発生する可能性があり、競合処理オプションを「なし」または「保持」に選択した場合、アドオンのインストール/変更が失敗する可能性があります。競合処理オプションを「上書き」に選択することで競合を防ぐことができます。

> [保持オプションに関する注意事項]
> アドオンを構成するリソースのすべての変更を保持することはできません。
> 保持できないフィールドで競合が発生した場合、インストール/変更の処理は失敗として処理されます。

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
> 代わりに、アドオン変更機能を使用して各アドオンのバージョンを変更できます。

#### アドオン管理機能の有効化
アドオン管理機能が有効化されていない既存クラスターでも、アドオン管理機能を使用できます。アドオンが設定されていないクラスターは、calico や coredns などが動作していても、アドオンがインストールされていないと表示されます。この状態で各アドオンをインストールすると、以降はアドオン管理機能を通じてアドオンを管理できます。アドオンを構成するリソースの設定を変更して使用している場合、競合処理オプションを「保持」に選択してインストールすると、既存リソースの設定を維持できます。

<a id="addon-mgmt-types"></a>
### アドオンタイプ
アドオンタイプは、クラスターにインストールされるアドオンを特性に応じて分類したものです。

| タイプ | 必須 | 説明 |
|---|---|---|
| CNI | O | クラスターにインストールされる CNI に該当するタイプです。 |
| kube-dns | O | NKS クラスター内で動作するデフォルト DNS サーバーです。 |
| cinder-csi-plugin | X | NHN Cloud のブロックストレージをプロビジョニングおよび管理できる CSI ドライバーです。 |
| metrics-server | X | オートスケーリングとモニタリングのために、ノードと Pod からリソース使用指標を収集する Kubernetes コンポーネントです。 |
| snapshot-controller | X | ボリュームスナップショットの作成、削除、PVC 連携を含むライフサイクルを管理する Kubernetes コンポーネントです。 |
| nfs-csi-plugin | X | NHN Cloud の NFS をプロビジョニングおよび管理できる CSI ドライバーです。 |

<a id="addon-mgmt-addon-list"></a>
### アドオン一覧

<a id="addon-mgmt-addon-calico"></a>
#### Calico
Calico は Kubernetes のネットワーキングとネットワークセキュリティを提供する CNI プラグインです。NHN Cloud が提供する Calico の説明については、[Calico CNI 種類](#calico_cni_types) を参照してください。

* タイプ: CNI
* オプション
    * mode
        * Calico の動作モードを決定します。
        * サポートする動作モード: vxlan、ebpf
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
    * v3.30.2-nks1
    * v3.30.2-nks2: アドオン管理機能の安定性を強化しました。
    * v3.30.2-nks3: konnectivity 環境をサポートします。
    * v3.31.4-nks1: データストアは KDD (Kubernetes Datastore Driver) で、konnectivity 環境をサポートします。

> [注記]
> * konnectivity をサポートするプラットフォームバージョン (1.202605.0 以上) でインストール/更新可能な calico バージョンは次のとおりです。
>     * v3.28.2-nks3 以上
>     * v3.30.2-nks3 以上
>     * v3.31.4 以上

<a id="addon-mgmt-addon-calico-datastore"></a>
##### Calico のデータストア
calico は pod IP、ノードごとの IP 帯域など、さまざまな情報をデータストアに保存します。既存で提供されていたバージョンではデータストアに etcd を使用していましたが、新規提供バージョンではデータストアに KDD (Kubernetes Datastore Driver) を使用します。KDD は Kubernetes CRD を使用して各種情報を Kubernetes レベルのリソース/オブジェクトに保存します。KDD を使用するとネットワークトポロジーがシンプルになり、関連情報がすべて CR として公開されるため、管理上の利点があります。

以下のバージョンはデータストアに etcd を使用します。
* v3.28.2
* v3.30.2

以下のバージョンはデータストアに KDD を使用します。
* v3.31.4 以上

> [注意]
> * データストアを etcd → KDD に変更するアドオン更新時の競合オプションは「上書き (overwrite)」のみサポートします。
> * データストアを KDD → etcd に変更するアドオン更新はサポートされていません。

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

<a id="addon-mgmt-addon-coredns"></a>
#### CoreDNS
CoreDNS は Kubernetes クラスターのデフォルト DNS サーバーです。

* タイプ: kube-dns
* オプション: なし
* ユーザー変更不可リソースおよびフィールド
    * Deployment/coredns、ネームスペース kube-system
        * .spec.template.spec.containers[name="coredns"].image'
* サポートバージョン一覧
    * 1.8.4-nks1
    * 1.8.4-nks2
        * アドオン管理機能の安定性を強化しました。
        * ユーザー変更不可リソースおよびフィールドを調整しました。
            * Deployment/coredns、ネームスペース kube-system
                * .metadata.labels.k8s-app を削除
                * .metadata.labels.kubernetes.io/name を削除
                * .spec.template.spec.nodeSelector を削除
                * .spec.template.spec.serviceAccountName を削除


<a id="addon-mgmt-addon-cinder-csi-plugin">
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
    * v1.27.102-nks1
    * v1.27.102-nks2: 内部コンテナバージョンが変更されました。
        * csi-attacher: v3.0.2 → v3.3.0
        * csi-provisioner: v2.0.4 → v2.2.2
        * csi-snapshotter: v3.0.2 → v3.0.3
        * csi-resizer: v1.0.1 → v1.3.0
        * csi-node-driver-registrar: v2.0.1 → v2.3.0
    * v1.27.102-nks3: アドオン管理機能の安定性を強化しました。

<a id="adoon-mgmt-addon-metrics-server">
#### Metrics Server
Metrics Server は、オートスケーリングとモニタリングのために、ノードと Pod からリソース使用指標を収集する Kubernetes コンポーネントです。

* タイプ: metrics-server
* オプション: なし
* ユーザー変更不可リソースおよびフィールド
    * Deployment/metrics-server、ネームスペース kube-system
        * .spec.template.spec.containers[name="metrics-server"].image
* サポートバージョン一覧
    * v0.4.4-nks1
    * v0.4.4-nks2: アドオン管理機能の安定性を強化しました。

<a id="addon-mgmt-addon-snapshot-controller">
#### Snapshot Controller
Snapshot Controller は、ボリュームスナップショットの作成、削除、PVC 連携を含むライフサイクルを管理する Kubernetes コンポーネントです。

* タイプ: snapshot-controller
* オプション: なし
* ユーザー変更不可リソースおよびフィールド
    * Deployment/snapshot-controller、ネームスペース kube-system
        * .spec.template.spec.containers[name="snapshot-controller"].image
* サポートバージョン一覧
    * v4.1.1-nks1
    * v4.1.1-nks2: アドオン管理機能の安定性を強化しました。

<a id="addon-mgmt-addon-nfs-csi-plugin">
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
        * ユーザー変更不可リソース/フィールドを検証しない問題を修正しました。
    * v1.0.2-nks1
        * 任意項目である snapshot 設定が必須として要求されていた問題を修正しました。

<a id="loadbalancer-service"></a>

## LoadBalancer サービス
Kubernetes アプリケーションの基本実行単位である Pod は、CNI (container network interface) によってクラスターネットワークに接続されます。デフォルトでは、クラスター外部から Pod へアクセスすることはできません。Pod のサービスをクラスター外部に公開するには、Kubernetes の `LoadBalancer` サービス (Service) オブジェクト (object) を使用して外部に公開するパスを作成する必要があります。LoadBalancer サービスオブジェクトを作成すると、クラスター外部に NHN Cloud Load Balancer が作成され、サービスオブジェクトと紐付けられます。

<a id="create-webserver-pod"></a>

### Web サーバー Pod の作成
次のように、2 つの nginx Pod を実行するデプロイメント (deployment) オブジェクトのマニフェストファイルを作成し、オブジェクトを生成します。

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

### LoadBalancer サービスの作成
Kubernetes のサービスオブジェクトを定義するには、次の項目で構成されたマニフェストが必要です。

| 項目 | 説明 |
| --- | --- |
| metadata.name | サービスオブジェクトの名前 |
| spec.selector | サービスオブジェクトに関連付けるPodの名前 |
| spec.ports | 外部ロードバランサーから受信するトラフィックをPodに転送するインターフェース設定 |
| spec.ports.name | インターフェースの名前 |
| spec.ports.protocol | インターフェースで使用するプロトコル（例：TCP） |
| spec.ports.port | サービスオブジェクトの外部に公開するポート番号 |
| spec.ports.targetPort | サービスオブジェクトに関連付けるPodのポート番号 |
| spec.type | サービスオブジェクトの種類 |

次のようにサービスマニフェストを作成します。この LoadBalancer サービスオブジェクトは、**spec.selector** に定義された名前に従い、`app: nginx` ラベルが付いた Pod と関連付けられます。また、**spec.ports** に定義されたとおり、TCP/8080 ポートで受信したトラフィックをPodの TCP/80 ポートに転送します。

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

LoadBalancer サービスオブジェクトを作成すると、クラスターの外部にロードバランサーを作成して接続するまでに少し時間がかかります。外部ロードバランサーと接続される前は、**EXTERNAL-IP** 項目が `<pending>` と表示されます。

```
$ kubectl apply -f service.yaml
service/nginx-svc created

$ kubectl get service
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
nginx-svc    LoadBalancer   10.254.134.18   <pending>     8080:30013/TCP   11s
```

外部ロードバランサーと接続されると、**EXTERNAL-IP** 項目に IP が表示されます。この IP は外部ロードバランサーのフローティング IP です。

```
$ kubectl get service
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)          AGE
nginx-svc    LoadBalancer   10.254.134.18   123.123.123.30   8080:30013/TCP   3m13s
```

> [注記]
> 作成されたロードバランサーは **Network > Load Balancer** ページで確認できます。
> ロードバランサーの IP は外部からアクセス可能なフローティング IP です。**Network > Floating IP** ページで確認できます。

<a id="internet-test-via-service"></a>

### インターネットを介したサービステスト
ロードバランサーに接続されたフローティング IP に HTTP リクエストを送信し、Kubernetes クラスターの Web サーバー Pod が応答するかを確認します。サービスオブジェクトの TCP/8080 ポートを Pod の TCP/80 ポートに接続するよう設定しているため、TCP/8080 ポートにリクエストを送信する必要があります。外部ロードバランサーとサービスオブジェクト、Pod が正しく接続されていれば、Web サーバーは nginx のデフォルトページを返します。

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

### ロードバランサーの詳細オプション設定
Kubernetes のサービスオブジェクトを定義する際に、ロードバランサーのさまざまなオプションを設定できます。設定可能な項目は次のとおりです。

* ロードバランサー名の設定
* keep-alive タイムアウトの設定
* ロードバランサータイプの設定
* 静的ルートの設定
* セッション持続性の設定
* フローティング IP アドレスの保持設定
* ロードバランサー IP の設定
* フローティング IP の使用有無の設定
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
* ヘルスチェック最大リトライ回数の設定
* ヘルスチェックポートの設定
* ヘルスチェックホストヘッダーの設定
* L7 ルールおよび条件

#### グローバル設定とリスナーごとの設定
設定項目ごとに、グローバル設定とリスナーごとの設定が可能です。グローバル設定とリスナーごとの設定のどちらも存在しない場合は、各設定のデフォルト値を使用します。

* リスナーごとの設定: 対象のリスナーにのみ適用される設定です。
* グローバル設定: 対象のリスナーにリスナーごとの設定がない場合に適用される設定です。

#### リスナーごとの設定形式
リスナーごとの設定は、グローバル設定のキーにリスナーを示すプレフィックスを付けて設定できます。リスナーを示すプレフィックスは、サービスオブジェクトのポートプロトコル（`spec.ports[].protocol`）とポート番号（`spec.ports[].port`）をダッシュ（`-`）で連結したものです。たとえば、プロトコルが TCP でポート番号が 80 の場合、プレフィックスは `TCP-80` です。このポートに接続されるリスナーにセッション持続性を設定したい場合は、.metadata.annotations 配下の TCP-80.loadbalancer.nhncloud/pool-session-persistence に設定できます。

次のマニフェストは、グローバル設定とリスナーごとの設定を混在させた例です。

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
    
    # リスナーごとの設定
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
| ロードバランシング方式 | SOURCE_IP | LEAST_CONNECTIONS | TCP-80 リスナーはグローバル設定に従い SOURCE_IP に設定<br>TCP-443 リスナーはリスナーごとの設定に従い LEAST_CONNECTIONS に設定 |
| セッション持続性 | SOURCE_IP | None | TCP-80 リスナーはリスナーごとの設定に従い SOURCE_IP に設定<br>TCP-443 リスナーはデフォルト値に従い None に設定 |
| リスナープロトコル | HTTP | TCP | TCP-80 リスナーと TCP-443 リスナーはいずれもリスナーごとの設定に従い設定 |

> [注記]
> 別途記載のない機能は、Kubernetes v1.19.13 以降のバージョンのクラスターにのみ適用できます。
> Kubernetes v1.19.13 バージョンのクラスターは、2022年1月25日以降に作成されたクラスターに限り、リスナーごとの設定が適用されます。
>

> [注意]
> 以下の機能の設定値はすべて文字列形式で入力する必要があります。YAML ファイルの入力形式において、入力値の形式に関わらず文字列として入力するには、入力値をダブルクォーテーション（"）で囲んでください。YAML ファイル形式の詳細については、[Yaml Cookbook](https://yaml.org/YAML_for_ruby.html) を参照してください。
>

<a id="loadbalancer-update-without-modification"></a>

#### 設定を変更せずにロードバランサーを更新する方法

証明書の更新など、ロードバランサーの設定変更なしにロードバランサーの更新が必要な場合は、次のコマンドを使用できます。

```
# 下記コマンドで annotation を設定
kubectl annotate svc <name> loadbalancer.nhncloud/force-reconcile=true
```
ロードバランサーの更新が開始されると、上記コマンドで設定した annotation は自動的に削除されます。

> [注意]
> この機能は、プラットフォームバージョンが 1.202605.0 以上のクラスターで動作します。

#### ロードバランサー名の設定

ロードバランサーの名前を設定できます。

* 設定場所は .metadata.annotations 配下の loadbalancer.nhncloud/loadbalancer-name です。
* リスナーごとの設定は適用できません。
* 英字、数字、「-」、「_」のみ入力できます。
    * 無効な文字が含まれている場合は、デフォルトのロードバランサー名形式に従ってロードバランサー名が設定されます。
    * デフォルトのロードバランサー名形式: "kube_service_{CLUSTER_UUID}\_{SERVICE_NAMESPACE}_{SERVICE_NAME}"
* 最大長は 255 文字で、最大長を超えた場合はロードバランサー名が 255 文字に切り詰められます。

> [注意]
> 次の操作を行うと、ロードバランサーの重大な誤動作を引き起こす可能性があります。
> * サービスオブジェクトの作成後にロードバランサー名を変更する
> * プロジェクト内に同じ名前のロードバランサーを作成する

#### ロードバランサータイプの設定
ロードバランサーのタイプを設定できます。ロードバランサーの詳細については、[ロードバランサーコンソール使用ガイド](/Network/Load%20Balancer/ja/console-guide/)を参照してください。

* 設定場所は .metadata.annotations 配下の loadbalancer.nhncloud/loadbalancer-type です。
* リスナーごとの設定は適用できません。
* 次のいずれかに設定できます。
    * shared: 「一般」タイプのロードバランサーを作成します。未設定時のデフォルト値です。
    * dedicated: 「専用」タイプのロードバランサーを作成します。

#### 静的ルートの設定
ロードバランサーへの静的ルートの適用有無を設定できます。

* 設定場所は .metadata.annotations 配下の loadbalancer.nhncloud/apply-subnet-host-routes です。
* リスナーごとの設定は適用できません。
* 次のいずれかに設定できます。
    * true: 静的ルートを適用します。
    * false: 静的ルートを適用しません。未設定時のデフォルト値です。

> [注意]
> 静的ルートの設定は、2024年8月27日以降に作成されたか、k8s バージョンをアップグレードしたクラスターで設定できます。

#### セッション持続性の設定
ロードバランサーのセッション持続性を設定できます。

* 設定場所は .metadata.annotations 配下の loadbalancer.nhncloud/pool-session-persistence です。
* リスナーごとの設定を適用できます。
* 次のいずれかに設定できます。
    * 空文字列（""）: セッション持続性を「なし」に設定します。未設定時のデフォルト値です。
    * SOURCE_IP: セッション持続性を SOURCE_IP に設定します。
* ロードバランシング方式が SOURCE_IP の場合、セッション持続性の設定は無視され、セッション持続性は「なし」に設定されます。
* v1.17.6、v1.18.19 クラスター
    * ロードバランサー作成後は変更できません。
* v1.19.13 以降のクラスター
    * ロードバランサー作成後も変更できます。

#### フローティング IP アドレスの保持設定
ロードバランサーにはフローティング IP が接続されています。ロードバランサーの削除およびフローティング IP の変更時に、ロードバランサーに接続されたフローティング IP を削除するか保持するかを設定できます。

* 設定場所は .metadata.annotations 配下の loadbalancer.openstack.org/keep-floatingip です。
* リスナーごとの設定は適用できません。
* 次のいずれかに設定できます。
    * true: フローティング IP を保持します。
    * false: フローティング IP を削除します。未設定時のデフォルト値です。

> [注記]
> フローティング IP アドレスの保持が設定されていない場合（デフォルト値 false）、ロードバランサーの削除またはフローティング IP の変更時に、次の条件をすべて満たすフローティング IP は自動的に削除されます。
> 
> * サービスオブジェクト作成時に自動で作成されたフローティング IP である場合
> * フローティング IP に削除保護が設定されていない場合
> 
> 上記条件に該当しないフローティング IP は、フローティング IP アドレスの保持設定に関わらず削除対象にはなりません。

> [注意]
> 2021年10月26日以前に作成された v1.18.19 クラスターは、ロードバランサーが削除される際にフローティング IP が削除されない問題があります。カスタマーセンターの 1:1 お問い合わせよりご連絡いただければ、この問題を解決するための手順について詳しくご案内します。

#### ロードバランサー IP の設定
ロードバランサーを作成する際に、ロードバランサーの IP を設定できます。

* 設定場所は .spec.loadBalancerIP です。
* リスナーごとの設定は適用できません。
* 次のいずれかに設定できます。
  * 空文字列（""）: ロードバランサーに自動で作成されるフローティング IP を接続します。未設定時のデフォルト値です。
  * <Floating_IP>: ロードバランサーに既存のフローティング IP を接続します。すでに割り当て済みで未接続のフローティング IP がある場合に使用できます。

次は、ロードバランサーにカスタムフローティング IP を接続するマニフェストの例です。

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

#### フローティング IP の使用有無の設定
ロードバランサー作成時に、フローティング IP の使用有無を設定できます。

* 設定場所は .metadata.annotations 配下の service.beta.kubernetes.io/openstack-internal-load-balancer です。
* リスナーごとの設定は適用できません。
* 次のいずれかに設定できます。
  * true: フローティング IP を使用せず、VIP（Virtual IP）を使用します。
  * false: フローティング IP を使用します。未設定時のデフォルト値です。
* VIP を使用する場合、.spec.loadBalancerIP 項目を併せて設定することで、ロードバランサーに自動で作成される VIP を接続する代わりに、VIP を指定して接続できます。

次は、ロードバランサーにカスタム VIP を接続するマニフェストの例です。

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

フローティング IP の使用有無の設定とロードバランサー IP の設定の組み合わせにより、次のように動作します。

| フローティング IP 使用有無の設定 | ロードバランサー IP の設定 | 説明 |
| --- | --- | --- |
| false | 未設定 | ロードバランサーにフローティング IP を作成して接続します。 |
| false | 設定 | ロードバランサーに指定されたフローティング IP を接続します。 |
| true | 未設定 | ロードバランサーに接続される VIP を自動で設定します。 |
| true | 設定 | ロードバランサーに指定された VIP を接続します。 |

#### VPC の設定
ロードバランサー作成時に、ロードバランサーを接続する VPC を設定できます。

* 設定場所は .metadata.annotations 配下の loadbalancer.openstack.org/network-id です。
* リスナーごとの設定は適用できません。
* 設定しない場合は、クラスター作成時に設定した VPC が使用されます。

#### サブネット設定
ロードバランサー作成時に、ロードバランサーが接続されるサブネットを設定できます。設定したサブネットにロードバランサーのプライベート IP が接続されます。メンバーサブネットの設定がない場合、このサブネットに接続されたワーカーノードがロードバランサーのメンバーとして追加されます。

* 設定場所は .metadata.annotaions 配下の loadbalancer.openstack.org/subnet-id です。
* リスナーごとの設定は適用できません。
* 設定しない場合、クラスター作成時に設定したサブネットが使用されます。

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

#### メンバーサブネット設定
ロードバランサー作成時に、ロードバランサーのメンバーが接続されるサブネットを設定できます。このサブネットに接続されたワーカーノードがロードバランサーのメンバーとして追加されます。

* 設定場所は .metadata.annotaions 配下の loadbalancer.nhncloud/member-subnet-id です。
* リスナーごとの設定は適用できません。
* 設定しない場合、ロードバランサーのサブネット設定値が適用されます。
* メンバーサブネットは、必ずロードバランサーのサブネットと同じ VPC に含まれている必要があります。
* 2 つ以上のメンバーサブネットを設定する場合は、カンマ区切りのリストで入力します。

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
> * インスタンスのネットワークインターフェースのサブネット設定
>     * eth0: サブネット#1
>     * eth1: サブネット#2（メンバー）
>
> この場合、インスタンスの eth1 の IP アドレスがメンバーとして登録されます。ロードバランサーから送信されたヘルスチェックパケットはインスタンスの eth1 で受信し、eth0 を通じて送信を試みます。このとき、eth0 から送信するパケットのソース IP アドレスが eth0 の IP アドレスと異なります。eth0 のネットワークインターフェースでソース/宛先確認機能が有効になっている場合、このパケットは送信されずに破棄されます。このような構成では、eth0 のネットワークインターフェースのソース/宛先確認機能を無効にする必要があり、そうすることでメンバーが正常に動作します。ソース/宛先確認機能の詳細については、[ソース/宛先確認の変更](/Network/Network%20Interface/ja/console-guide/#_4)を参照してください。
>
> **例2.**
>
> * ロードバランサーのサブネット: サブネット#1
> * ロードバランサーのメンバーサブネット: サブネット#2
> * インスタンスのネットワークインターフェースのサブネット設定
>     * eth0: サブネット#3
>     * eth1: サブネット#2（メンバー）
>
> この場合、インスタンスの eth1 の IP アドレスがメンバーとして登録されます。ロードバランサーから送信されたヘルスチェックパケットはインスタンスの eth1 で受信します。応答パケットをロードバランサーの VIP に送信する必要がありますが、サブネット#1 が直接接続されたネットワークではないため、ルーティングテーブルによって送信インターフェースが決定されます。ネットワークインターフェースのソース/宛先確認機能を設定せずに通信を可能にするには、ロードバランサーの VIP に向かうトラフィックを eth1 を通じて送信できるようにルーティングを設定する必要があります。

> [注意]
> メンバーサブネットは、2023 年 11 月 28 日以降に v1.24.3 以上のバージョンにアップグレードされたか、新規作成されたクラスターで設定できます。

#### リスナー接続制限設定
リスナーの接続制限を設定できます。

* 設定場所は .metadata.annotations 配下の loadbalancer.nhncloud/connection-limit です。
* リスナーごとの設定を適用できます。
* v1.17.6、v1.18.19 クラスター
    * 最小値は 1、最大値は 60000 です。
    * 設定しない場合は -1 に設定され、実際にロードバランサーに適用される値は 2000 です。
* v1.19.13 以降のクラスター
    * 最小値は 1、最大値は 60000 です。
    * 設定しない場合、または範囲外の値を入力した場合は、デフォルト値の 60000 に設定されます。

#### リスナープロトコルの設定
リスナーのプロトコルを設定できます。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/listener-protocol です。
* リスナーごとに設定を適用できます。
* 次のいずれかを設定できます。
    * TCP: 未設定時のデフォルト値です。
    * HTTP
    * HTTPS
    * TERMINATED_HTTPS: TERMINATED_HTTPS として設定します。SSL バージョン、証明書、秘密鍵の情報を追加設定する必要があります。

> [注意]
> リスナープロトコルの設定は、サービスオブジェクトを変更してもロードバランサーには適用されません。
> リスナープロトコルの設定を変更するには、サービスオブジェクトを削除してから再作成する必要があります。
> この場合、ロードバランサーが削除された後に再作成されますのでご注意ください。


SSL バージョンは次のように設定できます。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/listener-terminated-https-tls-version です。
* リスナーごとに設定を適用できます。
* 次のいずれかを設定できます。
    * TLSv1.3: 未設定時のデフォルト値です。
    * TLSv1.2
    * TLSv1.1
    * TLSv1.0_2016
    * TLSv1.0
    * SSLv3

> [注意]
> TLSv1.3 は、2022 年 3 月 29 日以降に作成されたクラスターで設定できます。

証明書情報は次のように設定できます。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/listener-terminated-https-cert です。
* リスナーごとに設定を適用できます。
* 開始行および終了行を含める必要があります。

秘密鍵情報は次のように設定できます。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/listener-terminated-https-key です。
* リスナーごとに設定を適用できます。
* 開始行および終了行を含める必要があります。

以下は、リスナープロトコルを TERMINATED_HTTPS に設定する際のマニフェストの例です。証明書情報と秘密鍵情報は一部省略されています。
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

証明書情報と秘密鍵情報をマニフェストに登録する代わりに、Certificate Manager に登録された証明書を使用して TERMINATED_HTTPS タイプのリスナーを作成できます。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/listener-terminated-https-cert-manager-name です。
* 設定値は Certificate Manager に登録した証明書の名前です。
* リスナーごとに設定を適用できます。

以下は、リスナープロトコルを TERMINATED_HTTPS に設定する際に Certificate Manager に登録された証明書を使用するマニフェストの例です。

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
> Certificate Manager に登録された証明書を使用する方法は、2024 年 5 月 28 日以降に作成されたか、k8s バージョンをアップグレードしたクラスターで設定できます。
> リスナーと連携している Certificate Manager の証明書を削除すると、ロードバランサーの動作に影響を与える可能性があります。

#### リスナープロキシプロトコル (Proxy Protocol) の設定
リスナープロトコルが TCP または HTTPS の場合、リスナーにプロキシプロトコルを設定できます。プロキシプロトコルの詳細については、[ロードバランサープロキシモード](/Network/Load%20Balancer/ja/overview/#_4)を参照してください。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/proxy-protocol です。
* リスナーごとに設定を適用できます。
* 次のいずれかを設定できます。
    * true: プロキシプロトコルを有効にします。
    * false: プロキシプロトコルを無効にします。未設定時のデフォルト値です。

#### ロードバランシング方式の設定
ロードバランシング方式を設定できます。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/pool-lb-method です。
* リスナーごとに設定を適用できます。
* 次のいずれかを設定できます。
    * ROUND_ROBIN: 未設定時のデフォルト値です。
    * LEAST_CONNECTIONS
    * SOURCE_IP

#### ヘルスチェックプロトコルの設定
ヘルスチェックプロトコルを設定できます。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/healthmonitor-type です。
* リスナーごとに設定を適用できます。
* 次のいずれかを設定できます。
    * HTTP: HTTP URL、HTTP メソッド、HTTP ステータスコードを追加設定する必要があります。
    * HTTPS: HTTP URL、HTTP メソッド、HTTP ステータスコードを追加設定する必要があります。
    * TCP: 未設定時のデフォルト値です。

HTTP URL は次のように設定できます。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/healthmonitor-http-url です。
* リスナーごとに設定を適用できます。
* 設定値は / で始まる必要があります。
* 設定しない場合またはルールに合わない値を入力した場合、デフォルト値の / が設定されます。

HTTP メソッドは次のように設定できます。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/healthmonitor-http-method です。
* リスナーごとに設定を適用できます。
* 現在 GET のみサポートしており、設定しない場合または別の値を入力した場合、デフォルト値の GET が設定されます。

HTTP ステータスコードは次のように設定できます。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/healthmonitor-http-expected-code です。
* リスナーごとに設定を適用できます。
* 単一値 (例: 200)、リスト (例: 200,202)、範囲 (例: 200-204) の形式で入力できます。
* 設定しない場合またはルールに合わない値を入力した場合、デフォルト値の 200 が設定されます。

#### ヘルスチェック間隔の設定
ヘルスチェックの間隔を設定できます。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/healthmonitor-delay です。
* リスナーごとに設定を適用できます。
* 秒単位で設定します。
* 最小値 1、最大値 5000 です。
* 設定しない場合または範囲外の値を入力した場合、デフォルト値の 60 が設定されます。

#### ヘルスチェック最大応答時間の設定
ヘルスチェックの最大応答時間を設定できます。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/healthmonitor-timeout です。
* リスナーごとに設定を適用できます。
* 秒単位で設定します。
* 最小値 1、最大値 5000 です。
* この設定は必ずヘルスチェック間隔の設定値より小さい必要があります。
* 設定しない場合または範囲外の値を入力した場合、デフォルト値の 30 が設定されます。
* ただし、入力値または設定値がヘルスチェック間隔の設定より大きい場合、ヘルスチェック間隔の設定の 1/2 が設定されます。

#### ヘルスチェック最大再試行回数の設定
ヘルスチェックの最大再試行回数を設定できます。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/healthmonitor-max-retries です。
* リスナーごとに設定を適用できます。
* 最小値 1、最大値 10 です。
* 設定しない場合または範囲外の値を入力した場合、デフォルト値の 3 が設定されます。

#### ヘルスチェックポートの設定
ヘルスチェックの対象となるメンバーポートを設定できます。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/healthmonitor-health-check-port です。
* リスナーごとに設定を適用できます。
* 最小値 0、最大値 65535 です。
* 0 を指定した場合、各メンバーに指定されたポート番号を対象にヘルスチェックを実行します。
* 設定しない場合または範囲外の値を入力した場合、デフォルト値の 0 が設定されます。

#### ヘルスチェックホストヘッダーの設定
ヘルスチェックに使用するホストヘッダーのフィールド値を設定できます。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/healthmonitor-http-host-header です。
* リスナーごとに設定を適用できます。
* ヘルスチェックプロトコルを TCP に設定した場合、このフィールドに設定した値は無視されます。

#### keep-alive タイムアウトの設定
keep-alive タイムアウト値を設定できます。

* 設定位置は .metadata.annotations 配下の loadbalancer.nhncloud/keepalive-timeout です。
* リスナーごとに設定を適用できます。
* 秒単位で設定します。
* 最小値 0、最大値 3600 です。
* 設定しない場合または範囲外の値を入力した場合、デフォルト値の 300 が設定されます。

> [注意]
> keep-alive タイムアウトは、2023 年 11 月 28 日以降に v1.24.3 以上のバージョンにアップグレードされたか、新規作成されたクラスターで設定できます。

#### L7 ルール
リスナーごとに L7 ルールを設定できます。L7 ルールは次のように動作します。

* L7 ルールは、リスナーのプロトコルが HTTP または TERMINATED_HTTPS の場合にのみ作成できます。
* L7 ルールは、作業タイプに応じて、ブロック、URL への転送、メンバーグループへの転送の順に適用されます。
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

L7 ルールの設定には次の制約事項があります。

* L7 ルールの設定に使用するインデックスは 0〜9 の整数値を使用できます。
* 1 つのリスナーに設定する L7 ルールは、それぞれ異なるインデックス値で設定する必要があります。
* 1 つのリスナーに設定する L7 ルールは、それぞれ異なる名前で設定する必要があります。

#### L7 条件
L7 ルールごとに L7 条件を設定できます。L7 条件は次のように動作します。

* L7 ルールに属するすべての L7 条件が満たされた場合に、該当の L7 ルールが適用されます。
* L7 条件間に優先順位はありません。

L7 条件は次のように設定できます。

* 1 つの L7 ルールに L7 条件を最大 10 個まで設定できます。
* 各 L7 条件を識別するために、設定位置に `rule-%d`（`%d` は 0 から始まるインデックス）の形式を使用します。

| 設定位置 | 意味 | 必須 | 値 |
| --- | --- | :-: | --- |
| {LISTENER_SPEC}.{L7POLICY}.{RULE}.loadbalancer.nhncloud/type | 種類 | O | HOST_NAME（ホスト名）、PATH（パス）、FILE_TYPE（ファイルタイプ）、HEADER（ヘッダー）、COOKIE（クッキー）のいずれか |
| {LISTENER_SPEC}.{L7POLICY}.{RULE}.loadbalancer.nhncloud/compare-type | 比較方式 | O | REGEX、STARTS_WITH、ENDS_WITH、CONTAINS、EQUAL_TO のいずれか <br>（ただし、種類が FILE_TYPE の場合は EQUAL_TO、REGEX のみ使用可能） |
| {LISTENER_SPEC}.{L7POLICY}.{RULE}.loadbalancer.nhncloud/key | キー | X（ただし、種類が HEADER、COOKIE の場合は必須） | 255 文字以下の文字列 |
| {LISTENER_SPEC}.{L7POLICY}.{RULE}.loadbalancer.nhncloud/value | 値 | O | 255 文字以下の文字列 |

> [注記]
> * {RULE} は `rule-%d` の形式で、`%d` は 0 から始まるインデックスです。（例: rule-0）

L7 条件には次の制約事項があります。

* L7 条件の設定に使用するインデックスは 0〜9 の整数値を使用できます。
* 1 つの L7 ルールに設定する L7 条件は、互いに異なるインデックス値を設定する必要があります。
* 1 つの L7 ルールに同一仕様の L7 条件（種類、比較方式、キー、値がすべて同じ条件）は追加できません。

> [注意]
> L7 ルールおよび L7 条件は、2024 年 7 月 23 日以降に v1.24.3 以上のバージョンにアップグレードされたか、新規作成されたクラスターで設定できます。

次は L7 ルールおよび条件を設定する例です。

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

## インgressコントローラー
インgressコントローラー（ingress controller）は、Ingress オブジェクトに定義されたルールを参照して、クラスター外部から内部サービスへの HTTP および HTTPS リクエストをルーティングし、SSL/TLS 終端や仮想ホスティングなどを提供します。インgressコントローラーおよびインgressの詳細については、[インgressコントローラー](https://kubernetes.io/ko/docs/concepts/services-networking/ingress-controllers/)および[インgresss](https://kubernetes.io/ko/docs/concepts/services-networking/ingress/) のドキュメントを参照してください。

<a id="install-nginx-ingress-controller"></a>

### NGINX Ingress Controller のインストール
NGINX Ingress Controller は、広く使用されているインgressコントローラーの 1 つです。詳細については、[NGINX Ingress Controller](https://kubernetes.github.io/ingress-nginx/) および [NGINX Ingress Controller for Kubernetes](https://www.nginx.com/products/nginx-ingress-controller/) のドキュメントを参照してください。NGINX Ingress Controller のインストールについては、[Installation Guide](https://kubernetes.github.io/ingress-nginx/deploy/) のドキュメントを参照してください。

<a id="uri-based-service-routing"></a>

### URI ベースのサービス分岐
インgressコントローラーは URI を基にサービスを分岐できます。以下の図は、URI を基にサービスを分岐する簡単な例の構造を示しています。

![ingress-01.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/ingress-01.png)

#### サービスと Pod の作成
次のように、サービスと Pod を作成するためのマニフェストを作成します。`tea-svc` サービスには `tea` Pod を接続し、`coffee-svc` サービスには `coffee` Pod を接続します。

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

マニフェストを適用し、デプロイメント、サービス、Pod が作成されたことを確認します。Pod は **Running** 状態である必要があります。

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

#### Ingress の作成
リクエストパスに応じてサービスを接続する Ingress マニフェストを作成します。エンドポイントが `/tea` のリクエストは `tea-svc` サービスに接続し、`/coffee` のリクエストは `coffee-svc` サービスに接続します。

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

Ingress を作成し、しばらくしてから確認したときに **ADDRESS** フィールドに IP が設定されている必要があります。

```
$ kubectl apply -f cafe-ingress-uri.yaml
ingress.networking.k8s.io/cafe-ingress-uri created

$ kubectl get ingress cafe-ingress-uri
NAME               CLASS   HOSTS   ADDRESS          PORTS   AGE
cafe-ingress-uri   nginx   *       123.123.123.44   80      23s
```

#### HTTP リクエストの送信
外部ホストから Ingress の **ADDRESS** フィールドに設定された IP アドレスへ HTTP リクエストを送信し、Ingress が正しく設定されているか確認します。

エンドポイント `/coffee` へのリクエストは `coffee-svc` サービスに転送され、`coffee` Pod が応答します。応答の **Server name** 項目を確認すると、`coffee` Pod がラウンドロビン方式で交互に応答していることが確認できます。

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

同様に、エンドポイント `/tea` へのリクエストは `tea-svc` サービスに転送され、`tea` Pod が応答します。

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

定義されていない URI へリクエストを送信すると、Ingress コントローラーが `404 Not Found` を返します。

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

#### リソースの削除
テストに使用したリソースは、作成時に使用したマニフェストを使用して削除できます。

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

### ホストベースのサービス分岐
Ingressコントローラーは、ホスト名を基準にサービスを分岐できます。下図は、ホスト名を基準にサービスを分岐する簡単な例の構造を示しています。

![ingress-02.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/ingress-02.png)

#### サービスとPodの作成
[URIベースのサービス分岐](/Container/NKS/ja/user-guide/#uri)と同じマニフェストを使用してサービスとPodを作成します。

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

Ingressを作成してしばらく後に確認したとき、**ADDRESS** フィールドに IP が設定されている必要があります。

```
$ kubectl apply -f cafe-ingress-host.yaml
ingress.networking.k8s.io/cafe-ingress-host created

$ kubectl get ingress
NAME                CLASS   HOSTS                                          ADDRESS          PORTS   AGE
cafe-ingress-host   nginx   tea.cafe.example.com,coffee.cafe.example.com   123.123.123.44   80      36s
```

#### HTTP Requestの送信
外部ホストからIngressのADDRESSに設定されたIPへHTTPリクエストを送信します。ただし、ホスト名を使用してサービスを分岐するようにIngressを構成しているため、ホスト名を指定してリクエストを送信する必要があります。

> [注記]
> 任意のホスト名を使用してテストするには、curl の `--resolve` オプションを使用します。`--resolve` オプションは `{ホスト名}:{ポート番号}:{IP}` の形式で指定します。これは、{ホスト名} に送信される {ポート番号} へのリクエストを {IP} として解決 (resolve) するという意味です。
> `/etc/host` ファイルを開いて `{IP} {ホスト名}` の形式で追加することもできます。

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

不明なホストへリクエストを送信すると、Ingressコントローラーが `404 Not Found` を返します。

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

### ingress-nginx コントローラーの内部通信構造と注意事項
ingress-nginx コントローラーを通じてサービスを外部に公開する場合、リクエストを送信するクライアントの位置（クラスター内部または外部）によって、リクエストがワークロードに転送される経路が異なります。

#### クラスター外部のクライアント
クラスター外部のクライアントが送信するリクエストは、ロードバランサーを通じてIngress Controllerに転送されます。ロードバランサーはIngress Controller Serviceの外部エンドポイントとして機能し、Ingress ControllerはIngressのルールに従ってリクエストを宛先のBackend Podにルーティングします。

```
クラスター外部のクライアント → ロードバランサー → ingress-nginx Service → ingress-nginx Controller Pod → Backend Pod
```

#### クラスター内部のクライアント
クラスター内部のPodがIngressのアドレスにリクエストする場合、トラフィックはロードバランサーを経由しません。リクエストはIngress Controller ServiceのClusterIPを通じて内部経路で直接転送され、この過程でCNIに応じて次の方式でルーティングされます。

- **Calico (VXLAN)**: kube-proxy の iptables ルールベース
- **Calico (eBPF)**: BPF MAP ベースのデータパスを使用

どちらの方式でも、トラフィックは内部ネットワーク内のみで転送され、外部ロードバランサーを経由しません。
```
内部Pod → ingress-nginx Service (ClusterIP) → ingress-nginx Controller Pod → Backend Pod
```

#### 注意事項

- 内部リクエストはロードバランサーのポリシーが適用されません。ロードバランサーの TLS 設定、セキュリティポリシー、ファイアウォールルールなどは内部トラフィックに影響しません。
- IngressドメインをそのままクラスターI内部から呼び出す場合、ロードバランサーを経由しないため、外部とは異なる TLS または Redirect の動作が発生する可能性があります。
- 内部通信時には、IngressドメインではなくService DNSを使用することをお勧めします。内部Pod間の通信は直接Serviceを使用し、外部公開用のエンドポイントにのみIngressを活用することが望ましいです。


<a id="k8s-dashboard"></a>

## Kubernetes ダッシュボード
NHN Kubernetes Service(NKS) は基本の Web UI ダッシュボード (dashboard) を提供します。Kubernetes ダッシュボードの詳細については、[Web UI (ダッシュボード)](https://kubernetes.io/ko/docs/tasks/access-application-cluster/web-ui-dashboard/) ドキュメントを参照してください。

> [注意]
> * Kubernetes ダッシュボードは NKS v1.25.4 までのみデフォルトで提供されます。
> * NKS クラスターバージョンを v1.25.4 から v1.26.3 にアップグレードしても、動作中の Kubernetes ダッシュボード Pod および関連リソースはそのまま維持されます。
> * NHN Cloud コンソールで Kubernetes リソースを照会できます。

<a id="expose-dashboard"></a>
### ダッシュボードサービスの公開
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

ただし、`kubernetes-dashboard` サービスオブジェクトは ClusterIP タイプであるため、まだクラスター外部には公開されていません。ダッシュボードを外部に公開するには、サービスオブジェクトを LoadBalancer タイプに変更するか、Ingress コントローラーと Ingress オブジェクトを作成する必要があります。

#### LoadBalancer サービスオブジェクトへの変更

`LoadBalancer` タイプにサービスオブジェクトを変更すると、クラスター外部に NHN Cloud Load Balancer が作成され、ロードバランサーとサービスオブジェクトが接続されます。ロードバランサーと接続されたサービスオブジェクトを照会すると、**EXTERNAL-IP** フィールドにロードバランサーの IP が表示されます。`LoadBalancer` タイプのサービスオブジェクトの説明については、[LoadBalancer サービス](/Container/NKS/ja/user-guide/#loadbalancer)を参照してください。下図は `LoadBalancer` タイプのサービスを使用してダッシュボードを外部に公開する構造を示しています。

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
> ロードバランサーの IP は外部からアクセス可能なフローティング IP です。**Network > Floating IP** ページで確認できます。

Web ブラウザーで `https://{EXTERNAL-IP}` にアクセスすると、Kubernetes ダッシュボードページが読み込まれます。ログインに必要なトークンについては、[ダッシュボードアクセストークン](/Container/NKS/ja/user-guide/#dashboard-access-token)を参照してください。

> [注記]
> Kubernetes ダッシュボードは自動生成されるプライベート証明書を使用しているため、Web ブラウザーの種類やセキュリティ設定によっては、安全でないページとして表示される場合があります。

#### Ingress を使用したサービスの公開

Ingress は、クラスター内部の複数のサービスへアクセスするためのルーティングを提供するネットワークオブジェクトです。Ingress オブジェクトの設定は Ingress コントローラーによって動作します。`kubernetes-dashboard` サービスオブジェクトを Ingress を通じて公開できます。Ingress および Ingress コントローラーの説明については、[Ingress コントローラー](/Container/NKS/ja/user-guide/#ingress-controller)を参照してください。下図は Ingress を通じてダッシュボードを外部に公開する構造を示しています。

![dashboard-02.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/dashboard-02.png)

[NGINX Ingress Controller のインストール](/Container/NKS/ja/user-guide/#nginx-ingress-controller)を参照して `NGINX Ingress Controller` をインストールし、次のように Ingress オブジェクト作成用のマニフェストを作成します。

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
### ダッシュボードアクセストークン
Kubernetes ダッシュボードにログインするにはトークンが必要です。トークンは次のコマンドで取得できます。

```
# SECRET_NAME=$(kubectl -n kube-system get secrets | grep "kubernetes-dashboard-token" | cut -f1 -d ' ')

$ kubectl describe secret $SECRET_NAME -n kube-system | grep -E '^token' | cut -f2 -d':' | tr -d " "
eyJhbGc...-QmXA
```

出力されたトークンをブラウザーのトークン入力欄に入力すると、クラスター管理者権限を付与されたユーザーとしてログインできます。

<a id="persistent-volume"></a>

## パシステントボリューム
パシステントボリューム (Persistent Volume, PV) は、物理ストレージデバイス (volume) を表す Kubernetes のリソースです。1 つの PV は 1 つの NHN Cloud Block Storage と接続されます。詳細については、[パシステントボリューム](https://kubernetes.io/ko/docs/concepts/storage/persistent-volumes/) ドキュメントを参照してください。

PV を Pod に接続して使用するには、パシステントボリュームクレーム (Persistent Volume Claims, PVC) オブジェクトが必要です。PVC は容量や読み取り/書き込みモードなど、必要なボリュームの要件を定義します。

PV と PVC により、ユーザーは使用したいボリュームの属性を定義し、システムはユーザーの要件に合ったボリュームリソースを割り当てる方式で、リソースの使用と管理を分離します。

<a id="pv-lifecycle"></a>

### PV/PVC のライフサイクル
PV と PVC は 4 段階のライフサイクル (life cycle) に従います。

* プロビジョニング (provisioning)
[ストレージクラス](https://kubernetes.io/ko/docs/concepts/storage/storage-classes/)を使用して、ユーザーが直接ボリュームを確保して PV を作成 (static provisioning) するか、動的に作成 (dynamic provisioning) できます。

* バインディング (binding)
PV と PVC を 1:1 でバインドします。動的プロビジョニングで PV を作成した場合、バインディングも自動的に実行されます。

* 使用 (using)
PV を Pod にマウントして使用します。

* 返還 (reclaiming)
使用が終わったボリュームを回収します。回収方法には、削除 (Delete)、保存 (Retain)、再利用 (Recycle) があります。

| 方法 | 説明 |
| --- | --- |
| 削除 (Delete) | PV を削除する際に、接続されたボリュームも一緒に削除します。 |
| 保存 (Retain) | PV を削除する際に、接続されたボリュームは削除しません。ボリュームはユーザーが直接削除するか、再利用できます。 |
| 再利用 (Recycle) | PV を削除する際に、接続されたボリュームを削除せず、再利用可能な状態にします。この方法は非推奨 (deprecated) となっています。 |

<a id="storageclass"></a>

### StorageClass
プロビジョニングを行うには、まずストレージクラスが定義されている必要があります。ストレージクラスは、特定の特性によってストレージを分類する方法を提供します。ストレージ提供者（provisioner）に関する情報をはじめ、メディアの種類や Availability Zone などを設定できます。

#### provisioner
ストレージの提供者情報を設定します。Kubernetes バージョンによって、サポートされるストレージ提供者情報は次のとおりです。

* v1.19.13 以前のバージョン: provisioner フィールドを必ず `kubernetes.io/cinder` に設定する必要があります。
* v1.20.12 以降のバージョン: provisioner フィールドを `cinder.csi.openstack.org` に設定して使用できます。

#### parameter
ストレージクラスを通じて、次のパラメーターを設定できます。

* ストレージ種類（type）: ストレージの種類を入力します（未入力時は General HDD が設定されます）。
    * **General HDD**: ストレージ種類が HDD に設定されます。
    * **General SSD**: ストレージ種類が SSD に設定されます。
* Availability Zone（availability）: Availability Zone を設定します（未入力時はランダムに設定されます）。
    * 板橋リージョン: **kr-pub-a** または **kr-pub-b**
    * 平村リージョン: **kr2-pub-a** または **kr2-pub-b**
    * 光州リージョン: **kr3-pub-a** または **kr3-pub-b**

#### VolumeBindingMode
ボリュームバインディングモードは、ボリュームバインディングと動的プロビジョニングの開始タイミングを制御します。この設定は、ストレージ提供者が `cinder.csi.openstack.org` の場合にのみ設定できます。

* **Immediate**: PersistentVolumeClaim が作成されると同時に、ボリュームバインディングと動的プロビジョニングが開始されます。PersistentVolumeClaim が作成される時点では、ボリュームを接続する Pod に関する事前情報がない状態です。そのため、ボリュームの Availability Zone と Pod がスケジューリングされるノードの Availability Zone が異なる場合、Pod が正常に動作しない可能性があります。
* **WaitForFirstConsumer**: PersistentVolumeClaim が作成される際には、ボリュームバインディングと動的プロビジョニングを行いません。この PersistentVolumeClaim が初めて Pod に接続されると、Pod がスケジューリングされたノードの Availability Zone 情報をもとにボリュームバインディングと動的プロビジョニングを実行します。そのため、Immediate モードのように、ボリュームの Availability Zone とインスタンスの Availability Zone が異なることで Pod が正常に動作しない問題は発生しません。

#### allowVolumeExpansion
作成済みボリュームの拡張を許可するかどうかを設定します（未入力時は false が設定されます）。

* **True**: ボリュームの拡張を許可します。
* **False**: ボリュームの拡張を許可しません。

#### 例1
以下のストレージクラスマニフェストは、v1.19.13 以前のバージョンを使用する Kubernetes クラスターで利用できます。パラメーターを通じて Availability Zone とボリュームタイプを指定できます。

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

ストレージクラスを作成し、確認します。

```
$ kubectl apply -f storage_class.yaml
storageclass.storage.k8s.io/sc-ssd created

$ kubectl get sc
NAME     PROVISIONER            RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
sc-ssd   kubernetes.io/cinder   Delete          Immediate           false                  3s
```

#### 例2
以下のストレージクラスマニフェストは、v1.20.12 以降のバージョンを使用する Kubernetes クラスターで利用できます。ボリュームバインディングモードを WaitForFirstConsumer に設定することで、PersistentVolumeClaim が Pod に接続されたときにボリュームバインディングと動的プロビジョニングを開始します。

```yaml
# storage_class_csi.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: csi-storageclass
provisioner: cinder.csi.openstack.org
volumeBindingMode: WaitForFirstConsumer
```

ストレージクラスを作成し、確認します。

```
$ kubectl apply -f storage_class_csi.yaml
storageclass.storage.k8s.io/csi-storageclass created

$ kubectl get sc
NAME               PROVISIONER                RECLAIMPOLICY   VOLUMEBINDINGMODE      ALLOWVOLUMEEXPANSION   AGE
csi-storageclass   cinder.csi.openstack.org   Delete          WaitForFirstConsumer   false                  7s
```

<a id="static-provisioning"></a>

### 静的プロビジョニング

静的プロビジョニング (static provisioning) では、ユーザーが直接ブロックストレージを準備する必要があります。NHN Cloud ウェブコンソールの **Storage > Block Storage** サービスページで **[ブロックストレージ作成]** ボタンをクリックし、PV と接続するブロックストレージを作成します。ブロックストレージガイドの[ブロックストレージ作成](/Storage/Block%20Storage/ja/console-guide/#_1)を参照してください。

PV を作成するには、ブロックストレージの ID が必要です。**Storage > Block Storage** サービスページのブロックストレージ一覧から使用するブロックストレージを選択します。下部の **[情報]** タブのブロックストレージ名項目で ID を確認できます。

ブロックストレージと接続する PV マニフェストを作成します。**spec.storageClassName** にはストレージクラス名を入力します。NHN Cloud Block Storage を使用するには、**spec.accessModes** を必ず `ReadWriteOnce` に設定する必要があります。**spec.presistentVolumeReclaimPolicy** は `Delete` または `Retain` に設定できます。

v1.20.12 以降のバージョンのクラスターは **cinder.csi.openstack.org** ストレージプロバイダーを使用する必要があります。ストレージプロバイダーを定義するには、**spec.annotations** 配下に `pv.kubernetes.io/provisioned-by: cinder.csi.openstack.org` の値を指定し、**csi** 項目配下に `driver: cinder.csi.openstack.org` の値を指定します。

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
    volumeHandle: "e6f95191-d58b-40c3-a191-9984ce7532e5" # ブロックストレージの UUID
```

PV を作成し、確認します。

```
$ kubectl apply -f pv-static.yaml
persistentvolume/pv-static-001 created

$ kubectl get pv -o wide
NAME            CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE   VOLUMEMODE
pv-static-001   10Gi       RWO            Delete           Available           sc-default              7s    Filesystem
```

作成した PV を使用するための PVC マニフェストを作成します。**spec.volumeName** には PV の名前を指定する必要があります。その他の項目は PV マニフェストの内容と同じ値を設定します。

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

PVC を作成し、確認します。

```
$ kubectl apply -f pvc-static.yaml
persistentvolumeclaim/pvc-static created

$ kubectl get pvc -o wide
NAME         STATUS   VOLUME          CAPACITY   ACCESS MODES   STORAGECLASS   AGE   VOLUMEMODE
pvc-static   Bound    pv-static-001   10Gi       RWO            sc-default     7s    Filesystem
```

PVC を作成した後に PV の状態を照会すると、**CLAIM** 項目に PVC 名が指定され、**STATUS** 項目が `Bound` に変更されたことを確認できます。

```
$ kubectl get pv -o wide
NAME            CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                STORAGECLASS   REASON   AGE   VOLUMEMODE
pv-static-001   10Gi       RWO            Delete           Bound    default/pvc-static   sc-default              79s   Filesystem
```

<a id="dynamic-provisioning"></a>

### 動的プロビジョニング

動的プロビジョニング（dynamic provisioning）は、ストレージクラスに定義された属性を参照して自動的にブロックストレージを作成します。動的プロビジョニングを使用するには、ストレージクラスのボリュームバインディングモードを設定しないか、**Immediate** に設定する必要があります。

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

ボリュームバインディングモードを設定しないか **Immediate** に設定して PVC を作成すると、PV が自動的に作成されます。PV に接続されたブロックストレージも自動的に作成され、NHN Cloud ウェブコンソールの **Storage > Block Storage** サービスページのブロックストレージ一覧から確認できます。

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
> 動的プロビジョニングで作成されたブロックストレージは、ウェブコンソールから削除することはできません。また、クラスターを削除する際に自動的に削除されることもありません。そのため、クラスターを削除する前にすべての PVC を削除する必要があります。PVC を削除せずにクラスターを削除すると、課金される可能性があります。動的プロビジョニングで作成された PV の reclaimPolicy はデフォルトで `Delete` に設定されているため、PVC を削除するだけで PV とブロックストレージも削除されます。

<a id="pod-pvc-mount"></a>

### Pod への PVC マウント

Pod に PVC をマウントするには、Pod マニフェストにマウント情報を定義する必要があります。`spec.volumes.persistenVolumeClaim.claimName` に使用する PVC 名を入力します。また、`spec.containers.volumeMounts.mountPath` にマウントするパスを入力します。

以下の例は、静的プロビジョニングで作成した PVC を Pod の `/usr/share/nginx/html` にマウントします。

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

Pod を作成し、ブロックストレージがマウントされていることを確認します。

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

### ボリューム拡張
PersistentVolumeClaim (PVC) オブジェクトを編集して、既存ボリュームのサイズを変更できます。PVC オブジェクトの **spec.resources.requests.storage** 項目を変更することでボリュームサイズを変更できます。ボリュームの縮小はサポートされていません。ボリューム拡張機能を使用するには、StorageClass の **allowVolumeExpansion** 属性が **True** である必要があります。


#### v1.19.13 以前のバージョンのボリューム拡張
v1.19.13 以前のバージョンのストレージプロバイダー **kubernetes.io/cinder** は、使用中のボリュームの拡張機能を提供していません。使用中のボリュームの拡張機能を使用するには、v1.20.12 以降のバージョンの **cinder.csi.openstack.org** ストレージプロバイダーを使用する必要があります。クラスターのアップグレード機能を使用して v1.20.12 以降のバージョンにアップグレードすることで、**cinder.csi.openstack.org** ストレージプロバイダーを使用できます。

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

#### v1.20.12 以降のバージョンのボリューム拡張
v1.20.12 以降のバージョンのストレージプロバイダー **cinder.csi.openstack.org** は、デフォルトで使用中のボリュームの拡張機能をサポートしています。PVC オブジェクトの **spec.resources.requests.storage** 項目を任意の値に変更することでボリュームサイズを変更できます。

<a id="service-integration"></a>

## NHN Cloud サービス連携

<a id="ncr-integration"></a>

### NHN Cloud Container Registry(NCR) サービス連携
NHN Cloud Container Registry に保存したイメージを使用できます。レジストリに保存されたイメージを使用するには、ユーザーレジストリにログインするためのシークレット (secret) を作成する必要があります。

NHN Cloud (Old) Container Registry を使用する場合は、次のようにシークレットを作成する必要があります。

```
$ kubectl create secret docker-registry registry-credential --docker-server={ユーザーレジストリアドレス} --docker-username={NHN Cloud アカウントのメールアドレス} --docker-password={サービス Appkey または統合 Appkey}
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


デプロイメントマニフェストファイルにシークレット情報を追加し、イメージ名を変更することで、ユーザーレジストリに保存されたイメージを使用して Pod を作成できます。

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

### NHN Cloud NAS サービス連携
NHN Cloud が提供する NAS ボリュームを PV として活用できます。NAS サービスを使用するには、v1.20 以降のバージョンのクラスターを使用する必要があります。NHN Cloud NAS の詳細については、[NAS コンソール使用ガイド](/Storage/NAS%20(online)/ja/console-guide) を参照してください。

> [注記]
> NHN Cloud NAS サービスは、現時点 (2024年08月) で一部のリージョンのみで提供されています。NHN Cloud NAS サービスのサポートリージョンの詳細については、[NAS サービス概要](/Storage/NAS%20(online)/ja/overview) を参照してください。

#### すべてのワーカーノードで rpcbind サービスを実行
NAS ボリュームを使用するには、すべてのワーカーノードで rpcbind サービスを実行する必要があります。すべてのワーカーノードに接続した後、次のコマンドで rpcbind サービスを実行します。

rpcbind サービスの実行コマンドは、イメージの種類に関わらず同一です。

```
$ systemctl start rpcbind
```

強化されたセキュリティルールを使用しているクラスターの場合、セキュリティルールの追加が必要です。

| 方向 | IP プロトコル | ポート範囲 | Ether | リモート | 説明 |
| :-: | :-: | :-: | :-: | :-: | :-: |
| egress | TCP | 2049 | IPv4 | NAS IP アドレス | rpc の NFS ポート、方向: csi-nfs-node (ワーカーノード) → NAS |
| egress | TCP | 111 | IPv4 | NAS IP アドレス | rpc の portmapper ポート、方向: csi-nfs-node (ワーカーノード) → NAS |
| egress | TCP | 635 | IPv4 | NAS IP アドレス | rpc の mountd ポート、方向: csi-nfs-node (ワーカーノード) → NAS |

#### csi-driver-nfs のインストール
NHN Cloud NAS サービスを使用するには、クラスターに NHN Kubernetes Service (NKS) の Addon 機能として [nfs-csi-plugin](/Container/NKS/ja/user-guide/#addon-mgmt-addon-nfs-csi-plugin) をデプロイする必要があります。

csi-driver-nfs は、NFS ストレージに新しいサブディレクトリを作成する方式で動作する NFS ストレージプロビジョニングをサポートするドライバーです。
csi-driver-nfs は、ストレージクラスに NFS ストレージ情報を提供する方式で動作するため、ユーザーが管理する対象を削減できます。

csi-driver-nfs を使用して複数の PV を構成する場合、csi-driver-nfs が NFS ストレージ情報を StorageClass に登録するため、NFS-Provisioner Pod を別途構成する必要はありません。
<br>
![nfs-csi-driver-02.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/nfs-csi-driver-02.png)

#### プロビジョニング時に既存の NHN Cloud NAS ボリュームを利用する方法
PV マニフェスト作成時に NAS 情報を入力するか、StorageClass マニフェストに NAS 情報を入力して、既存の NAS ボリュームを PV として使用できます。

##### 方法 1. PV マニフェスト作成時に NAS ボリューム情報を定義する
PV マニフェスト作成時に NHN Cloud NAS ボリューム情報を定義します。設定箇所は .spec 配下の **csi** です。

* driver: **nfs.csi.k8s.io** を入力します。
* readOnly: **false** を入力します。
* volumeHandle: クラスター内で重複しない一意の id を入力します。
* volumeAttributes: NAS ボリュームの接続情報を入力します。
  * server: NAS ボリュームの接続情報のうち **ip** 部分の値を入力します。
  * share: NAS ボリュームの接続情報のうち **ボリューム名** 部分の値を入力します。

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

PV を作成して確認します。
```
$ kubectl apply -f pv.yaml
persistentvolume/pv-onas created

$ kubectl get pv -o wide
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM                      STORAGECLASS   REASON   AGE    VOLUMEMODE
pv-onas                                    300Gi      RWX            Retain           Available                                                      101s   Filesystem
```

作成した PV を使用するための PVC マニフェストを作成します。**spec.volumeName** には PV の名前を指定する必要があります。その他の項目は PV マニフェストの内容と同じように設定します。
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

PVC を作成して確認します。
```
$ kubectl apply -f pvc.yaml
persistentvolumeclaim/pvc-onas created

$ kubectl get pvc -o wide
NAME              STATUS   VOLUME    CAPACITY   ACCESS MODES   STORAGECLASS   AGE    VOLUMEMODE
pvc-onas   Bound    pv-onas   300Gi      RWX                           2m8s   Filesystem
```

PVC を作成した後に PV の状態を確認すると、**CLAIM** 項目に PVC 名が設定され、STATUS 項目が `Bound` に変更されていることを確認できます。
```
$ kubectl get pv -o wide
NAME      CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                     STORAGECLASS   REASON   AGE     VOLUMEMODE
pv-onas   300Gi      RWX            Retain           Bound    default/pvc-onas                           3m20s   Filesystem
```

##### 方法 2. StorageClass マニフェスト作成時に NAS 情報を定義する
StorageClass マニフェスト作成時にストレージプロバイダー情報および NHN Cloud NAS ボリューム情報を定義します。

* provisioner: **nfs.csi.k8s.io** を入力します。
* parameters: 入力項目については以下の表を参照してください。

| 項目 | 説明 | 例 | 必須 | デフォルト値 |
| ------- |------- | --------------------------- | ---------------------------- | ------------- |
| server | NAS ボリュームの接続情報のうち **ip** を意味します。 | 192.168.0.81 | O |  |
| share | NAS ボリュームの接続情報のうち **ボリューム名** を意味します。 | /onas_300gb | O |  |
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

StorageClass を作成して確認します。
```
$ kubectl apply -f storageclass.yaml
storageclass.storage.k8s.io/onas-sc created

$ kubectl get sc
NAME      PROVISIONER      RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
onas-sc   nfs.csi.k8s.io   Retain          Immediate           false                  3s
```

PV を別途作成する必要がないため、PVC マニフェストのみを作成します。PVC マニフェストには **spec.volumeName** を設定しません。
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
ボリュームバインディングモードを設定しないか、Immediate に設定して PVC を作成すると、PV が自動的に作成されます。

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

#### プロビジョニング時に新しい NHN Cloud NAS ボリュームを作成する方法
StorageClass および PVC マニフェスト作成時に NAS 情報を入力することで、自動的に作成された NAS ボリュームを PV として使用できます。

StorageClass マニフェストには、ストレージプロバイダー情報および作成する NAS ボリュームのスナップショットポリシー、アクセス制御リスト (ACL)、サブネット情報を定義します。

* provisioner: **nfs.csi.k8s.io** を入力します。
* parameters: 入力項目は下記の表を参照してください。パラメータ値に複数の値を定義する場合は **,** を使用して値を区切ります。

| 項目 | 説明 | 例 | 複数値 | 必須 | デフォルト値 |
| ------- |------- | --------------------------- | ---------------------------- | --------- | ------------- |
| maxscheduledcount | 保存可能な最大スナップショット数です。最大保存数に達すると、自動作成されたスナップショットのうち最も古いスナップショットが削除されます。1〜20 の数値のみ入力可能です。 | "7" | X | X |  |
| reservepercent | 保存可能な最大スナップショット容量です。スナップショット容量の合計が設定したサイズを超えた場合、すべてのスナップショットのうち最も古いスナップショットが削除されます。0〜80 の数値のみ入力可能です。 | "80" | X | X |  |
| scheduletime | スナップショットが作成される時刻です。 | "09:00" | X | X |  |
| scheduletimeoffset | スナップショット作成時刻のオフセットです。UTC 基準で、KST で使用する場合は +09:00 を指定します。 | "+09:00" | X | X |  |
| scheduleweekdays | スナップショット作成の周期です。日曜日から土曜日まで、それぞれ数値 0〜6 で表されます。 | "6" | O | X |  |
| subnet | ストレージにアクセスするサブネットです。選択した VPC のサブネットのみ選択できます。 | "59526f1c-c089-4517-86fd-2d3dac369210" | X | O |  |
| acl | 読み取り・書き込み権限を許可する IP または IP 帯域のリストです。 | "0.0.0.0/0" | O | X | 0.0.0.0/0 |
| onDelete | PVC 削除時に NAS ボリュームを削除するかどうかです。 | "delete" / "retain" | X | X | delete |
| mountPermissions | NAS ボリュームのマウントポイントディレクトリに設定する権限を指定します。 | "0700"| X | X | 0741 |
| uid | NAS ボリュームのマウントポイントディレクトリに設定する UID を入力します。 | 1000 | X | X | root(0) |
| gid | NAS ボリュームのマウントポイントディレクトリに設定する GID を入力します。 | 1000 | X | X | root(0) |

> [注記]
> スナップショットパラメータを使用する場合は、関連するすべてのパラメータ値を定義する必要があります。スナップショット関連のパラメータは以下のとおりです。
> + maxscheduledcount
> + reservepercent
> + scheduletime
> + scheduletimeoffset
> + scheduleweekdays

<br>

> [注意] 複数サブネット環境での制約事項
> 
> NAS ボリュームはストレージクラスに定義されたサブネットに接続されます。
> Pod が NAS ボリュームと連携するためには、すべてのワーカーノードグループがこのサブネットに接続されている必要があります。

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

PVC マニフェストの **Annotation** に、作成する NAS ボリュームの名前、説明、サイズを定義します。入力項目は下記の表を参照してください。

| 項目 | 説明 | 例 | 必須 |
| ---- | ------- | --------------------------- | --------- |
| nfs-volume-name | 作成するストレージの名前です。ストレージ名を使用して NFS アクセスパスを生成します。名前は 100 文字以内の英数字および一部の記号 ('-'、'_') のみ入力できます。 | "nas_sample_volume_300gb" | O |
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

Pod を作成し、NAS ボリュームがマウントされていることを確認します。
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
> Pod に PV をマウントする際、subdirectory のみがマウントされるのではなく NFS ストレージ全体がマウントされるため、アプリケーションがプロビジョニングされたサイズ分のボリュームのみ使用するよう強制することはできません。

<a id="encrypted-block-storage-integration"></a>

### NHN Cloud 暗号化ブロックストレージ連携
NHN Cloud が提供する暗号化されたブロックストレージを PV として活用できます。NHN Cloud 暗号化ブロックストレージの詳細については、[暗号化ブロックストレージ](/Storage/Block%20Storage/ja/console-guide/#_2)を参照してください。

> [注記]
> 暗号化ブロックストレージサービス連携機能は、v1.24.3 以上のバージョンのクラスターで使用できます。
> 2023年11月28日以降に新規作成されたクラスターには、デフォルトで暗号化ブロックストレージ連携機能が内蔵されています。
> 2023年11月28日以前に作成されたクラスターは、v1.24.3 以上のバージョンにアップグレードするか、csi-cinder-controllerplugin ステートフルセットと csi-cinder-nodeplugin デーモンセットの cinder-csi-plugin イメージを最新バージョンに置き換えることで、暗号化ブロックストレージ連携機能を使用できます。

> [注意]
> v1.24.3 以前のバージョンのクラスターをアップグレードせずに cinder-csi-plugin コンテナイメージのみ置き換えて使用した場合、誤動作を引き起こす可能性があります。

#### 暗号化ブロックストレージ連携のための cinder-csi-plugin イメージ更新
以下のコマンドを実行して、現在のクラスターにデプロイされている cinder-csi-plugin イメージのタグを確認できます。

```
$ kubectl -n kube-system get statefulset csi-cinder-controllerplugin -o=jsonpath="{$.spec.template.spec.containers[?(@.name=='cinder-csi-plugin')].image}"

> registry.k8s.io/provider-os/cinder-csi-plugin:v1.27.101
```

cinder-csi-plugin イメージのタグが v1.27.101 以上の場合、特別な対応なしに暗号化ブロックストレージを連携できます。
cinder-csi-plugin イメージのタグが v1.27.101 未満の場合、以下の手順で cinder-csi-plugin のイメージを更新した後、暗号化ブロックストレージを連携できます。

| リージョン | インターネット接続 | cinder-csi-plugin イメージ |
| --- | --- | --- |
| 韓国（判橋）リージョン | O | dfe965c3-kr1-registry.container.nhncloud.com/container_service/cinder-csi-plugin:v1.27.101 |
| | X | private-dfe965c3-kr1-registry.container.nhncloud.com/container_service/cinder-csi-plugin:v1.27.101 |
| 韓国（坪村）リージョン | O | 6e7f43c6-kr2-registry.container.cloud.toast.com/container_service/cinder-csi-plugin:v1.27.101 |
| | X | private-6e7f43c6-kr2-registry.container.cloud.toast.com/container_service/cinder-csi-plugin:v1.27.101 |
| 韓国（光州）リージョン | O | d6628457-kr3-registry.container.nhncloud.com/container_service/cinder-csi-plugin:v1.27.101 |
| | X | private-d6628457-kr3-registry.container.nhncloud.com/container_service/cinder-csi-plugin:v1.27.101 |

##### 1. container_image に正しい cinder-csi-plugin イメージの値を入力します。
```
$ container_image={cinder-csi-plugin イメージ}
```

##### 2. コンテナイメージを置き換えます。
```
$ kubectl -n kube-system patch statefulset csi-cinder-controllerplugin -p "{\"spec\": {\"template\": {\"spec\": {\"containers\": [{\"name\": \"cinder-csi-plugin\", \"image\": \"${container_image}\"}]}}}}"

$ kubectl -n kube-system patch daemonset csi-cinder-nodeplugin -p "{\"spec\": {\"template\": {\"spec\": {\"containers\": [{\"name\": \"cinder-csi-plugin\", \"image\": \"${container_image}\"}]}}}}"
```

> [注記]
> cinder-csi-plugin コンテナイメージは NHN Cloud NCR で管理されています。閉域網環境に構成されたクラスターはインターネットに接続されていないため、イメージを正常に取得するには Private URI を使用するための環境構成が必要です。Private URI の使用方法の詳細については、[NHN Cloud Container Registry(NCR) ユーザーガイド](/Container/NCR/ja/user-guide/#private-uri)を参照してください。


#### 静的プロビジョニング
PV を作成するには、暗号化ブロックストレージの ID が必要です。Storage > Block Storage サービスページのブロックストレージ一覧から使用するブロックストレージを選択します。下部の情報タブのブロックストレージ名項目から ID を確認できます。

PV マニフェストの作成時に、暗号化ブロックストレージの情報を入力します。設定箇所は **.spec.csi** 以下です。

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

PVC マニフェストの作成および Pod へのマウント手順は、通常のブロックストレージの静的プロビジョニングと同じです。詳細については、[静的プロビジョニング](/Container/NKS/ja/user-guide/#static-provisioning)を参照してください。

#### 動的プロビジョニング
ストレージクラスのマニフェスト作成時に暗号化ブロックストレージの作成に必要な情報を入力することで、自動的に作成された暗号化ブロックストレージを PV として使用できます。

ストレージクラスのマニフェストに暗号化ブロックストレージの作成に必要な情報を入力します。設定箇所は **.parameters** 以下です。

* ストレージの種類（type）: ストレージの種類を入力します。
    * **Encrypted HDD**: ストレージの種類が暗号化された HDD に設定されます。
    * **Encrypted SSD**: ストレージの種類が暗号化された SSD に設定されます。
* 暗号化キー ID（volume_key_id）: Secure Key Manager（SKM）サービスで作成した対称キーの ID を入力します。
* 暗号化 AppKey（volume_appkey）: Secure Key Manager（SKM）サービスで確認した Appkey を入力します。

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

PVC マニフェストの作成および Pod へのマウント手順は、通常のブロックストレージの動的プロビジョニングと同じです。詳細については、[動的プロビジョニング](/Container/NKS/ja/user-guide/#dynamic-provisioning)を参照してください。


<a id="etcd-encryption-with-skm"></a>

### 機密データの暗号化/復号化における Secure Key Manager サービス連携

NKS クラスターは、secret リソースをデータストア（etcd）に保存する際にデータを暗号化して保存します。NKS はこのデータを暗号化するために、2 つの方式を提供しています。

#### 基本方式

* クラスター作成時に対称キーを自動生成してコントロールプレーンに保存
* そのキーで etcd データを暗号化
* キー管理がクラスター内部で行われる

#### SKM 連携方式

* ストレージ暗号化プロバイダーを Secure Key Manager（SKM）に設定
* etcd データの暗号化/復号化時に SKM API を通じて暗号化/復号化処理を実行
* 集中化されたキー管理と監査ロギングが可能

> [注意]
> クラスターに連携されている SKM 対称キーまたはローテーションされたキーバージョンを削除すると、クラスターが正常に動作しなくなります。
> * etcd データの復号化が不可能になり、クラスターの起動に失敗します
> * 暗号化されたリソースへのアクセスが不可能になります
> * 回復不可能なデータ損失が発生する可能性があります

> 注記: 安全にローテーションされたキーバージョンを削除する方法
> すべての secret リソースデータを読み取り、新しいキーで再書き込みするよう強制すると、最新バージョンのキーでデータが再暗号化されます。
> 以下のコマンドですべての secret リソースデータを再暗号化した後、ローテーションされたキーバージョンを安全に削除できます。
> `kubectl get secrets --all-namespaces -o json | kubectl replace -f -`