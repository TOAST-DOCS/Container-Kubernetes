## Container > NHN Kubernetes Service(NKS) > 使用ガイド

## クラスター
クラスターは、ユーザーのKubernetesを構成するインスタンスのグループです。

### クラスター作成
NHN Kubernetes Service(NKS)を使用するには、まずクラスターを作成する必要があります。**Container > NHN Kubernetes Service(NKS)**サービスページで**クラスター作成**ボタンを押すと、クラスター作成ページが表示されます。クラスターの作成に必要な項目は次のとおりです。

| 項目 | 説明 |
| --- | --- |
| クラスター名 | Kubernetesクラスターの名前。20文字以内で小文字と数字、(-)のみ入力可能です。小文字で始まり、小文字または数字で終わる必要があります。 |
| Kubernetesのバージョン | 使用するKubernetesのバージョン |
| VPC | クラスターに接続するVPCネットワーク |
| サブネット | VPCに定義されたサブネットのうち、クラスターを構成するインスタンスに接続するサブネット |
| イメージ | クラスターを構成するインスタンスに使用するイメージ |
| アベイラビリティゾーン | 基本ノードグループインスタンスを作成する領域 |
| インスタンスタイプ | 基本ノードグループインスタンスの仕様 |
| ノード数 | 基本ノードグループインスタンスの数 |
| キーペア | 基本ノードグループアクセスに使用するキーペア |
| ブロックストレージタイプ | 基本ノードグループインスタンスのブロックストレージの種類 |
| ブロックストレージサイズ | 基本ノードグループインスタンスのブロックストレージサイズ |
| 追加ネットワーク | 基本ワーカーノードグループに作成する追加ネットワーク/サブネット |

> [注意]
> クラスタ作成時、サブネット帯域が以下のネットワーク帯域と重ならないように設定する必要があります。
>  - 10.100.0.0/16
>  - 10.254.0.0/16

NHN Kubernetes Service(NKS)は複数のバージョンをサポートしています。バージョンによっては一部機能に制約がある場合があります。

| バージョン | クラスタ新規作成 | 作成されたクラスタの使用|
| :-: | :-: | :-: |
| v1.17.6 | 不可 | 可能 |
| v1.18.19 | 不可 | 可能 |
| v1.19.13 | 可能 | 可能 |
| v1.20.12 | 不可 | 可能 |
| v1.21.6 | 可能 | 可能 |
| v1.22.3 | 可能 | 可能 |
| v1.23.3 | 可能 | 可能 |
| v1.24.3 | 可能 | 可能 |


必要な情報を入力し、**クラスター作成**ボタンを押すと、クラスターの作成が始まります。クラスターリストで状態を確認できます。作成には約10分かかります。クラスターの設定によっては、さらに時間がかかる場合もあります。


### クラスター照会
作成したクラスターは**Container > NHN Kubernetes Service(NKS)**サービスページで確認できます。クラスターを選択すると、下部にクラスター情報が表示されます。

| 項目 | 説明 |
| --- | --- |
| クラスター名 | Kubernetesクラスターの名前とID |
| ノード数 | クラスターを構成するすべてのノードインスタンス数 |
| Kubernetesバージョン | 使用中のKubernetesバージョン |
| VPC | クラスターに接続したVPCネットワーク |
| サブネット | クラスターを構成するノードインスタンスに接続したサブネット |
| APIエンドポイント | クラスターにアクセスして操作するためのAPIエンドポイントURI |
| 設定ファイル | クラスターにアクセスして操作するために必要な設定ファイルのダウンロードボタン |

### クラスター削除
削除するクラスターを選択し、**クラスター削除**ボタンを押すと削除が行われます。削除には約5分かかります。クラスターの状態によっては、さらに時間がかかる場合もあります。

## ノードグループ
ノードグループはKubernetesを構成するワーカーノードインスタンスのグループです。

### ノードグループ照会
クラスターリストからクラスター名を押すと、ノードグループリストを確認できます。ノードグループを選択すると、下部にノードグループ情報が表示されます。

* 基本情報
**基本情報**タブでは、次のような情報を確認できます。

| 項目 | 説明 |
| --- | --- |
| ノードグループ名 | ノードグループ名とID |
| クラスター名 | ノードグループが属しているクラスターの名前とID |
| Kubernetesバージョン | 使用中のKubernetesバージョン |
| アベイラビリティゾーン | ノードグループインスタンスが作成された領域 |
| インスタンスタイプ | ノードグループインスタンスの仕様 |
| イメージタイプ | ノードグループインスタンスに使用したイメージの種類 |
| ブロックストレージサイズ | ノードグループインスタンスのブロックストレージサイズ |
| 作成日 | ノードグループが作成された日時 |
| 修正日 | ノードグループが最後に修正された日時 |

* ノードリスト
**ノードリスト**タブでは、ノードグループを構成するインスタンスのリストを確認できます。

### ノードグループ作成
クラスターを作成すると、基本ノードグループが作成されますが、必要に応じて追加ノードグループを作成できます。基本ノードグループのインスタンスより高い仕様のコンテナ起動環境が必要な場合や、スケールアウト(scale out、拡張)のためにさらに多くのワーカーノードインスタンスが必要な場合は、追加ノードグループを作成して使用できます。ノードグループリストページで**ノードグループ作成**ボタンを押すと、ノードグループ作成ページが表示されます。ノードグループの作成に必要な項目は次のとおりです。

| 項目 | 説明 |
| --- | --- |
| アベイラビリティゾーン | クラスターを構成するインスタンスを作成する領域 |
| ノードグループ名 | 追加ノードグループの名前。20文字以内で小文字と数字、(-)のみ入力可能です。小文字で始まり、小文字または数字で終わる必要があります。 |
| インスタンスタイプ | 追加ノードグループのインスタンス仕様 |
| ノード数 | 追加ノードグループインスタンス数 |
| キーペア | 追加ノードグループアクセスに使用するキーペア |
| ブロックストレージタイプ | 追加ノードグループインスタンスのブロックストレージ種類 |
| ブロックストレージサイズ | 追加ノードグループインスタンスのブロックストレージサイズ |
| 追加ネットワーク | 基本ワーカーノードグループに作成する追加ネットワーク/サブネット |

必要な情報を入力し、**ノードグループ作成**ボタンを押すと、ノードグループの作成が始まります。ノードグループリストで状態を確認できます。ノードグループの作成には約5分かかります。ノードグループの設定によっては、さらに時間がかかる場合もあります。

>[注意]
>該当クラスタを作成したユーザーのみノードグループを作成できます。

### ノードグループ削除
ノードグループリストから削除するノードグループを選択し、**ノードグループ削除**ボタンを押すと、削除が行われます。ノードグループの削除には約5分かかります。ノードグループの状態によっては、さらに時間がかかる場合もあります。

### ノードグループにノード追加
動作中のノードグループにノードを追加できます。ノードグループ情報照会ページのノードリストタブを押すと、現在のノードリストが表示されます。ノード追加ボタン押し、ノード数を入力するとノードが追加されます。

>[注意]
>オートスケーラーが有効になっているノードグループは、手動でノードを追加できません。

### ノードグループからノード削除
動作中のノードグループからノードを削除できます。ノードグループ情報照会ページのノードリストタブを押すと、現在のノードリストが表示されます。ノードリストの中から削除するノードを選択し、ノード削除ボタンを押すと、確認ダイアログボックスが表示されます。削除するノード名をもう一度確認して確認ボタンを押すとノードが削除されます。

>[注意]
>削除されるノードで動作していたPodは強制終了します。削除されるノードで動作中のPodを安全に他のノードへ移すにはdrainコマンドを実行する必要があります。ノードがdrainされた後も新しいPodはこのノードにスケジューリングされる場合があります。新しいPodが削除されるノードにスケジューリングされることを防止するにはcordonコマンドを実行する必要があります。ノードを安全に管理するためのより詳しい内容は、下記の文書を参照してください。

>[注意]
>オートスケーラーが有効になっているノードグループは、手動でノードを削除できません。

* [安全なノードdrain](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/)
* [手動ノード管理](https://kubernetes.io/docs/concepts/architecture/nodes/#manual-node-administration)

### GPUノードグループ使用 
KubernetesでGPU基盤ワークロードの実行が必要な場合、 GPUインスタンスで構成されたノードグループを作成できます。
クラスターまたはノードグループ作成プロセスでインスタンスタイプを選択する時、 `g2`タイプを選択するとGPUノードグループを作成できます。

> [参考]
> NHN Cloud GPUインスタンスで提供されるGPUはNVIDIA系です。 ([使用可能なGPUの仕様を確認](/Compute/GPU%20Instance/ja/overview/#gpu))
> NVIDIA GPUを利用するために必要なKubernetesのnvidia-device-pluginは、GPUノードグループの作成時に自動的にインストールされます。

作成されたGPUノードの基本的な設定のヘルスチェックおよび簡単な動作テストは次のような方法を利用できます。

#### ノード水準のヘルスチェック
GPUノードに接続した後、`nvidia-smi`コマンドを実行します。
次のような内容が出力されればGPU driverが正常に動作しているということです。

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

#### Kubernetes水準のヘルスチェック
`kubectl`コマンドを使用してクラスター水準で使用可能なGPUリソース情報を確認します。
以下は各ノードで使用可能なGPUコアの個数を出力するコマンドおよび実行結果です。

```
$ kubectl get nodes -A -o custom-columns='NAME:.metadata.name,GPU Allocatable:.status.allocatable.nvidia\.com/gpu,GPU Capacity:.status.capacity.nvidia\.com/gpu'
NAME                                       GPU Allocatable   GPU Capacity
my-cluster-default-w-vdqxpwisjjsk-node-1   1                 1
```

#### GPUテストのためのサンプルワークロード実行
Kubernetesクラスターに属すGPUノードはCPUとメモリの他に`nvidia.com/gpu`という名前のリソースを提供します。
GPUを使用したい場合は`nvidia.com/gpu`リソースを割り当てられるように、下記のサンプルファイルのように入力してください。

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

上記のファイルを実行すると次のような結果を確認できます。

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

> [参考]
> GPUが必要ないワークロードがGPUノードに割り当てられることを防ぎたい場合は[TaintおよびTolerationの概要](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/)を参照してください。

### オートスケーラー
オートスケーラーはノードグループの可用リソースが足りなくてPodをスケジューリングできなかったり、ノードの使用率が一定水準以下で維持する時、ノードの数を自動的に調整する機能です。この機能はノードグループごとに設定することができ、独立して動作します。この機能はKubernetesプロジェクトの公式サポート機能であるcluster-autoscaler機能をベースにします。詳細な事項は[Cluster Autoscaler](https://github.com/kubernetes/autoscaler/tree/master/cluster-autoscaler)を参照してください。

> [参考]
> NHN Kubernetes Service(NKS)に適用された`cluster-autoscaler`のバージョンは`1.19.0`です。
#### 用語整理
オートスケーラー機能で使用する用語とその意味は次のとおりです。

| 用語 | 意味 |
| --- | --- |
| 増設 | ノードの数を増加させることです。 |
| 削除 | ノードの数を減らすことです。 |

> [注意]
> ワーカーノードがインターネットに接続できない環境で動作している場合、オートスケーラーコンテナイメージをワーカーノードに直接インストールする必要があります。この作業が必要な対象は次のとおりです。
> 
> * パンギョリージョン：2020年11月24日以前に作成したノードグループ
> * 坪村リージョン：2020年11月19日以前に作成したノードグループ
> 
> オートスケーラーのコンテナイメージパスは次のとおりです。
>
> * k8s.gcr.io/autoscaling/cluster-autoscaler:v1.19.0
#### オートスケーラー設定
オートスケーラー機能はノードグループごとに設定して動作します。オートスケーラー機能は下記の方法で設定できます。

* クラスター作成時、基本ノードグループに設定
* ノードグループを追加する時、追加ノードグループに設定
* 作成されているノードグループに設定

オートスケーラーを有効にすると、下記の項目を設定できます。

| 設定項目 | 意味 | 有効範囲 | デフォルト値 | 単位 |
| --- | --- | --- | --- | --- |
| 最小ノード数 | 削除可能な最小ノード数| 1-10 | 1 | 台|
| 最大ノード数 | 増設可能な最大ノード数| 1-10 | 10 | 台|
| 削除 | ノードの削除を行うかどうかの設定 | 有効/無効 | 有効 | - |
| リソース使用量しきい値 | 削除の基準であるリソース使用量しきい値の基準値 | 1-100 | 50 | % |
| しきい値維持時間| 削除対象になるノードのしきい値以下のリソース使用量維持時間| 1-1440 | 10 | 分 |
| 増設後の遅延時間 | ノード増設後、削除対象ノードでモニタリングを開始するまでの遅延時間| 10-1440 | 10 | 分 |

> [注意]
> オートスケーラーが有効になっているノードグループは手動でノードを追加または削除できません。
#### 増設および削除条件
下記の条件を全て満たすとノードを増設します。

* Podがスケジューリングできるノードがない
* 現在のノード数が最大ノード数より少ない

下記の条件を全て満たすとノードを減らします。

* ノードのリソース使用量がしきい値以下をしきい値維持時間継続
* 現在のノード数が最小ノード数より多い

特定のノードに下記の条件を満たすPodが1つでも存在する場合は、そのノードはノード削除候補から除外されます。

* "PodDisruptionBudget"で制約を受けるPod
* "kube-system"名前空間のPod
* "deployment"、"replicaset"などの制御オブジェクトにより始まっていないPod
* ローカルストレージを使用するPod
* "node selector"などの制約により他のノードに移動できないPod

増設および削除条件の詳細は[Cluster Autoscaler FAQ](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md)を参照してください。

#### 動作例
オートスケーラーの動作を例を用いて確認します。

##### 1. オートスケーラー有効化

対象クラスターの基本ノードグループのオートスケーラー機能を有効化します。この例では、基本ノードグループのノード数を1で作成し、オートスケーラー設定項目は下記のように設定しました。

| 設定項目 | 設定値 |
| --- | --- |
| 最小ノード数 | 1 |
| 最大ノード数 | 5 |
| 削除 | 有効 |
| リソース使用量しきい値 | 50 |
| しきい値維持時間| 3 |
| 増設後の遅延時間 | 5 |

##### 2. Pod配布

下記のマニフェストでPodを配布します。

> [注意]
> このマニフェストのようにコンテナのリソースリクエストが明示されている必要があります。
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

配布リクエストしたPodのCPUリソースの合計がノード1つのリソースより大きいため、以下のように複数のPodが`Pending`状態になります。この状況でノードの増設が発生します。

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

##### 3. ノード増設確認

以下は、増設前のノードリストです。

```
# kubectl get nodes
NAME                                            STATUS   ROLES    AGE   VERSION
autoscaler-test-default-w-ohw5ab5wpzug-node-0   Ready    <none>   45m   v1.23.3
```

約5～10分後、以下のようにノードが増設されたことを確認できます。

```
# kubectl get nodes
NAME                                            STATUS   ROLES    AGE   VERSION
autoscaler-test-default-w-ohw5ab5wpzug-node-0   Ready    <none>   48m   v1.23.3
autoscaler-test-default-w-ohw5ab5wpzug-node-1   Ready    <none>   77s   v1.23.3
autoscaler-test-default-w-ohw5ab5wpzug-node-2   Ready    <none>   78s   v1.23.3
```

`Pending`状態だったPodがノード増設後に正常スケジューリングされたことを確認できます。

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

ノード増設イベントは、以下のコマンドで確認できます。

```
# kubectl get events --field-selector reason="TriggeredScaleUp"
LAST SEEN   TYPE     REASON             OBJECT                                 MESSAGE
4m          Normal   TriggeredScaleUp   pod/nginx-deployment-756fd4cdf-64gtv   pod triggered scale-up: [{default-worker-bf5999ab 1->3 (max: 5)}]
4m          Normal   TriggeredScaleUp   pod/nginx-deployment-756fd4cdf-7bsst   pod triggered scale-up: [{default-worker-bf5999ab 1->3 (max: 5)}]
...
```


##### 4. Pod削除後、ノード削除確認

配布されているデプロイメント(deployment)を削除すると、配布されていたPodが削除されます。

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

監視後、ノード削除が発生してノード数が1個に減っていることを確認できます。ノード削除にかかる時間は設定によって異なります。

```
# kubectl get nodes
NAME                                            STATUS   ROLES    AGE   VERSION
autoscaler-test-default-w-ohw5ab5wpzug-node-0   Ready    <none>   71m   v1.23.3
```

ノード削除イベントは、下記のコマンドで確認できます。

```
# kubectl get events --field-selector reason="ScaleDown"
LAST SEEN   TYPE     REASON      OBJECT                                               MESSAGE
13m         Normal   ScaleDown   node/autoscaler-test-default-w-ohw5ab5wpzug-node-1   node removed by cluster autoscaler
13m         Normal   ScaleDown   node/autoscaler-test-default-w-ohw5ab5wpzug-node-2   node removed by cluster autoscaler
```

各ノードグループのオートスケーラーの状態情報は`configmap/cluster-autoscaler-status`で確認できます。このconfigmapはノードグループごとに別々の名前空間に作成されます。オートスケーラーが作成する各ノードグループの名前空間の名前ルールは次のとおりです。

* 形式：nhn-ng-{ノードグループ名}
* {ノードグループ名}にはノードグループの名前が入ります。
* 基本ノードグループのノードグループ名は"default-worker"です。

基本ノードグループのオートスケーラーの状態情報を確認する方法は次のとおりです。より詳細な情報は[Cluster Autoscaler FAQ](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md)を参照してください。

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
> 状態情報の内容のうち、`Cluster-wide`領域の内容は`NodeGroups`領域の内容と同じです。

#### HPA(HorizontalPodAutoscale)機能と連携した動作例
HPA(Horizontal Pod Autoscaler)機能はCPU使用量などのリソース使用量を監視してレプリケーションコントローラー(ReplicationController)、デプロイメント(Deployment)、レプリカセット(ReplicaSet)、ステートフルセット(StatefulSet)のPod数を自動的にスケールします。Pod数を調節しているとノードに可用リソースが不足したりリソースが多く残る状況が発生する場合があります。この時、オートスケーラー機能と連携してノードの数を増やしたり減らすことができます。この例ではHPA機能とオートスケーラー機能を連携して動作することを示しています。HPAの詳細な説明は[Horizontal Pod Autoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/)文書を参照してください。 

##### 1. オートスケーラー有効化
上の例のようにオートスケーラーを有効化します。

##### 2. HPA設定
Webリクエストを受けると一定時間CPU負荷を作成するコンテナを配布します。そしてサービスを表示させます。次は`php-apache.yaml`ファイルの内容です。

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

HPAを設定します。上で作成したphp-apache deploymentオブジェクトに対して最小Pod数1、最大Pod数、目標CPU loadは50%に設定します。

```
# kubectl autoscale deployment php-apache --cpu-percent=50 --min=1 --max=30
horizontalpodautoscaler.autoscaling/php-apache autoscaled
```

HPAの状態を照会すると、設定値と現在の状態を確認できます。まだCPU負荷をかけるweb requestを送っていないためCPU loadが0%です。

```
# kubectl get hpa
NAME         REFERENCE               TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   0%/50%    1         30        1          80s
```

##### 3. 負荷認可
新しいターミナルで負荷をかけるPodを実行します。このPodは無限にWebリクエストを送ります。`Ctrl+C`で止めることができます。

```
# kubectl run -i --tty load-generator --rm --image=busybox --restart=Never -- /bin/sh -c "while sleep 0.01; do wget -q -O- http://php-apache; done"
If you don't see a command prompt, try pressing enter.
OK!OK!OK!OK!OK!OK!OK!
```

`kubectl top nodes`コマンドを利用してノードの現在リソース使用量を確認できます。負荷をかけるPod実行後、時間が経つとCPU負荷が大きくなることを確認できます。

```
# kubectl top nodes
NAME                                            CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
autoscaler-test-default-w-ohw5ab5wpzug-node-0   66m          6%     1010Mi          58%

(しばらくすると)

# kubectl top nodes
NAME                                            CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
autoscaler-test-default-w-ohw5ab5wpzug-node-0   574m         57%    1013Mi          58%
```

HPAの状態を照会するとCPU loadが増加して、これを合わせるためにREPLICAS(=Pod数)の数が増えたことを確認できます。

```
# kubectl get hpa
NAME         REFERENCE               TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   250%/50%   1         30        5          2m44s
```

##### 4. オートスケーラー動作確認
Podを照会するとPodの数が増えて一部Podは`node-0`にスケジューリングされてRunning状態になりますが、一部はPending状態になっていることを確認できます。

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

Podをスケジューリングできない状況がオートスケーラーのノード増設条件です。 Cluster Autoscaler Podが提供する状態情報を照会するとScaleUpがInProgress状態になったことを確認できます。

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

しばらくするとノード(node-8)が1つ増えていることを確認できます。

```
# kubectl get nodes
NAME                                            STATUS     ROLES    AGE   VERSION
autoscaler-test-default-w-ohw5ab5wpzug-node-0   Ready      <none>   22d   v1.23.3
autoscaler-test-default-w-ohw5ab5wpzug-node-8   Ready      <none>   90s   v1.23.3
```

Pending状態だったPodが全て正常スケジューリングされてRunning状態になったことを確認できます。

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

負荷のために実行しておいたPod(`load-generator`)を`Ctrl+C`で中断させてしばらくすると負荷が減ります。負荷が減るとPodが占有していたCPU使用量が減ってPodの数が減ります。

```
# kubectl get hpa
NAME         REFERENCE               TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
php-apache   Deployment/php-apache   0%/50%    1         30        1          31m
```

Podの数が減ってノードのリソース使用量が減るとノードが縮小します。新たに追加されたnode-8が縮小したことを確認できます。

```
# kubectl get nodes
NAME                                            STATUS   ROLES    AGE   VERSION
autoscaler-test-default-w-ohw5ab5wpzug-node-0   Ready    <none>   22d   v1.23.3
```


### ユーザースクリプト(old)
クラスタを作成する時と追加ノードグループを作成する時、ユーザースクリプトを登録できます。ユーザースクリプト機能には次のような特徴があります。

* 機能設定
    * この機能はワーカーノードグループごとに設定できます。
    * クラスタ作成時に入力したユーザースクリプトは基本ワーカーノードグループに適用されます。
    * 追加ノードグループの作成時に入力したユーザースクリプトは該当ワーカーノードグループに適用されます。
    * **ワーカーノードグループが作成された後はユーザースクリプトの内容を変更できません。**
* スクリプト実行タイミング
    * ユーザースクリプトはワーカーノード初期化プロセスのうち、インスタンス初期化プロセスで実行されます。
    * ユーザースクリプトが実行された後、そのインスタンスを「ワーカーノードグループ」のワーカーノードに設定して登録します。
* スクリプト内容
    * ユーザースクリプトの最初の行は必ず#!で始まる必要があります。
    * スクリプトの最大サイズは64KBです。
    * スクリプトはroot権限で実行されます。
    * スクリプトの実行記録は以下の位置に保存されます。
        * スクリプト終了コード：`/var/log/userscript.exitcode`
        * スクリプト標準出力および標準エラーストリーム：`/var/log/userscript.output`

### ユーザースクリプト
2022年7月26日以降に作成されるノードグループには新しいバージョンのユーザースクリプト機能が搭載されます。以前のバージョンの機能と比較して次のような特徴があります。

* **ワーカーノードグループが作成された後もユーザースクリプトの内容を変更できます。**
    * ただし、変更された内容はユーザースクリプト変更後に作成されるノードにのみ適用されます。 
* スクリプト実行記録は次の位置に保存されます。
    * スクリプト終了コード： `/var/log/userscript_v2.exitcode`
    * スクリプト標準出力および標準エラーストリーム： `/var/log/userscript_v2.output`

* 以前のバージョンとの関係
    * 新規バージョンの機能が以前のバージョンの機能を代替します。
        * コンソール、 APIを介してノードグループを作成するとき、設定したユーザースクリプトは新規バージョンの機能に設定されます。
    * 以前のバージョンのユーザースクリプトを設定したワーカーノードグループは、以前のバージョンの機能と新規バージョンの機能が別々に動作します。
        * 以前のバージョンで設定したユーザースクリプトの内容は変更できません。
        * 新規バージョンで設定したユーザースクリプトの内容は変更できます。
    * 以前のバージョンと新規バージョンにそれぞれユーザースクリプトを設定すると、次の順序で実行されます。
        1. 以前のバージョンのユーザースクリプト
        2. 新規バージョンのユーザースクリプト

## クラスター管理
遠隔のホストからクラスターを操作し、管理するには、Kubernetesが提供するコマンドラインツール(CLI)、`kubectl`が必要です。

### kubectlインストール
kubectlは、インストール不要で、実行ファイルをダウンロードしてすぐに使用できます。各OSのダウンロードパスは次のとおりです。

| OS | ダウンロードコマンド |
| --- | --- |
| Linux | curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.15.7/bin/linux/amd64/kubectl |
| MacOS | curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.15.7/bin/darwin/amd64/kubectl |
| Windows | curl -LO https://storage.googleapis.com/kubernetes-release/release/v1.15.7/bin/windows/amd64/kubectl.exe |

その他、インストール方法とオプションなどの詳細は、[Install and Set Up kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)文書を参照してください。

#### 権限変更
ダウンロードしたファイルは基本的に実行権限がありません。実行権限を追加する必要があります。

```
$ chmod +x kubectl
```

#### 位置変更またはパス指定
どのパスからでもkubectlを実行できるように環境変数に指定されたパスに移すか、kubectlがあるパスを環境変数に追加します。

* 環境変数に指定したパスへ移動
```
$ sudo mv kubectl /usr/local/bin/
```

* 環境変数にパスを追加
```
// kubectlがあるパスで実行
$ export PATH=$PATH:$(pwd)
```

### 設定
kubectlでKubernetesクラスターにアクセスするには、クラスター設定ファイル(kubeconfig)が必要です。NHN Cloud Webコンソールで**Container > NHN Kubernetes Service(NKS)**サービスページを開き、アクセスするクラスターを選択します。下部、**基本情報**タブで**設定ファイル**項目の**ダウンロード**ボタンを押して設定ファイルをダウンロードします。ダウンロードした設定ファイルは、任意の位置へ移動させ、kubectl実行時に参照できるように準備します。

> [注意]
> NHN Cloud Webコンソールからダウンロードした設定ファイルは、クラスター情報と認証のためのトークンなどが含まれています。設定ファイルがある場合は該当Kubernetesクラスターにアクセスできる権限を持ちます。設定ファイルを絶対に紛失しないように注意してください。

kubectlは実行するたびにクラスター設定ファイルが必要です。したがって、毎回`--kubeconfig`オプションを利用してクラスター設定ファイルを指定する必要があります。しかし、環境変数にクラスター設定ファイルパスが保存されている場合は、毎回オプションを指定する必要はありません。

```
$ export KUBECONFIG={クラスター設定ファイルパス}
```

クラスター設定ファイルのパスを環境変数に保存したくない場合は、kubectlの基本設定ファイル、`$HOME/.kube/config`にコピーして使用することもできます。しかし、クラスターを複数運用する場合は、環境変数の値を変更する方法が便利です。

### 接続確認
`kubectl version`コマンドで、正常に設定できているかを確認します。問題がなければ`Server Version`が出力されます。

```
$ kubectl version
Client Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.7", GitCommit:"6c143d35bb11d74970e7bc0b6c45b6bfdffc0bd4", GitTreeState:"clean", BuildDate:"2019-12-11T12:42:56Z", GoVersion:"go1.12.12", Compiler:"gc", Platform:"darwin/amd64"}
Server Version: version.Info{Major:"1", Minor:"15", GitVersion:"v1.15.7", GitCommit:"6c143d35bb11d74970e7bc0b6c45b6bfdffc0bd4", GitTreeState:"clean", BuildDate:"2019-12-11T12:34:17Z", GoVersion:"go1.12.12", Compiler:"gc", Platform:"linux/amd64"}
```

* Client Version：実行したkubectlファイルのバージョン情報
* Server Version：クラスターを構成しているKubernetesのバージョン情報

### CSR(CertificateSigningRequest)
Kubernetesの認証API(Certificate API)を通してKubernetes APIクライアントのためのX.509証明書(certificate)をリクエストして発行できます。 CSRリソースは証明書をリクエストして、リクエストに対して承認/拒否を決定できるようにします。詳細事項は[Certificate Signing Requests](https://kubernetes.io/docs/reference/access-authn-authz/certificate-signing-requests/)文書を参照してください。

#### CSRリクエストと発行承認例
まず秘密鍵(private key)を作成します。証明書作成に関する詳細は[Certificates](https://kubernetes.io/docs/concepts/cluster-administration/certificates/)文書を参照してください。

```
# openssl genrsa -out dev-user1.key 2048
Generating RSA private key, 2048 bit long modulus
...........................................................................+++++
..................+++++
e is 65537 (0x010001)

# openssl req -new -key dev-user1.key -subj "/CN=dev-user1" -out dev-user1.csr
```

作成した秘密鍵情報を含むCSRリソースを作成して証明書発行をリクエストします。

```
# BASE64_CSR=$(cat dev-user1.csr | base64 | tr -d '\n')
# cat <<EOF > csr.yaml -
apiVersion: certificates.k8s.io/v1beta1
kind: CertificateSigningRequest
metadata:
  name: dev-user1
spec:
  groups:
  - system:authenticated
  request: ${BASE64_CSR}
  usages:
  - digital signature
  - key encipherment
  - server auth
  - client auth
EOF

# kubectl apply -f csr.yaml
certificatesigningrequest.certificates.k8s.io/dev-user1 created
```

登録されたCSRは`Pending`状態です。この状態は発行承認または拒否を待っている状態です。

```
# kubectl get csr
NAME        AGE   REQUESTOR          CONDITION
dev-user1   6s    system:unsecured   Pending
```

この証明書発行リクエストに対して承認処理を行います。

```
# kubectl certificate approve dev-user1
certificatesigningrequest.certificates.k8s.io/dev-user1 approved
```

CSRをもう一度確認すると`Approved,Issued`状態に変更されたことを確認できます。
```
# kubectl get csr
NAME        AGE    REQUESTOR          CONDITION
dev-user1   114s   system:unsecured   Approved,Issued
```

証明書は次のように照会できます。証明書はstatusのcertificateフィールドの値です。

```
# kubectl get csr/dev-user1 -o yaml
apiVersion: certificates.k8s.io/v1beta1
kind: CertificateSigningRequest
metadata:
  annotations:
    kubectl.kubernetes.io/last-applied-configuration: |
      {"apiVersion":"certificates.k8s.io/v1beta1","kind":"CertificateSigningRequest","metadata":{"annotations":{},"name":"dev-user1"},"spec":{"groups":["system:authenticated"],"request":"LS0tLS...(以下省略)","usages":["digital signature","key encipherment","server auth","client auth"]}}
  creationTimestamp: "2020-12-07T06:32:53Z"
  name: dev-user1
  resourceVersion: "3202"
  selfLink: /apis/certificates.k8s.io/v1beta1/certificatesigningrequests/dev-user1
  uid: b22477eb-0abc-4fc4-8a79-f6516751a940
spec:
  groups:
  - system:masters
  - system:authenticated
  request: LS0tLS...(以下省略)
  usages:
  - digital signature
  - key encipherment
  - server auth
  - client auth
  username: system:unsecured
status:
  certificate: LS0tLS...(以下省略)
  conditions:
  - lastUpdateTime: "2020-12-07T06:34:43Z"
    message: This CSR was approved by kubectl certificate approve.
    reason: KubectlApprove
    type: Approved
```

> [注意]
> この機能はクラスター作成時点が下記の期間に該当する場合にのみ提供されます。
> 
> * パンギョリージョン：2020年12月29日以降に作成したクラスター
> * 坪村リージョン：2020年12月24日以降に作成したクラスター

### 承認コントローラー(admission controller)プラグイン
承認コントローラーはKubernetes APIサーバーリクエストを奪ってオブジェクトを変更したり、リクエストを拒否できます。承認コントローラーの詳細は[承認コントローラー](https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/)を参照してください。また承認コントローラーの使用例は[承認コントローラーガイド](https://kubernetes.io/blog/2019/03/21/a-guide-to-kubernetes-admission-controllers/)を参照してください。

クラスタバージョンとクラスタ作成時点によって、承認コントローラーに適用されるプラグインの種類が異なります。詳細についてはリージョン別の作成時点によるプラグインリストを参照してください。

#### v1.19.13以前のバージョン
パンギョリージョン2021年2月22日以前に作成したクラスタおよび坪村リージョン2021年2月17日以前に作成したクラスタは次のように適用されます。

* DefaultStorageClass
* DefaultTolerationSeconds
* LimitRanger
* MutatingAdmissionWebhook
* NamespaceLifecycle
* NodeRestriction
* ResourceQuota
* ServiceAccount
* ValidatingAdmissionWebhook

パンギョリージョン2021年2月23日以降に作成したクラスタおよび坪村リージョン2021年2月18日以降に作成したクラスタは次のように適用されます。

* DefaultStorageClass
* DefaultTolerationSeconds
* LimitRanger
* MutatingAdmissionWebhook
* NamespaceLifecycle
* NodeRestriction
* PodSecurityPolicy(新規追加)
* ResourceQuota
* ServiceAccount
* ValidatingAdmissionWebhook

#### v1.20.12以降のバージョン
Kubernetesバージョン別の基本アクティブ承認コントローラーはすべて有効になります。基本アクティブ承認コントローラーに以下のコントローラーが有効になります。
* NodeRestriction
* PodSecurityPolicy


### クラスタアップグレード
NHN Kubernetes Service(NKS)は動作中のKubernetesクラスタのKubernetesコンポーネントのアップグレードをサポートします。 

#### Kubernetesバージョン違いサポートポリシー
Kubernetesのバージョンは`x.y.z`で表現されます。 `x`はメジャーバージョン、`y`はマイナーバージョン、`z`はパッチバージョンです。機能が追加される場合、メジャーバージョンまたはマイナーバージョンをアップロードし、バグ修正など以前のバージョンと互換性のある機能を提供する場合はパッチバージョンをアップロードします。詳しい内容は[こちら](https://semver.org/)を参照してください。

Kubernetesクラスタは、動作中の状態でKubernetesコンポーネントをアップグレードできます。そのため、KubernetesコンポーネントごとにKubernetesバージョンの違いによる機能をサポートするかどうかを定義しています。マイナーバージョンを基準にした段階のバージョンの違いは相互機能互換をサポートすることで動作中のクラスタのKubernetesコンポーネントアップグレードをサポートします。またコンポーネントの種類ごとにアップグレード順序を定義しています。詳しい内容は[こちら](https://kubernetes.io/releases/version-skew-policy/)を参照してください。

#### 機能動作方式
NHN CloudでサポートするKubernetesクラスタアップグレード機能の動作方式を説明します。 

##### Kubernetesバージョン管理
NHN CloudのKubernetesクラスタはクラスタマスターとワーカーノードグループごとにKubernetesバージョンを管理します。マスターのKubernetesバージョンはクラスタ照会画面で確認することができ、ワーカーノードグループのKubernetesバージョンは各ワーカーノードグループ照会画面で確認できます。 

##### アップグレードルール
NHN CloudのKubernetesクラスタバージョン管理方式とKubernetesバージョン違いサポートポリシーによりコンポーネントごとに順序に合わせてアップグレードする必要があります。NHN CloudのKubernetesクラスタアップグレード機能に適用されるルールは次のとおりです。

* マスターとワーカーノードグループごとにアップグレードコマンドを実行する必要があります。
* マスターのKubernetesバージョンとすべてのワーカーノードグループのKubernetesバージョンが一致している時のみアップグレードが可能です。
* マスターが先にアップグレードした後、ワーカーノードグループをアップグレードできます。 
* 現在バージョンの次のバージョン(マーナーバージョン基準 +1)にアップグレード可能です。 
* ダウングレードはサポートしません。 
* 他の機能の動作によりクラスタがアップデート中の状態ではアップグレードができません。

次の例はKubernetesバージョンのアップグレード可否を表にしたものです。例に使用された条件は次のとおりです。 

* NHN CloudがサポートするKubernetesバージョンリスト：v1.21.6, v1.22.3, v1.23.3
* クラスタはv1.21.6で作成

|状態 | マスターバージョン | マスターアップグレード可否 | ワーカーノードグループバージョン | ワーカーノードグループアップグレード可否
 | --- | :-: | :-: | :-: | :-: |
 | 初期状態| v1.21.6 | 可能(注1) | v1.21.6 | 不可(注2) | 
 | マスターアップグレード後の状態 | v1.22.3 | 不可(注3) | v1.21.6 | 可能(注4) | 
| ワーカーノードグループアップグレード後の状態 | v1.22.3 | 可能(注1) | v1.22.3 | 不可(注2) |
 | マスターアップグレード後の状態 | v1.23.3 | 不可(注3) | v1.22.3 | 可能(注4) | 
| ワーカーノードグループアップグレード後の状態 | v1.23.3 | 不可(注5) | v1.23.3 | 不可(注2)| 

(注1)マスターとすべてのワーカーノードグループのバージョンが一致する状態のためアップグレード可能
(注2)ワーカーノードグループはマスターがアップグレードされた後にアップグレード可能
(注3)マスターとすべてのワーカーノードグループのバージョンが一致する時のみアップグレード可能
(注4)マスターがアップグレードされたためアップグレード可能
(注5) NHN Cloudでサポートする最新バージョンを使用しているためアップグレード不可


##### マスターコンポーネントアップグレード
NHN CloudのKubernetesクラスタマスターは高可用性を保障するために多数のマスターで構成されています。マスターに対してローリングアップデート方式でアップグレードされるため、クラスタの可用性が保障されます。 

この過程で以下のようなことが発生することがあります。

* Kubernetes APIが一時的に失敗することがあります。


##### ワーカーコンポーネントアップグレード
ワーカーノードグループごとにワーカーコンポーネントをアップグレードできます。ワーカーコンポーネントアップグレードは、次の順序で行われます。

1. クラスタオートスケーラ機能を無効化します。(注1)
2. 該当ワーカーノードグループにバッファノード(注3)を追加します。
3. ワーカーノードグループ内のすべてのワーカーノードに対して順番に以下の作業を行います。(注4)
    1. 該当ワーカーノードで動作中のPodを追放して、ノードをスケジュールできない状態に切り替えます。
    2. ワーカーコンポーネントをアップグレードします。
    3. ノードをスケジュール可能な状態に切り替えます。
4. バッファノードで動作中のPodを追放してバッファノードを削除します。
5. クラスタオートスケーラ機能を再度有効にします。(注1)

(注1)この段階はアップグレード機能開始前にクラスタオートスケーラ機能が有効になっている場合にのみ有効です。
(注2)バッファノードとは、アップグレード中に既存ワーカーノードから追放されたPodがもう一度スケジューリングできるように作成しておく余裕ノードのことです。該当ワーカーノードグループで定義したワーカーノードと同じ規格のノードで作成され、アップグレードが終了する時に自動的に削除されます。このノードはInstance料金ポリシーに基づいて費用が請求されます。 
(注3)アップグレード時のバッファノード数を設定できます。デフォルト値は1で、0に設定するとバッファノードを追加しません。最小値は0で、最大値は(ノードグループ当たりの最大ノード数クォーター - 該当のワーカーノードグループの現在ノード数)です。
(注4)アップグレード時に設定した最大サービス不可ノード数だけ作業を実行します。デフォルト値は1です。最小値は1で、最大値は該当ワーカーノードグループの現在ノード数です。

この過程で以下のようなことが発生することがあります。

* サービス中のPodが追放されて他のノードにスケジューリングされます(Pod追放の詳細は以下のPod追放関連注意事項を参照してください)。
* オートスケーラ機能が動作しません。 


> [Pod追放関連注意事項]
> 1. デーモンセット(daemonset)コントローラーによるPodは追放されません。
> デーモンセットコントローラーは、ワーカーノードごとにPodを実行するため、デーモンセットコントローラーにより実行されたPodは追放されても他のノードで実行されません。ワーカーノードグループアップグレード過程でデーモンセットコントローラーにより実行されたPodは追放しません。 
> 2. ローカル記憶領域を使用するPodは追放される時、使用していたデータを失います。
> `emptyDir`を利用してノードのローカル記憶領域を使用するPodは追放される時、使用していたデータを失います。ノードのローカルに保存された記憶領域が他のノードに移動できないためです。 
> 3. 他のノードに複製ができないPodは、他のノードに移動できません。
> レプリケーションコントローラー(ReplicationController)、レプリカセット(ReplicaSet)、ジョブ(Job)、デーモンセット(Daemonset)、ステートフル(StatefulSet)などのコントローラーにより実行されたPodが追放されると、コントローラーにより他のノードにスケジューリングされます。しかしこのようなコントローラーを利用していないPodは追放された後、他のノードにスケジューリングされません。 
> 4. PodDisruptionBudgets(PDB)設定により追放に失敗したり、遅くなることがあります。
> PodDisruptionBudgets(PDB)設定で維持する必要があるPod数を定義できます。この機能設定によりアップグレード過程でPodの追放ができない場合もあり、Pod追放時間が長くなることがあります。Pod追放に失敗するとアップグレードが失敗します。したがってPDB設定が行われている場合は、適切なPDB設定でPod追放が円滑に動作するように設定する必要があります。 PDB設定の詳細は[こちら](https://kubernetes.io/docs/tasks/run-application/configure-pdb/)を参照してください。

安全なPod追放については[こちら](https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/)を参照してください。

##### システムPodアップグレード
マスターとすべてのワーカーノードグループをアップグレードしてバージョンが一致すると、Kubernetesクラスタ構成のために動作するシステムPodがアップグレードされます。

> [注意]
> マスターアップグレード後にワーカーノードグループをアップグレードしなかった場合、一部Podが正常に動作しない可能性があります。

## ワーカーノード管理

### コンテナ管理

#### Kubernetes v1.24.3以前のバージョンのクラスタ
Kubernetes v1.24.3以前のバージョンのクラスタはDockerを利用してコンテナランタイムを構成します。ワーカーノードでdocker CLIを利用してコンテナ状態照会、コンテナイメージ照会などの作業が行えます。docker CLIの詳細な説明と使用方法については[Use the Docker command line](https://docs.docker.com/engine/reference/commandline/cli/)を参照してください。

#### Kubernetes v1.24.3以降のバージョンのクラスタ

Kubernetes v1.24.3以降のバージョンのクラスタはcontainerdを利用してコンテナランタイムを構成します。ワーカーノードでdocker CLIの代わりにnerdctlを利用してコンテナ状態照会、コンテナイメージ照会などの作業ができます。Nerdctlの詳細な説明と使用方法については[nerdctl: Docker-compatible CLI for containerd](https://github.com/containerd/nerdctl#nerdctl-docker-compatible-cli-for-containerd)を参照してください。


### ネットワーク管理

#### 基本ネットワークインタフェース
すべてのワーカーノードはクラスタ作成時に入力したVPC/サブネットに接続されるネットワークインタフェースを持っています。この基本ネットワークインタフェースの名前は"eth0"で、ワーカーノードはこのネットワークインタフェースを介してマスターと接続されます。

#### 追加ネットワークインタフェース
クラスタまたはワーカーノードグループ作成時に追加ネットワークを設定すると、該当ワーカーノードグループのワーカーノードに追加ネットワークインタフェースが作成されます。追加ネットワークインタフェースは追加ネットワーク設定に入力した順序通りにインタフェース名が設定されます(eth1, eth2, ...)。

#### 基本パス(default route)設定
ワーカーノードに複数のネットワークインタフェースが存在する場合、ネットワークインタフェースごとに基本パスが設定されます。あるシステムに複数の基本パスが設定されている場合、マトリックス(metric)値が最も低い基本パスがシステム基本パスとして動作します。ネットワークインタフェースごとの基本パスはインタフェース番号が小さいほど小さいマトリックス値が設定されています。このため動作中のネットワークインタフェースのうち最も小さい番号のネットワークインタフェースがシステム基本パスとして動作します。

システム基本パスを追加ネットワークインタフェースとして設定するには、以下のような作業が必要です。

##### 1. ネットワークインタフェース別マトリックス設定の変更
ワーカーノードのすべてのネットワークインタフェースはDHCPサーバーを介してIPアドレスを割り当てられます。DHCPサーバーからIPアドレスを割り当てられる時、ネットワークインタフェースごとに基本パスを設定します。この時、各基本パスのマトリックス値はインタフェースごとにあらかじめ設定されています。各Linuxディストリビューションの保存位置および設定項目は次のとおりです。

* CentOS
    * 設定ファイルの位置：/etc/sysconfig/network-scripts/ifcfg-{ネットワークインタフェース名}
    * マトリックス値設定項目； METRIC
* Ubuntu
    * 設定ファイルの位置：/etc/systemd/network/toastcloud-{ネットワークインタフェース名}.network
    * マトリックス値設定項目：DHCPセクションのRouteMetric

> [注意]
> 基本パス別マトリックス値は基本パスが設定される時点で決定されます。
> したがって変更された設定は次の基本パス設定時に適用されます。
> 現在システムに適用されているパス別マトリックス値を変更するには以下の`現在パスのマトリックス値変更`を参照してください。

##### 2. 現在パスのマトリックス値の変更

システム基本パスを変更するためにネットワークインタフェース別基本パスのマトリックス値を調整できます。次はrouteコマンドを利用して各基本パスのマトリックス値を調整する例です。

次は作業実行前の状態です。インタフェース番号が小さいほどマトリックス値が小さく設定されていることを確認できます。
```
# route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         10.0.0.1        0.0.0.0         UG    0      0        0 eth0
0.0.0.0         192.168.0.1     0.0.0.0         UG    100    0        0 eth1
0.0.0.0         172.16.0.1      0.0.0.0         UG    200    0        0 eth2
...
```

eth1をシステム基本パスに設定するためにeth1のマトリックス値を0に、eth0のマトリックス値を100に変更します。マトリックス値のみ変更することはできないため、パスを削除して再度追加する必要があります。まずeth0のパスを削除し、eth0のマトリックス値を100に設定します。

```
# route del -net 0.0.0.0/0 dev eth0
# route add -net 0.0.0.0/0 gw 10.0.0.1 dev eth0 metric 100
```

eth1も先に既存パスを削除し、eth1のマトリックスを0に設定します。 
```
# route del -net 0.0.0.0/0 dev eth1
# route add -net 0.0.0.0/0 gw 192.168.0.1 dev eth1 metric 0
```

もう一度パスを照会するとマトリックス値が変更されていることを確認できます。
```
# route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         192.168.0.1     0.0.0.0         UG    0      0        0 eth1
0.0.0.0         10.0.0.1        0.0.0.0         UG    100    0        0 eth0
0.0.0.0         172.16.0.1      0.0.0.0         UG    200    0        0 eth2
...
```

#### ユーザースクリプト機能を利用した基本パス設定の変更
ユーザースクリプト機能を利用するとノード増設などでノードが新たに初期化される時も上記のような設定を維持できます。次のユーザースクリプトはCentOSを使用するワーカーノードでeth0のマトリックス値を100に、eth1のマトリックス値を0に設定する例です。このようにすると現在システムに適用されている基本パスごとのマトリックス値も変更され、これはワーカーノードの再起動後にも維持されます。
```
#!/bin/bash
sed -i -e 's|^METRIC=.*$|METRIC=100|g' /etc/sysconfig/network-scripts/ifcfg-eth0
sed -i -e 's|^METRIC=.*$|METRIC=0|g' /etc/sysconfig/network-scripts/ifcfg-eth1
route del -net 0.0.0.0/0 dev eth0
route add -net 0.0.0.0/0 gw 10.0.0.1 dev eth0 metric 100
route del -net 0.0.0.0/0 dev eth1
route add -net 0.0.0.0/0 gw 192.168.0.1 dev eth1 metric 0
```

## LoadBalancerサービス
Kubernetesアプリケーションの基本実行単位Podは、CNI(Container Network Interface)でクラスターネットワークに接続されます。基本的にクラスターの外部からPodにはアクセスできません。Podのサービスをクラスターの外部に公開するにはKubernetesの`LoadBalancer`サービス(Service)オブジェクト(object)を利用して外部に公開するパスを作成する必要があります。LoadBalancerサービスオブジェクトを作成すると、クラスターの外部にNHN Cloud Load Balancerが作成され、サービスオブジェクトと接続されます。

### WebサーバーPod作成
次のように2個のnginx Podを実行するデフォルトデプロイメント(deployment)オブジェクトマニフェストファイルを作成し、オブジェクトを作成します。

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

デプロイメントオブジェクトを作成すると、マニフェストに定義したPodが自動的に作成されます。

```
$ kubectl apply -f nginx.yaml
deployment.apps/nginx-deployment created

$ kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE  
nginx-deployment-7fd6966748-pvrzs   1/1     Running   0          4m13s
nginx-deployment-7fd6966748-wv7rd   1/1     Running   0          4m13s
```

NHN Cloud Container Registryに保存したイメージを使用したい場合は、先にユーザーレジストリにログインするためのシークレット(secret)を作成する必要があります。
NHN Cloud (Old) Container Registryを使用するには次のようにシークレットを作成する必要があります。

```
$ kubectl create secret docker-registry registry-credential --docker-server={ユーザーレジストリアドレス} --docker-username={NHN Cloudアカウントemailアドレス} --docker-password={サービスAppkeyまたは統合Appkey}
secret/registry-credential created

$ kubectl get secrets
NAME                  TYPE                             DATA   AGE
registry-credential   kubernetes.io/dockerconfigjson   1      30m
```


NHN Cloud Container Registryを使用するには次のようにシークレットを作成する必要があります。

```
$ kubectl create secret docker-registry registry-credential --docker-server={ユーザーレジストリアドレス} --docker-username={User Access Key ID} --docker-password={Secret Access Key}
secret/registry-credential created
$ kubectl get secrets
NAME                  TYPE                             DATA   AGE
registry-credential   kubernetes.io/dockerconfigjson   1      30m


デプロイメントマニフェストファイルにシークレット情報を追加し、イメージ名を変更すると、ユーザーレジストリに保存されたイメージを利用してPodを作成できます。

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

> [参考]
> NHN Cloud Container Registryの使用方法については、[Container Registryユーザーガイド](/Container/NCR/ko/user-guide)のドキュメントを参照してください。

### LoadBalancerサービスの作成
Kubernetesのサービスオブジェクトを定義するには、次の項目で構成されたマニフェストが必要です。

| 項目 | 説明 |
| --- | --- |
| metadata.name | サービスオブジェクトの名前 |
| spec.selector | サービスオブジェクトと接続するPodの名前 |
| spec.ports | 外部ロードバランサーから流入するトラフィックをPodに伝達するインターフェイス設定 |
| spec.ports.name | インターフェイス名 |
| spec.ports.protocol | インターフェイスで使用するプロトコル(例：TCP) |
| spec.ports.port | サービスオブジェクトの外部に公開するポート番号 |
| spec.ports.targetPort | サービスオブジェクトと接続するPodのポート番号 |
| spec.type | サービスオブジェクトタイプ |

次のようにサービスマニフェストを作成します。このLoadBalancerサービスオブジェクトは**spec.selector**に定義された名前に応じて`app: nginx`ラベルがついたPodと接続されます。そして**spec.ports**に定義されたとおりにTCP/8080ポートに流入するトラフィックをPodのTCP/80ポートへ伝達します。

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

LoadBalancerサービスオブジェクトを作成すると、クラスターの外部にロードバランサーを作成して接続するまで、若干の時間が必要です。外部ロードバランサーと接続されるまで**EXTERNAL-IP**項目が`<pending>`と表示されます。

```
$ kubectl apply -f service.yaml
service/nginx-svc created

$ kubectl get service
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)          AGE
nginx-svc    LoadBalancer   10.254.134.18   <pending>     8080:30013/TCP   11s
```

外部ロードバランサーと接続すると、**EXTERNAL-IP**項目にIPが表示されます。このIPは外部ロードバランサーのFloating IPです。

```
$ kubectl get service
NAME         TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)          AGE
nginx-svc    LoadBalancer   10.254.134.18   123.123.123.30   8080:30013/TCP   3m13s
```

> [参考]
> 作成されたロードバランサーは、**Network > Load Balancer**ページで確認できます。
> ロードバランサーのIPは、外部からアクセスできるFloating IPです。**Network > Floating IP**ページで確認できます。


### インターネットによるサービステスト
ロードバランサーに接続されたFloating IPにHTTPリクエストを送ってKubernetesクラスターのWebサーバーPodが応答するかを確認します。サービスオブジェクトのTCP/8080ポートをPodのTCP/80ポートと接続するように設定したため、TCP/8080ポートにリクエストを送る必要があります。外部ロードバランサーとサービスオブジェクト、Podが正常に接続されていれば、Webサーバーはnginx基本ページをレスポンスします。

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

### ロードバランサー詳細オプション設定
Kubernetesのサービスオブジェクトを定義する時、ロードバランサーの複数のオプションを設定できます。

#### グローバル設定とリスナー別設定
設定項目ごとにグローバル設定とリスナー別設定が可能です。グローバル設定とリスナー別設定がどちらもない場合、設定別デフォルト値を使用します。
* リスナー別設定：対象リスナーにのみ適用される設定です。
* グローバル設定：対象リスナーにリスナー別設定がない場合にこの設定を適用します。

#### リスナー別設定形式
リスナー別設定は、グローバル設定キーにリスナーを表すprefixをつけて設定できます。リスナーを表すprefixはサービスオブジェクトのポートプロトコル(`spec.ports[].protocol`)とポート番号(`spec.ports[].port`)をダッシュ(`-`)でつなげたものです。例えばプロトコルがTCPで、ポート番号が80の場合、prefixは`TCP-80`です。このポートに接続しているリスナーにセッション持続性設定を行いたい場合は.metadata.annotations下のTCP-80.loadbalancer.nhncloud/pool-session-persistenceに設定できます。

以下のマニフェストは、グローバル設定とリスナー別設定を組み合わせた例です。 

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

このマニフェストを適用すると、リスナー別設定は次の表のように設定されます。
| 項目 | TCP-80リスナー | TCP-433リスナー| 説明 |
| --- | --- | --- | --- |
| ロードバランシング方式 | SOURCE_IP | LEAST_CONNECTIONS | TCP-80リスナーはグローバル設定に基づいてSOURCE_IPに設定<br>TCP-443リスナーはリスナー別設定に基づいてLEAST_CONNECTIONSに設定 |
| セッション持続性 | SOURCE_IP | None | TCP-80リスナーはリスナー別設定に基づいてSOURCE_IPに設定<br>TCP-443リスナーはデフォルト値に基づいてNoneに設定 |
| リスナープロトコル | HTTP | TCP | TCP-80リスナーとTCP-443リスナーはどちらもリスナー別設定に基づいて設定 |

> [参考]
> 別途表示されていない機能はKubernetes v1.19.13以降のバージョンのクラスタにのみ適用可能です。
> Kubernetes v1.19.13バージョンのクラスタは2022年1月25日以降に作成されたクラスタにのみリスナー別設定が適用されます。
>

> [注意]
> 以下の機能の設定値は全て文字列形式で入力する必要があります。YAMLファイル入力形式で入力値の形式に関係なく文字列形式で入力するには入力値を二重引用符(")で囲んでください。 YAMLファイル形式の詳しい内容は[Yaml Cookbook](https://yaml.org/YAML_for_ruby.html)文書を参照してください。
>


#### セッション持続性設定
ロードバランサーのセッション持続性を設定できます。

* 設定位置は .metadata.annotations下のloadbalancer.nhncloud/pool-session-persistenceです。
* リスナー別設定を適用できます。
* 次の中から1つを設定できます。
    * 空の文字列("")：セッション持続性を「なし」に設定します。未設定時のデフォルト値です。
    * SOURCE_IP：セッション持続性をSOURCE_IPに設定します。
* ロードバランシング方式がSOURCE_IPの場合、セッション持続性設定は無視され、セッション持続性設定は'なし'に設定されます。
* v1.17.6, v1.18.19クラスタ
    * ロードバランサーの作成後は変更できません。
* v1.19.13以降のクラスタ
    * ロードバランサーの作成後も変更可能です。

#### ロードバランサーを削除する時にFloating IPアドレスを保存するかどうかの設定
ロードバランサーにはFloating IPが接続されています。ロードバランサーの削除時にロードバランサーに接続されたFloating IPを削除あるいは保存するかどうかを設定できます。

* 設定位置は.metadata.annotations下のloadbalancer.openstack.org/keep-floatingipです。
* **リスナー別設定を適用できません。**
* 次の中から1つを設定できます。
    * true：Floating IPを保存します。
    * false：Floating IPを削除します。未設定時のデフォルト値です。

> [注意]
> 2021年10月26日以前に作成されたv1.18.19クラスタは、ロードバランサーが削除される時、Floating IPが削除されない問題があります。サポートの1:1お問い合わせを通してお問い合わせください。この問題を解決するための手順を詳しくお伝えします。

#### ロードバランサーIP設定
ロードバランサーを作成するときにロードバランサーのIPを設定できます。

* 設定位置は .spec.loadBalancerIPです。
* 次のいずれかに設定できます。
  * 空の文字列("")：ロードバランサーに自動的に作成されるFloating IPを接続します。未設定時のデフォルト値です。
  * <Floating_IP>：ロードバランサーに既存のFloating IPを接続します。すでに割り当てられているが接続されていないFloating IPがある場合に使用できます。

以下はロードバランサーにユーザー指定Floating IPを接続するマニフェストの例です。

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

#### Floating IP使用設定
ロードバランサーを作成するときにFloating IPを使用するかどうかを設定できます。

* 設定位置は .metadata.annotaions下のservice.beta.kubernetes.io/openstack-internal-load-balancerです。
* 次のいずれかに設定できます。
  * true：Floating IPを使用せず、VIP(Virtual IP)を使用します。
  * false：Floating IPを使用します。未設定時のデフォルト値です。
* VIPを使用する場合は.spec.loadBalancerIP項目を一緒に設定してロードバランサーに自動的に作成されるVIPを接続する代わりにVIPを指定して接続できます。

以下はロードバランサーにユーザー指定VIPを接続するマニフェストの例です。

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

Floating IP使用設定とロードバランサーIP設定の組み合わせによって、次のように動作します。

| Floating IP使用設定 | ロードバランサーIP設定 | 説明 |
| --- | --- | --- |
| false | 未設定 | ロードバランサーにFloating IPを作成して接続します。 |
| false | 設定 | ロードバランサーに指定されたFloating IPを接続します。 |
| true | 未設定 | ロードバランサーに接続されるVIPを自動的に設定します。 |
| true | 設定 | ロードバランサーに指定されたVIPを接続します。 |


#### VPC設定
ロードバランサー作成時、ロードバランサーが接続されるVPCを設定できます。

* 設定位置は .metadata.annotaions下のloadbalancer.openstack.org/network-idです。
* 設定しない場合はクラスタ作成時に設定したVPCに設定します。

#### サブネット設定
ロードバランサー作成時、ロードバランサーが接続されるサブネットを設定できます。

* 設定位置は .metadata.annotaions下のloadbalancer.openstack.org/subnet-idです。
* 設定しない場合はクラスタ作成時に設定したサブネットに設定します。

以下はロードバランサーにVPCとサブネットを設定するマニフェスト例です。

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

#### リスナー接続制限設定
リスナーの接続制限を設定できます。

* 設定位置は .metadata.annotations下のloadbalancer.nhncloud/connection-limitです。
* リスナー別設定を適用できます。
* v1.17.6, v1.18.19クラスタ
    * 最小値1、最大値60000です。
    * 設定しない場合は-1に設定され、実際のロードバランサーに適用される値は2000です。
* v1.19.13以降のクラスタ
    * 最小値1、最大値60000です。
    * 設定しない場合や範囲外の値を入力した場合はデフォルト値の60000に設定されます。


#### リスナープロトコル設定
リスナーのプロトコルを設定できます。

* 設定位置は .metadata.annotations下のloadbalancer.nhncloud/listener-protocolです。
* リスナー別設定を適用できます。
* 次のいずれかに設定できます。
    * TCP：未設定時のデフォルト値です。
    * HTTP
    * HTTPS
    * TERMINATED_HTTPS: TERMINATED_HTTPSに設定します。 SSLバージョン、証明書、秘密鍵情報を追加設定する必要があります。

> [注意]
> リスナープロトコル設定はサービスオブジェクトを変更してもロードバランサーに適用されません。 
> リスナープロトコル設定を変更するにはサービスオブジェクトを削除した後、再度作成する必要があります。
> この場合、ロードバランサーが削除された後、再び作成されますのでご注意ください。


SSLバージョンは次のように設定できます。

* 設定位置は .metadata.annotations下のloadbalancer.nhncloud/listener-terminated-https-tls-versionです。
* リスナー別設定を適用できます。
* 次のいずれかに設定できます。
    * TLSv1.3：未設定時のデフォルト値です。
    * TLSv1.2
    * TLSv1.1
    * TLSv1.0_2016
    * TLSv1.0
    * SSLv3

> [注意]
> TLSv1.3は2022年3月29日以降に作成されたクラスタで設定可能です。


証明書情報は次のように設定できます。

* 設定位置は .metadata.annotations下のloadbalancer.nhncloud/listener-terminated-https-certです。
* リスナー別設定を適用できます。
* 開始行と終了行を含める必要があります。

秘密鍵情報は次のように設定できます。

* 設定位置は .metadata.annotations下のloadbalancer.nhncloud/listener-terminated-https-keyです。
* リスナー別設定を適用できます。
* 開始行と終了行を含める必要があります。

次はリスナープロトコルをTERMINATED_HTTPSに設定したときのマニフェスト例です。証明書情報と秘密鍵情報は一部省略されています。
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

#### ロードバランシング方式設定
ロードバランシング方式を設定できます。

* 設定位置は .metadata.annotations下のloadbalancer.nhncloud/pool-lb-methodです。
* リスナー別設定を適用できます。
* 次のいずれかに設定できます。
    * ROUND_ROBIN：未設定時のデフォルト値です。
    * LEAST_CONNECTIONS
    * SOURCE_IP


#### ヘルスチェックプロトコル設定
ヘルスチェックプロトコルを設定できます。

* 設定位置は .metadata.annotations下のloadbalancer.nhncloud/healthmonitor-typeです。
* リスナー別設定を適用できます。
* 次のいずれかに設定できます。
    * HTTP：HTTP URL、HTTPメソッド、 HTTPステータスコードを追加設定する必要があります。
    * HTTPS：HTTP URL、HTTPメソッド、 HTTPステータスコードを追加設定する必要があります。
    * TCP：未設定時のデフォルト値です。

HTTP URLは次のように設定できます。

* 設定位置は .metadata.annotations下のloadbalancer.nhncloud/healthmonitor-http-urlです。
* リスナー別設定を適用できます。
* 設定値は /で始まる必要があります。
* 設定しない場合やルールに合わない値を入力した場合はデフォルト値である /に設定されます。

HTTPメソッドは次のように設定できます。

* 設定位置は .metadata.annotations下のloadbalancer.nhncloud/healthmonitor-http-methodです。
* リスナー別設定を適用できます。
* 現在GETのみサポートしており、設定していない場合や他の値を入力した場合はデフォルト値であるGETに設定されます。

HTTPステータスコードは次のように設定できます。

* 設定位置は .metadata.annotations下のloadbalancer.nhncloud/healthmonitor-http-expected-codeです。
* リスナー別設定を適用できます。
* 単一値(例：200)、リスト(例：200,202)、範囲(例：200-204)形式で入力できます。
* 設定しない場合やルールに合わない値を入力するとデフォルト値の200に設定されます。

#### ヘルスチェックサイクルの設定
ヘルスチェック周期を設定できます。

* 設定位置は.metadata.annotations下のloadbalancer.nhncloud/healthmonitor-delayです。
* リスナー別設定を適用できます。
* 秒単位で設定します。
* 最小値1、最大値5000です。
* 設定しない場合や範囲外の値を入力するとデフォルト値の60に設定されます。

#### ヘルスチェック最大レスポンス時間設定
ヘルスチェック最大レスポンス時間を設定できます。

* 設定位置は .metadata.annotations下のloadbalancer.nhncloud/healthmonitor-timeoutです。
* リスナー別設定を適用できます。
* 秒単位で設定します。
* 最小値1、最大値5000です。
* この設定は必ずヘルスチェックサイクル設定値より小さくする必要があります。
* 設定しない場合や範囲外の値を入力するとデフォルト値の30に設定されます。
* ただし、入力値または設定値がヘルスチェックサイクル設定より大きい場合は、ヘルスチェックサイクル設定の1/2に設定されます。

#### ヘルスチェック最大再試行回数設定
ヘルスチェック最大再試行回数を設定できます。

* 設定位置は .metadata.annotations下のloadbalancer.nhncloud/healthmonitor-max-retriesです。
* リスナー別設定を適用できます。
* 最小値1、最大値10です。
* 設定しない場合や範囲外の値を入力するとデフォルト値の3に設定されます。


## イングレスコントローラー
イングレスコントローラー(Ingress Controller)は、イングレスオブジェクトに定義されているルールを参照してクラスタ外部から内部サービスにHTTPとHTTPSリクエストをルーティングし、SSL/TSL終了、仮想ホスティングなどを提供します。イングレスコントローラーとイングレスの詳細については[イングレスコントローラー](https://kubernetes.io/ko/docs/concepts/services-networking/ingress-controllers/)、[イングレス](https://kubernetes.io/ko/docs/concepts/services-networking/ingress/)文書を参照してください。


### NGINX Ingress Controllerのインストール
NGINX Ingress Controllerは、よく使われるイングレスコントローラーの1つです。詳細については[NGINX Ingress Controller](https://kubernetes.github.io/ingress-nginx/)と[NGINX Ingress Controller for Kubernetes](https://www.nginx.com/products/nginx-ingress-controller/)文書を参照してください。 NGINX Ingress Controllerのインストールは[Installation Guide](https://kubernetes.github.io/ingress-nginx/deploy/)文書を参照してください。

### URIベースのサービス分岐
イングレスコントローラーはURIに基づいてサービスを分岐できます。次の図はURIに基づいてサービスを分岐する簡単な例の構造を表しています。

![ingress-01.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/ingress-01.png)

#### サービスとPod作成
次のようにサービスとPodを作成するためのマニフェストを作成します。 `tea-svc`サービスには`tea` Podを接続し、`coffee-svc`サービスには`coffee` Podを接続します。

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

マニフェストを適用し、デプロイメント、サービス、 Podが作成されたことを確認します。Podは**Running**状態でなければなりません。

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

#### イングレス(Ingress)作成
リクエストパスに基づいてサービスに接続するイングレスマニフェストを作成します。エンドポイントが`/tea`のリクエストは`tea-svc`サービスに接続し、`/coffee`のリクエストは`coffee-svc`サービスに接続します。

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

イングレスを作成し、しばらくしてから確認したときに**ADDRESS**フィールドにIPが設定されている必要があります。

```
$ kubectl apply -f cafe-ingress-uri.yaml
ingress.networking.k8s.io/cafe-ingress-uri created

$ kubectl get ingress cafe-ingress-uri
NAME               CLASS   HOSTS   ADDRESS          PORTS   AGE
cafe-ingress-uri   nginx   *       123.123.123.44   80      23s
```

#### HTTPリクエストの送信
外部ホストからingressの**ADDRESS**フィールドに設定されたIPアドレスにHTTPリクエストを送信してイングレスが正しく設定されていることを確認します。

エンドポイント`/coffee`に対するリクエストは`coffee-svc`サービスに渡され、`coffee` Podがレスポンスします。レスポンスの**Server name**項目を見ると`coffee` Podがラウンドロビン方式で交互にレスポンスすることを確認できます。

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

同様に、エンドポイント`/tea`に対するリクエストは`tea-svc`サービスに渡され、`tea` Podがレスポンスします。

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

定義されていないURIにリクエストを送信すると、イングレスコントローラーが`404 Not Found`をレスポンスします。

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

#### リソース削除
テストに使用したリソースは作成するときに使用したマニフェストを利用して削除できます。

```
$ kubectl delete -f cafe-ingress-uri.yaml
ingress.networking.k8s.io "cafe-ingress-uri" deleted

$ kubectl delete -f cafe.yaml
deployment.apps "coffee" deleted
service "coffee-svc" deleted
deployment.apps "tea" deleted
service "tea-svc" deleted
```

### ホストベースのサービス分岐
イングレスコントローラーはホスト名に基づいてサービスを分岐できます。次の図はホスト名に基づいてサービスを分岐する簡単な例の構造を表しています。

![ingress-02.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/ingress-02.png)

#### サービスとPod作成
[URIベースのサービス分岐](/Container/NKS/ja/user-guide/#uri)と同じマニフェストを利用してサービスとPodを作成します。

#### イングレス作成
ホスト名に基づいてサービスに接続するイングレスマニフェストを作成します。 `tea.cafe.example.com`ホストに入ったリクエストは`tea-svc`サービスに接続し、`coffee.cafe.example.com`ホストに入ったリクエストは`coffee-svc`サービスに接続します。

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

イングレスを作成し、しばらくしてから確認したときに**ADDRESS**フィールドにIPが設定されている必要があります。

```
$ kubectl apply -f cafe-ingress-host.yaml
ingress.networking.k8s.io/cafe-ingress-host created

$ kubectl get ingress
NAME                CLASS   HOSTS                                          ADDRESS          PORTS   AGE
cafe-ingress-host   nginx   tea.cafe.example.com,coffee.cafe.example.com   123.123.123.44   80      36s
```

#### HTTP Request送信
外部ホストからイングレスのADDRESSに設定されたIPにHTTPリクエストを送信します。ただしホスト名を利用してサービスを分岐するようにイングレスを構成したため、ホスト名を利用してリクエストを送信する必要があります。

> [参考]
> 任意のホスト名を使用してテストするにはcurlの --resolveオプションを使用します。 --resolveオプションは`{ホスト名}:{ポート番号}:{IP}`の形式で入力します。これは{ホスト名}に送る{ポート番号}のリクエストを{IP}で解析(resolve)しろという意味です。
> `/etc/host`ファイルを開き`{IP} {ホスト名}`形式で追加することもできます。

ホスト`coffee.cafe.example.com`にリクエストを送信すると`coffee-svc`サービスに渡されて`coffee` Podがレスポンスします。

```
$ curl --resolve coffee.cafe.example.com:80:123.123.123.44 http://coffee.cafe.example.com/
Server address: 10.100.24.27:8080
Server name: coffee-7c86d7d67c-fqn6n
Date: 11/Mar/2022:06:40:59 +0000
URI: /
Request ID: 1efb60d29891d6d48b5dcd9f5e1ba66d
```

ホスト`tea.cafe.example.com`にリクエストを送信すると`tea-svc`サービスに渡されて`tea` Podがレスポンスします。

```
$ curl --resolve tea.cafe.example.com:80:123.123.123.44 http://tea.cafe.example.com/
Server address: 10.100.24.28:8080
Server name: tea-5c457db9-ngrxq
Date: 11/Mar/2022:06:41:39 +0000
URI: /
Request ID: 5a6cc490893636029766b02d2aab9e39
```

不明なホストにリクエストを送るとイングレスコントローラーが`404 Not Found`を返します。

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

## Kubernetesダッシュボード
NHN Kubernetes Service(NKS)は基本Web UIダッシュボード(dashboard)を提供します。 Kubernetesダッシュボードの詳細については[Web UI (ダッシュボード)](https://kubernetes.io/ko/docs/tasks/access-application-cluster/web-ui-dashboard/)文書を参照してください。

### ダッシュボードサービス公開
ユーザーKubernetesにはダッシュボードを公開するための`kubernetes-dashboard`サービスオブジェクトがあらかじめ作成されています。

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

しかし`kubernetes-dashboard`サービスオブジェクトはClusterIPタイプのため、まだクラスタ外部に公開されていません。ダッシュボードを外部公開するにはサービスオブジェクトをLoadBalancerタイプに変更するか、イングレスコントローラーとイングレスオブジェクトを作成する必要があります。

#### LoadBalancerサービスオブジェクトに変更

`LoadBalancer`タイプにサービスオブジェクトを変更すると、クラスタ外部にNHN Cloud Load Balancerが作成され、ロードバランサーとサービスオブジェクトに接続されます。ロードバランサーに接続したサービスオブジェクトを照会すると**EXTERNAL-IP**フィールドにロードバランサーのIPが表示されます。 `LoadBalancer`タイプのサービスオブジェクトについては[LoadBalancerサービス](/Container/NKS/ja/user-guide/#loadbalancer)を参照してください。次の図は`LoadBalancer`タイプのサービスを利用してダッシュボードを外部に公開する構造を表しています。

![dashboard-01.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/dashboard-01.png)

次のように`kubernetes-dashboard`サービスオブジェクトのタイプを`LoadBalancer`に変更します。

```
$ kubectl -n kube-system patch svc/kubernetes-dashboard -p '{"spec":{"type":"LoadBalancer"}}'
service/kubernetes-dashboard patched
```

`kubernetes-dashboard`サービスオブジェクトが`LoadBalancer`タイプに変更されると、しばらくした後**EXTERNAL-IP**フィールドでロードバランサーIPを確認できます。

```
$ kubectl get svc -n kube-system
NAME                   TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)                  AGE
...
kubernetes-dashboard   LoadBalancer   10.254.95.176   123.123.123.81   443:30963/TCP            2d23h
```

> [参考]
> 作成されたロードバランサーは **Network > Load Balancer**ページで確認できます。
> ロードバランサーのIPは外部からアクセスできるFloating IPです。 **Network > Floating IP**ページで確認できます。

Webブラウザで`https://{EXTERNAL-IP}`に接続するとKubernetesダッシュボードページがローディングされます。ログインのために必要なトークンは[ダッシュボードアクセストークン](/Container/NKS/ja/user-guide/#_49)を参照してください。

> [参考]
> Kubernetesダッシュボードは自動作成されるプライベート証明書を使用するため、Webブラウザの種類とセキュリティ設定によっては安全ではないページと表示されることがあります。

#### イングレス(Ingress)を利用したサービス公開

イングレスは、クラスタ内部の複数のサービスにアクセスするためのルーティングを提供するネットワークオブジェクトです。イングレスオブジェクトの設定は、イングレスコントローラーで動作します。 `kubernetes-dashboard`サービスオブジェクトをイングレスを介して公開できます。イングレスとイングレスコントローラーの詳細については[イングレスコントローラー](/Container/NKS/ja/user-guide/#_42)を参照してください。次の図はイングレスを介してダッシュボードを外部に公開する構造を表しています。

![dashboard-02.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/dashboard-02.png)

[NGINX Ingress Controllerインストール](/Container/NKS/ja/user-guide/#nginx-ingress-controller)を参照して`NGINX Ingress Controller`をインストールして次のようにイングレスオブジェクトを作成するためのマニフェストを作成します。

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

マニフェストを適用してイングレスを作成し、イングレスオブジェクトの**ADDRESS**フィールドを確認します。

```
$ kubectl apply -f kubernetes-dashboard-ingress-tls-passthrough.yaml
ingress.networking.k8s.io/k8s-dashboard-ingress created

$ kubectl get ingress -n kube-system
NAME                    CLASS   HOSTS   ADDRESS          PORTS     AGE
k8s-dashboard-ingress   nginx   *       123.123.123.44   80, 443   34s
```

Webブラウザで`https://{ADDRESS}`に接続するとKubernetesダッシュボードページがローディングされます。ログインのために必要なトークンは[ダッシュボードアクセストークン](/Container/NKS/ja/user-guide/#_49)を参照してください。

### ダッシュボードアクセストークン
Kubernetesダッシュボードにログインするにはトークンが必要です。トークンは次のコマンドで取得できます。

```
# SECRET_NAME=$(kubectl -n kube-system get secrets | grep "kubernetes-dashboard-token" | cut -f1 -d ' ')

$ kubectl describe secret $SECRET_NAME -n kube-system | grep -E '^token' | cut -f2 -d':' | tr -d " "
eyJhbGc...-QmXA
```

出力されたトークンをブラウザのトークン入力ウィンドウに入力すると、クラスタ管理者権限を付与されたユーザーとしてログインできます。


## パシステントボリューム
パシステントボリューム(Persistent Volume, PV)は物理記憶装置(volume)を表すKubernetesのリソースです。1つのPVは1つのNHN Cloud Block Storageに接続されます。詳細については[パシステントボリューム](https://kubernetes.io/ko/docs/concepts/storage/persistent-volumes/)文書を参照してください。

PVをPodに接続して使用するにはパシステントボリュームクレーム(Persistent Volume Claims, PVC)オブジェクトが必要です。 PVCは容量、読み取り/書き込みモードなど必要なボリュームの要求事項を定義します。

PVとPVCでユーザーは使用したいボリュームのプロパティを定義し、システムはユーザーの要求事項に合ったボリュームリソースを割り当てる方式でリソースの使用と管理を分離します。

### PV/PVCのライフサイクル
PVとPVCは4段階のライフサイクル(life cycle)に従います。

* プロビジョニング(provisioning)
[ストレージクラス](https://kubernetes.io/ko/docs/concepts/storage/storage-classes/)を使用してユーザーが直接ボリュームを確保し、PVを作成(static provisioning)したり、動的に作成(dynamic provisioning)できます。

* バインディング(binding)
PVとPVCを1：1でバインディングします。動的プロビジョニングでPVを作成した場合はバインディングも自動的に行われます。

* 使用(using)
PVをPodにマウントして使用します。

* 変換(reclaiming)
使用を終えたボリュームを回収します。回収方法は削除(Delete)、保存(Retain)、再使用(Recycle)があります。

| 方法 | 説明 |
| --- | --- |
| 削除(Delete) | PVを削除するとき、接続しているボリュームを一緒に削除します。 |
| 保存(Retain) | PVを削除するとき、接続しているボリュームを削除しません。ボリュームはユーザーが直接削除するか再利用できます。 |
| 再利用(Recycle) | PVを削除するとき、接続しているボリュームを削除せず、再利用できる状態にします。この方法は停止(deprecated)しています。 |

### ストレージクラス(StorageClass)
プロビジョニングを行うには、まずストレージクラスが定義されている必要があります。ストレージクラスは特定の特性でストレージを分類できる方法を提供します。ストレージ提供者(provisioner)の情報を含め、メディアの種類やアベイラビリティゾーンなどを設定できます。 

#### ストレージ提供者(provisioner)
ストレージの提供者情報を設定します。Kubernetesバージョンに応じてサポートされているストレージプロバイダー情報は次のとおりです。

* v1.19.13以前のバージョン：provisionerフィールドを必ず`kubernetes.io/cinder`に設定する必要があります。
* v1.20.12以降のバージョン：provisionerフィールドを`cinder.csi.openstack.org`に設定して使用できます。

#### パラメータ(parameter)
ストレージクラスを介して次のパラメータを設定できます。

* ストレージ種類(type)：ストレージの種類を入力します。(未入力の場合はGeneral HDDが設定されます)
    * **General HDD**：ストレージ種類がHDDに設定されます。
    * **General SSD**：ストレージ種類がSSDに設定されます。
* アベイラビリティゾーン(availability)：アベイラビリティゾーンを設定します。(未入力の場合はランダムに設定されます)
    * パンギョリージョン：**kr-pub-a**または**kr-pub-b**
    * 坪村リージョン：**kr2-pub-a**または**kr2-pub-b**

#### ボリュームバインディングモード(VolumeBindingMode)
ボリュームバインディングモードは、ボリュームバインディングと動的プロビジョニングの開始時点を制御します。この設定はストレージ提供者がcinder.csi.openstack.orgの場合にのみ設定できます。

* **Immediate**：パシステントボリュームクレームが作成されるとすぐにボリュームバインディングと動的プロビジョニングが始まります。パシステントボリュームクレームが作成される時点ではボリュームを接続するPodの事前知識がない状態です。そのためボリュームのアベイラビリティゾーンとPodがスケジューリングされるノードのアベイラビリティゾーンが異なる場合はPodが正常に動作しません。 
* **WaitForFirstConsumer**：パシステントボリュームクレームが作成されるときはボリュームバインディングと動的プロビジョニングを行いません。このパシステントボリュームクレームが初めてPodに接続すると、 Podがスケジューリングされたノードのアベイラビリティゾーン情報をもとにボリュームバインディングと動的プロビジョニングを行います。したがってImmediateモードなど、ボリュームのアベイラビリティゾーンとインスタンスのアベイラビリティゾーンが異なりPodが正常に動作しない場合が発生しません。

#### ボリューム拡張許可(allowVolumeExpansion)
作成されたボリュームの拡張を許可するかどうかを設定します(未入力の場合はfalseが設定されます)。

* **True**：ボリューム拡張を許可します。
* **False**：ボリューム拡張を許可しません。

#### 例1
以下のストレージクラスマニフェストはv1.19.13以前のバージョンを使用するKubernetesクラスタで使用できます。パラメータを介してアベイラビリティゾーンとボリュームタイプを指定できます。

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

#### 例2
次のストレージクラスマニフェストは、v1.20.12以降のバージョンを使用するKubernetesクラスタで使用できます。ボリュームバインディングモードをWaitForFirstConsumerに設定してパシステントボリュームクレームがPodに接続されるときにボリュームバインディングと動的プロビジョニングを開始します。

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


### 静的プロビジョニング

静的プロビジョニング(static provisioning)は、ユーザーが直接ブロックストレージを準備する必要があります。 NHN Cloud Webコンソールの**Storage > Block Storage**サービスページで**ブロックストレージ作成**ボタンをクリックしてPVに接続するブロックストレージを作成します。ブロックストレージガイドの[ブロックストレージ作成](/Storage/Block%20Storage/ko/console-guide/#_1)を参照してください。

PVを作成するにはブロックストレージのIDが必要です。**Storage > Block Storage**サービスページのブロックストレージリストから使用するブロックストレージを選択します。下の**情報**タブのブロックストレージ名項目でIDを確認できます。

ブロックストレージと接続するPVマニフェストを作成します。**spec.storageClassName**にはストレージクラス名を入力します。 NHN Cloud Block Storageを使用するには**spec.accessModes**は必ず`ReadWriteOnce`に設定する必要があります。**spec.presistentVolumeReclaimPolicy**は`Delete`または`Retain`に設定できます。

> [注意]
> Kubernetesバージョンに合ったストレージ提供者が定義されているストレージクラスを設定する必要があります。

```yaml
# pv-static.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-static-001
spec:
  capacity:
    storage: 10Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Delete
  storageClassName: sc-default
  cinder:
    fsType: "ext3"
    volumeID: "e6f95191-d58b-40c3-a191-9984ce7532e5"
```

PVを作成して確認します。

```
$ kubectl apply -f pv-static.yaml
persistentvolume/pv-static-001 created

$ kubectl get pv -o wide
NAME            CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE   VOLUMEMODE
pv-static-001   10Gi       RWO            Delete           Available           sc-default              7s    Filesystem
```

作成したPVを使用するためのPVCマニフェストを作成します。**spec.volumeName**にはPVの名前を指定する必要があります。他の項目はPVマニフェストの内容と同じように設定します。

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

PVCを作成して確認します。

```
$ kubectl apply -f pvc-static.yaml
persistentvolumeclaim/pvc-static created

$ kubectl get pvc -o wide
NAME         STATUS   VOLUME          CAPACITY   ACCESS MODES   STORAGECLASS   AGE   VOLUMEMODE
pvc-static   Bound    pv-static-001   10Gi       RWO            sc-default     7s    Filesystem
```

PVCを作成した後、PVの状態を照会してみると**CLAIM**項目にPVC名が指定され、**STATUS**項目が`Bound`に変更されていることを確認できます。

```
$ kubectl get pv -o wide
NAME            CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                STORAGECLASS   REASON   AGE   VOLUMEMODE
pv-static-001   10Gi       RWO            Delete           Bound    default/pvc-static   sc-default              79s   Filesystem
```


### 動的プロビジョニング

動的プロビジョニング(dynamic provisioning)はストレージクラスに定義されているプロパティを参照して自動的にブロックストレージを作成します。動的プロビジョニングを使用するには、ストレージクラスのボリュームバインディングモード設定を行わないか、**Immediate**に設定する必要があります。

```yaml
# storage_class_csi_dynamic.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: csi-storageclass-dynamic
provisioner: cinder.csi.openstack.org
volumeBindingMode: Immediate
```

動的プロビジョニングはPVを作成する必要がありません。したがってPVCマニフェストには**spec.volumeName**を設定しません。

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

ボリュームバインディングモードを設定しない場合や**Immediate**に設定してPVCを作成する場合はPVが自動的に作成されます。 PVに接続されたブロックストレージも自動的に作成され、NHN Cloud Webコンソール**Storage > Block Storage**サービスページのブロックストレージリストで確認できます。

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
> 動的プロビジョニングで作成されたブロックストレージはWebコンソールから削除できません。またクラスタを削除するとき、自動的に削除されません。したがってクラスタを削除する前にPVCをすべて削除する必要があります。PVCを削除せずにクラスタを削除すると課金されることがあります。動的プロビジョニングを作成されたPVCのreclaimPolicyは基本的に`Delete`に設定されるため、PVを削除するだけでPVとブロックストレージが削除されます。


### PodにPVCマウント

PodにPVCをマウントするにはPodマニフェストにマウント情報を定義する必要があります。 `spec.volumes.persistenVolumeClaim.claimName`に使用するPVC名を入力します。そして`spec.containers.volumeMounts.mountPath`にマウントするパスを入力します。

次の例は静的プロビジョニングで作成したPVCをPodの`/usr/share/nginx/html`にマウントします。

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

Podを作成し、ブロックストレージがマウントされていることを確認します。

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

NHN Cloud Webコンソール**Storage > Block Storage**サービスページでもブロックストレージの接続情報を確認できます。

#### ボリューム拡張の許可(allowVolumeExpansion)
作成されたボリュームの拡張を許可するかどうかを設定します(未入力の場合はfalseが設定されます)。

* **True**：ボリュームの拡張を許可します。
* **False**：ボリュームの拡張を許可しません。

#### 例1
以下のストレージクラスマニフェストはv1.19.13以前のバージョンを使用するKubernetesクラスタで使用できます。パラメータを利用してアベイラビリティゾーンとボリュームタイプを指定できます。

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
以下のストレージクラスマニフェストはv1.20.12以降のバージョンを使用するKubernetesクラスタで使用できます。ボリュームバインディングモードをWaitForFirstConsumerに設定してパシステントボリュームクレームがPodに接続される時にボリュームバインディングと動的プロビジョニングを開始します。

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


### 静的プロビジョニング

静的プロビジョニング(static provisioning)はユーザーが直接ブロックストレージを準備する必要があります。NHN Cloud Webコンソールの**Storage > Block Storage**サービスページで**ブロックストレージ作成**ボタンをクリックしてPVと接続するブロックストレージを作成します。ブロックストレージガイドの[ブロックストレージ作成](/Storage/Block%20Storage/ko/console-guide/#_2)を参照してください。

PVを作成するにはブロックストレージのIDが必要です。**Storage > Block Storage**サービスページのブロックストレージリストから使用するブロックストレージを選択します。下部の**情報**タブのブロックストレージ名項目でIDを確認できます。

ブロックストレージと接続するPVマニフェストを作成します。**spec.storageClassName**にはストレージクラス名を入力します。NHN Cloud Block Storageを使用するには**spec.accessModes**は必ず`ReadWriteOnce`に設定する必要があります。**spec.presistentVolumeReclaimPolicy**は`Delete`または`Retain`に設定できます。

> [注意]
> Kubernetesバージョンに合ったストレージプロバイダーが定義されたストレージクラスを設定する必要があります。

```yaml
# pv-static.yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-static-001
spec:
  capacity:
    storage: 10Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Delete
  storageClassName: sc-default
  cinder:
    fsType: "ext3"
    volumeID: "e6f95191-d58b-40c3-a191-9984ce7532e5"
```

PVを作成し、確認します。

```
$ kubectl apply -f pv-static.yaml
persistentvolume/pv-static-001 created

$ kubectl get pv -o wide
NAME            CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE   VOLUMEMODE
pv-static-001   10Gi       RWO            Delete           Available           sc-default              7s    Filesystem
```

作成したPVを使用するためのPVCマニフェストを作成します。**spec.volumeName**にはPVの名前を指定する必要があります。他の項目はPVマニフェストの内容と同じように設定します。

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

PVCを作成し、確認します。

```
$ kubectl apply -f pvc-static.yaml
persistentvolumeclaim/pvc-static created

$ kubectl get pvc -o wide
NAME         STATUS   VOLUME          CAPACITY   ACCESS MODES   STORAGECLASS   AGE   VOLUMEMODE
pvc-static   Bound    pv-static-001   10Gi       RWO            sc-default     7s    Filesystem
```

PVCを作成した後、PVの状態を照会してみると、**CLAIM**項目にPVC名が指定され、**STATUS**項目が`Bound`に変更されていることを確認できます。

```
$ kubectl get pv -o wide
NAME            CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                STORAGECLASS   REASON   AGE   VOLUMEMODE
pv-static-001   10Gi       RWO            Delete           Bound    default/pvc-static   sc-default              79s   Filesystem
```


### 動的プロビジョニング

動的プロビジョニング(dynamic provisioning)はストレージクラスに定義されたプロパティを参照して自動的にブロックストレージを作成します。動的プロビジョニングを使用するにはストレージクラスのボリュームバインディングモードを設定しないか、**Immediate**に設定する必要があります。

```yaml
# storage_class_csi_dynamic.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: csi-storageclass-dynamic
provisioner: cinder.csi.openstack.org
volumeBindingMode: Immediate
```

動的プロビジョニングはPVを作成する必要がありません。したがってPVCマニフェストには**spec.volumeName**を設定しません。

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

ボリュームバインディングモードを設定しないか、**Immediate**に設定してPVCを作成すると、PVが自動的に作成されます。PVに接続されたブロックストレージも自動的に作成され、NHN Cloud Webコンソール**Storage > Block Storage**サービスページのブロックストレージリストで確認できます。

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
> 動的プロビジョニングによって作成されたブロックストレージはWebコンソールから削除できません。またクラスタを削除する時に自動的に削除されません。したがってクラスタを削除する前にPVCを全て削除する必要があります。PVCを削除せずにクラスタを削除すると課金される可能性があります。動的プロビジョニングを作成されたPVのreclaimPolicyは基本的に`Delete`に設定されるため、PVCのみ削除してもPVとブロックストレージが削除されます。


### PodにPVCマウント

PodにPVCをマウントするにはPodマニフェストにマウント情報を定義する必要があります。`spec.volumes.persistenVolumeClaim.claimName`に使用するPVC名を入力します。そして`spec.containers.volumeMounts.mountPath`にマウントするパスを入力します。

以下の例は静的プロビジョニングで作成したPVCをPodの`/usr/share/nginx/html`にマウントします。

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

Podを作成し、ブロックストレージがマウントされていることを確認します。

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

NHN Cloud Webコンソール**Storage > Block Storage**サービスページでもブロックストレージの接続情報を確認できます。

### ボリューム拡張
PersistentVolumeClaim (PVC)オブジェクトを編集して既存ボリュームのサイズを調整できます。PVCオブジェクトの**spec.resources.requests.storage**項目を修正することでボリュームサイズを変更できます。ボリューム縮小はサポートされません。ボリューム拡張機能を使用するにはStorageClassの**allowVolumeExpansion**プロパティが**True**である必要があります。


#### v1.19.13以前のバージョンのボリューム拡張
v1.19.13以前のバージョンのストレージプロバイダー**kubernetes.io/cinder**は使用中のボリュームの拡張機能を提供しません。使用中のボリュームの拡張機能を使用するにはv1.20.12以降のバージョンの**cinder.csi.openstack.org**ストレージプロバイダーを使用する必要があります。クラスタアップグレード機能を利用してv1.20.12以降のバージョンにアップグレードして**cinder.csi.openstack.org**ストレージプロバイダーを使用できます。

v1.19.13以前のバージョンの**kubernetes.io/cinder**ストレージプロバイダーの代わりにv1.20.12以降のバージョンの**cinder.csi.openstack.org**ストレージプロバイダーを使用するためにPVCのアノテーションを以下のように修正する必要があります。
+ ~~pv.kubernetes.io/bind-completed: "yes"~~ > 削除
+ ~~pv.kubernetes.io/bound-by-controller: "yes"~~ > 削除
+ ~~volume.beta.kubernetes.io/storage-provisioner: kubernetes.io/cinder~~ > volume.beta.kubernetes.io/storage-provisioner:cinder.csi.openstack.org
+ ~~volume.kubernetes.io/storage-resizer: kubernetes.io/cinder~~ > volume.kubernetes.io/storage-resizer: cinder.csi.openstack.org
+ pv.kubernetes.io/provisioned-by:cinder.csi.openstack.org > 追加


以下は修正されたPVCの例です。

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

#### v1.20.12以降のバージョンのボリューム拡張
v1.20.12以降のバージョンのストレージプロバイダー**cinder.csi.openstack.org**は基本的に使用中のボリュームの拡張機能をサポートします。PVCオブジェクトの**spec.resources.requests.storage**項目の値を修正してボリュームサイズを変更できます。


### NASサービス連動
NHN Cloudで提供するNASストレージをPVとして活用できます。NASサービスを使用するにはv1.20以降のバージョンのクラスタを使用する必要があります。NHN Cloud NASの詳細については[NASコンソール使用ガイド](/Storage/NAS/ko/console-guide)を参照してください。

> [参考]
> NHN Cloud NASサービスは現在(2022.09)一部リージョンでのみ提供されています。NHN Cloud NASサービスのサポートリージョンについての詳細な情報は[NASサービス概要](/Storage/NAS/ko/overview)を参照してください。


#### ワーカーノードにnfsパッケージインストール
NASストレージを使用するにはワーカーノードにnfsパッケージをインストールする必要があります。ワーカーノードに接続した後、以下のコマンドを実行してnfsパッケージをインストールします。

Ubuntuの場合、以下のコマンドでnfsパッケージをインストールできます。
```
$ apt-get install -y nfs-common
```

CentOSの場合、以下のコマンドでnfsパッケージをインストールできます。
```
$ yum install -y nfs-utils
```

#### csi-driver-nfs
csi-driver-nfsはnfsサーバーに新たなサブディレクトリを作成する方式で動作するPVの動的プロビジョニングをサポートするドライバーです。
csi-driver-nfsはストレージクラスにnfsサーバー情報を提供する方式で動作してユーザーが管理しなければならない対象を減らします。

既存の方法を利用して複数のPVを構成する場合、NFSサーバー情報を提供するためにPVごとにNFS-Provisioner podを構成する必要があります。
![nfs-csi-driver-01.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/nfs-csi-driver-01.png)

nfs-csi-driverを使用して複数のPVを構成する場合、nfs-csi-driverがNFSサーバー情報をStorageClassに登録してNFS-Provisoner podを構成する必要がありません。
![nfs-csi-driver-02.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/nfs-csi-driver-02.png)

#### csi-driver-nfsのインストール

csi-driver-nfsコンポーネントが含まれるgitプロジェクトをダウンロードします。
```
$ git clone https://github.com/kubernetes-csi/csi-driver-nfs.git
```

csi-driver-nfsフォルダに移動し、**./deploy/install-driver.sh master local**コマンドを使用してcsi-driver-nfsコンポーネントのインストールを行います。**kubectl**コマンドが正常に動作する状態でインストールが行われる必要があります。
```
$ cd csi-driver-nfs

$ ./deploy/install-driver.sh master local
use local deploy
Installing NFS CSI driver, version: master ...
serviceaccount/csi-nfs-controller-sa created
serviceaccount/csi-nfs-node-sa created
clusterrole.rbac.authorization.k8s.io/nfs-external-provisioner-role created
clusterrolebinding.rbac.authorization.k8s.io/nfs-csi-provisioner-binding created
csidriver.storage.k8s.io/nfs.csi.k8s.io created
deployment.apps/csi-nfs-controller created
daemonset.apps/csi-nfs-node created
NFS CSI driver installed successfully.
```

コンポーネントが正常にインストールされていることを確認します。
```
$ kubectl get pods -n kube-system
NAMESPACE     NAME                                         READY   STATUS    RESTARTS   AGE
kube-system   csi-nfs-controller-844d5989dc-scphc          3/3     Running   0          53s
kube-system   csi-nfs-node-hmps6                           3/3     Running   0          52s

$ kubectl get clusterrolebinding
NAME                                                                                                ROLE                                                               AGE
clusterrolebinding.rbac.authorization.k8s.io/nfs-csi-provisioner-binding                            ClusterRole/nfs-external-provisioner-role                          52s

$ kubectl get clusterrole
NAME                                                                                                         CREATED AT
clusterrole.rbac.authorization.k8s.io/nfs-external-provisioner-role                                          2022-08-09T06:21:20Z

$ kubectl get csidriver
NAME                                                ATTACHREQUIRED   PODINFOONMOUNT   MODES                  AGE
csidriver.storage.k8s.io/nfs.csi.k8s.io             false            false            Persistent,Ephemeral   47s

$ kubectl get deployment -n kube-system
NAMESPACE     NAME                        READY   UP-TO-DATE   AVAILABLE   AGE
kube-system   coredns                     2/2     2            2           22d
kube-system   csi-nfs-controller          1/1     1            1           4m32s

$ kubectl get daemonset -n kube-system
NAMESPACE     NAME                    DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR                   AGE
kube-system   csi-nfs-node            1         1         1       1            1           kubernetes.io/os=linux          4m23s
```

#### 静的プロビジョニング
NHN Cloud NASストレージを静的プロビジョニングを利用してPVとして活用するにはPVマニフェストを作成する時に**csi**情報を定義する必要があります。設定位置は.spec下のcsiです。

* driver：**nfs.csi.k8s.io**を入力します。
* readOnly：**false**を入力します。
* volumeHandle：クラスタ内で重複していない固有のidを入力します。
* volumeAttributes：NASストレージの接続情報を入力します。
  * server：NASストレージの接続情報の**ip**部分の値を入力します。
  * share：NASストレージの接続情報の**ボリューム名**部分の値を入力します。

``` yaml
# static-pv.yaml
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

PVを作成し、確認します。
```
$ kubectl apply -f static-pv.yaml
persistentvolume/pv-onas created

$ kubectl get pv -o wide
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM                      STORAGECLASS   REASON   AGE    VOLUMEMODE
pv-onas                                    300Gi      RWX            Retain           Available                                                      101s   Filesystem
```

PV作成後にPVC作成およびpodにボリュームをマウントするプロセスは基本静的プロビジョニングプロセスと同じです。静的プロビジョニングの詳細については[静的プロビジョニング](/Container/NKS/ko/user-guide/#_51)を参照してください。

#### 動的プロビジョニング
NHN Cloud NASストレージを動的プロビジョニングを利用してPVとして活用するにはStorageClassマニフェスト作成時にストレージプロバイダー情報およびNHN Cloud NASストレージ接続情報を定義する必要があります。

* provisioner：**nfs.csi.k8s.io**を入力します。
* parameters：NASストレージの接続情報を入力します。
  * server：NASストレージの接続情報の**ip**部分の値を入力します。
  * share：NASストレージの接続情報の**ボリューム名**部分の値を入力します。

``` yaml
# storageclass.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: onas-sc
provisioner: nfs.csi.k8s.io
parameters:
  server: 192.168.0.37
  share: /onas_300gb_dynamic
reclaimPolicy: Retain
volumeBindingMode: Immediate
```

StorageClassを作成し、確認します。
```
$ kubectl apply -f storageclass.yaml

$ kubectl get sc,pvc,pv
NAME                                  PROVISIONER      RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
storageclass.storage.k8s.io/onas-sc   nfs.csi.k8s.io   Retain          Immediate           false                  15s
```

StorageClass作成後にPVC作成およびpodにボリュームをマウントするプロセスは基本動的プロビジョニングプロセスと同じです。動的プロビジョニングの詳細については[動的プロビジョニング](/Container/NKS/ko/user-guide/#_52)を参照してください。

> [参考]
> nfs-csi-driverは動的プロビジョニングを利用してPVを作成する時、nfsストレージ内部にsubdirectoryを作成する方式で動作します。
> podにPVをマウントするプロセスでsubdirectoryのみマウントされるのではなく、nfsストレージ全体がマウントされるため、アプリケーションがプロビジョニングされたサイズだけボリュームを使用するように強制できません。
> podにPVをマウントするプロセスでsubdirectoryのみマウントされるのではなく、nfsストレージ全体がマウントされるため、アプリケーションがプロビジョニングされたサイズだけボリュームを使用するように強制できません。
