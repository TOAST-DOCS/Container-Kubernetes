## Container > Kubernetes > 使用ガイド

## クラスター
クラスターは、ユーザーのKubernetesを構成するインスタンスのグループです。

### クラスター作成
Kubernetesサービスを使用するには、まずクラスターを作成する必要があります。**Container > Kubernetes**サービスページで**クラスター作成**ボタンを押すと、クラスター作成ページが表示されます。クラスターの作成に必要な項目は次のとおりです。

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

必要な情報を入力し、**クラスター作成**ボタンを押すと、クラスターの作成が始まります。クラスターリストで状態を確認できます。作成には約10分かかります。クラスターの設定によっては、さらに時間がかかる場合もあります。


### クラスター照会
作成したクラスターは**Container > Kubernetes**サービスページで確認できます。クラスターを選択すると、下部にクラスター情報が表示されます。

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

必要な情報を入力し、**ノードグループ作成**ボタンを押すと、ノードグループの作成が始まります。ノードグループリストで状態を確認できます。ノードグループの作成には約5分かかります。ノードグループの設定によっては、さらに時間がかかる場合もあります。

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
> Kubernetesサービスに適用された`cluster-autoscaler`のバージョンは`1.19.0`です。
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
| 増設後の遅延時間 | ノード増設後、削除対象ノードでモニタリングを開始するまでの遅延時間| 1-1440 | 10 | 分 |

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
autoscaler-test-default-w-ohw5ab5wpzug-node-0   Ready    <none>   45m   v1.17.6
```

約5～10分後、以下のようにノードが増設されたことを確認できます。

```
# kubectl get nodes
NAME                                            STATUS   ROLES    AGE   VERSION
autoscaler-test-default-w-ohw5ab5wpzug-node-0   Ready    <none>   48m   v1.17.6
autoscaler-test-default-w-ohw5ab5wpzug-node-1   Ready    <none>   77s   v1.17.6
autoscaler-test-default-w-ohw5ab5wpzug-node-2   Ready    <none>   78s   v1.17.6
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
autoscaler-test-default-w-ohw5ab5wpzug-node-0   Ready    <none>   71m   v1.17.6
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
autoscaler-test-default-w-ohw5ab5wpzug-node-0   Ready      <none>   22d   v1.17.6
autoscaler-test-default-w-ohw5ab5wpzug-node-8   Ready      <none>   90s   v1.17.6
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
autoscaler-test-default-w-ohw5ab5wpzug-node-0   Ready    <none>   22d   v1.17.6
```


### 予約スクリプト
クラスタを作成する時と追加ノードグループを作成する時、予約スクリプトを登録できます。予約スクリプト機能には次のような特徴があります。

* 機能設定
    * この機能はワーカーノードグループごとに設定できます。
    * クラスタ作成時に入力した予約スクリプトは基本ワーカーノードグループに適用されます。
    * 追加ノードグループの作成時に入力した予約スクリプトは該当ワーカーノードグループに適用されます。
    * ワーカーノードグループが作成された後は予約スクリプトの内容を変更できません。
* スクリプト実行タイミング
    * 予約スクリプトはワーカーノード初期化プロセスのうち、インスタンス初期化プロセスで実行されます。
    * 予約スクリプトが実行された後、そのインスタンスを「ワーカーノードグループ」のワーカーノードに設定して登録します。
* スクリプト内容
    * bashスクリプトを使用する必要があります。
    * スクリプト内容にshabang(`#!/bin/bash`)は必要ありません。
    * スクリプトの最大サイズは64KBです。
    * スクリプトはroot権限で実行されます。
    * スクリプトの実行記録は以下の位置に保存されます。
        * スクリプト終了コード：`/var/log/userscript.exitcode`
        * スクリプト標準出力および標準エラーストリーム：`/var/log/userscript.output`


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
kubectlでKubernetesクラスターにアクセスするには、クラスター設定ファイル(kubeconfig)が必要です。NHN Cloud Webコンソールで**Container > Kubernetes**サービスページを開き、アクセスするクラスターを選択します。下部、**基本情報**タブで**設定ファイル**項目の**ダウンロード**ボタンを押して設定ファイルをダウンロードします。ダウンロードした設定ファイルは、任意の位置へ移動させ、kubectl実行時に参照できるように準備します。

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

クラスター作成時点によって承認コントローラーに適用されるプラグインの種類が異なります。詳細な内容はリージョン別作成時点に応じたプラグインリストを参照してください。

#### パンギョリージョン2021年2月22日以前に作成したクラスターおよび坪村リージョン2021年2月17日以前に作成したクラスター

* DefaultStorageClass
* DefaultTolerationSeconds
* LimitRanger
* MutatingAdmissionWebhook
* NamespaceLifecycle
* NodeRestriction
* PersistentVolumeClaimResize
* Priority
* ResourceQuota
* RuntimeClass
* ServiceAccount
* StorageObjectInUseProtection
* TaintNodesByCondition
* ValidatingAdmissionWebhook

#### パンギョリージョン2021年2月23日以降に作成したクラスターおよび坪村リージョン2021年2月18日以降に作成したクラスター

* DefaultStorageClass
* DefaultTolerationSeconds
* LimitRanger
* MutatingAdmissionWebhook
* NamespaceLifecycle
* NodeRestriction
* PersistentVolumeClaimResize
* PodSecurityPolicy(新規追加)
* Priority
* ResourceQuota
* RuntimeClass
* ServiceAccount
* StorageObjectInUseProtection
* TaintNodesByCondition
* ValidatingAdmissionWebhook

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

```
$ kubectl create secret docker-registry registry-credential --docker-server={ユーザーレジストリアドレス} --docker-username={NHN Cloudアカウントemailアドレス} --docker-password={サービスAppkeyまたは統合Appkey}
secret/registry-credential created

$ kubectl get secrets
NAME                  TYPE                             DATA   AGE
registry-credential   kubernetes.io/dockerconfigjson   1      30m
```

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
      - name: regcred

```

> [参考]
> NHN Cloud Container Registryの使用方法は[Container Registry使用ガイド](/Container/Container%20Registry/ja/user-guide)文書を参照してください。

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
ロードバランサーに設定されたFloating IPにHTTPリクエストを送ってKubernetesクラスターのWebサーバーPodが応答するかを確認します。サービスオブジェクトのTCP/8080ポートをPodのTCP/80ポートと接続するように設定したため、TCP/8080ポートにリクエストを送る必要があります。外部ロードバランサーとサービスオブジェクト、Podが正常に接続されていれば、Webサーバーはnginx基本ページをレスポンスします。

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


## イングレスコントローラー
イングレスコントローラー(ingress controller)は、イングレス(Ingress)オブジェクトに定義されたルールを参照してクラスター外部から内部サービスへHTTPとHTTPSリクエストをルーティングしてSSL/TSL終了、仮想ホスティングなどを提供します。イングレスコントローラーとイングレスの詳細な内容は[イングレスコントローラー](https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/), [イングレス](https://kubernetes.io/docs/concepts/services-networking/ingress/)文書を参照してください。


### NGINX Ingress Controllerのインストール
NGINX Ingress Controllerは、数多く使用されているイングレスコントローラーの中の1つです。詳細は[NGINX Ingress Controller](https://kubernetes.github.io/ingress-nginx/)と[NGINX Ingress Controller for Kubernetes](https://www.nginx.com/products/nginx-ingress-controller/)文書を参照してください。

NGINX Ingress Controllerは、必要なリソースをすぐに作成できるように、あらかじめ定義したマニフェストファイルを提供します。このマニフェストを利用すると、簡単に必要なリソースを作成できます。

```
$ kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/nginx-0.30.0/deploy/static/mandatory.yaml
namespace/ingress-nginx created
configmap/nginx-configuration created
configmap/tcp-services created
configmap/udp-services created
serviceaccount/nginx-ingress-serviceaccount created
clusterrole.rbac.authorization.k8s.io/nginx-ingress-clusterrole created
role.rbac.authorization.k8s.io/nginx-ingress-role created
rolebinding.rbac.authorization.k8s.io/nginx-ingress-role-nisa-binding created
clusterrolebinding.rbac.authorization.k8s.io/nginx-ingress-clusterrole-nisa-binding created
deployment.apps/nginx-ingress-controller created
limitrange/ingress-nginx created
```

### LoadBalancerサービス作成
イングレスコントローラーは、Podに作成されるため、外部に公開するにはLoadBalancerサービスまたはNodePortサービスを作成する必要があります。次のようにHTTPとHTTPSを処理できるLoadBalancerサービスマニフェストを定義します。

```yaml
# ingress-nginx-lb.yaml
apiVersion: v1
kind: Service
metadata:
  name: ingress-nginx
  namespace: ingress-nginx
  labels:
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/part-of: ingress-nginx
spec:
  type: LoadBalancer
  selector:
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/part-of: ingress-nginx
  ports:
    - name: http
      port: 80
      targetPort: 80
      protocol: TCP
    - name: https
      port: 443
      targetPort: 443
      protocol: TCP
  selector:
    app.kubernetes.io/name: ingress-nginx
    app.kubernetes.io/part-of: ingress-nginx
```

サービスオブジェクトを作成し、外部ロードバランサーが接続されているかを確認します。**EXTERNAL-IP**フィールドにはFloating IPアドレスが設定されている必要があります。

```
$ kubectl apply -f ingress-nginx-lb.yaml
service/ingress-nginx created

$ kubectl get svc -n ingress-nginx
NAME            TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                      AGE
ingress-nginx   LoadBalancer   10.254.2.128   123.123.123.41   80:30820/TCP,443:30269/TCP   39s
```

### URIを元にサービス分岐
イングレスコントローラーは、URIを元にサービスを分岐できます。下記の図はURIを元にサービスを分岐する簡単な例です。

![ingress-01.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/ingress-01.png)

#### サービスとPodの作成
次のようにサービスとPodを作成するためのマニフェストを作成します。`tea-svc`サービスには`tea`Podを接続し、`coffee-svc`サービスには`coffee`Podを接続します。

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

マニフェストを適用し、デプロイメント、サービス、Podが作成されているかを確認します。Podは**Running**状態になっている必要があります。

```
$ kubectl apply -f cafe.yaml
deployment.apps/coffee created
service/coffee-svc created
deployment.apps/tea created
service/tea-svc created

$ kubectl get deploy,svc,pods
NAME                           READY   UP-TO-DATE   AVAILABLE   AGE
deployment.extensions/coffee   3/3     3            3           18s
deployment.extensions/tea      2/2     2            2           18s

NAME                 TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
service/coffee-svc   ClusterIP   10.254.51.117    <none>        80/TCP    18s
service/tea-svc      ClusterIP   10.254.210.170   <none>        80/TCP    18s

NAME                          READY   STATUS    RESTARTS   AGE
pod/coffee-67c6f7c5fd-98vh5   1/1     Running   0          18s
pod/coffee-67c6f7c5fd-c58l2   1/1     Running   0          18s
pod/coffee-67c6f7c5fd-dmxf6   1/1     Running   0          18s
pod/tea-7df475c6-gtlx5        1/1     Running   0          18s
pod/tea-7df475c6-lxqsx        1/1     Running   0          18s
```

#### イングレス(Ingress)作成
リクエストパスに応じてサービスを接続するイングレスマニフェストを作成します。エンドポイントが`/tea`のリクエストは`tea-svc`サービスに接続し、`/coffee`のリクエストは`coffee-svc`サービスに接続します。

```yaml
# cafe-ingress-uri.yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: cafe-ingress-uri
spec:
  rules:
  - http:
      paths:
      - path: /tea
        backend:
          serviceName: tea-svc
          servicePort: 80
      - path: /coffee
        backend:
          serviceName: coffee-svc
          servicePort: 80
```

イングレスを作成した後、**ADDRESS**フィールドにIPが設定されている必要があります。

```
$ kubectl apply -f cafe-ingress-uri.yaml
ingress.extensions/cafe-ingress-uri created

$ kubectl get ingress cafe-ingress-uri
NAME               HOSTS   ADDRESS          PORTS   AGE
cafe-ingress-uri   *       123.123.123.44   80      88s
```

#### HTTPリクエスト転送
外部ホストから、ingressの**ADDRESS**フィールドに設定されたIPアドレスにHTTPリクエストを転送し、イングレスが正しく設定されたかを確認します。

エンドポイント`/coffee`に対するリクエストは`coffee-svc`サービスに伝達され、`coffee` Podがレスポンスします。レスポンスの**Server name**項目を見ると`coffee`Podがラウンド-ロビン方式で交互にレスポンスすることを確認できます。

```
$ curl http://123.123.123.44/coffee
Server address: 10.100.3.48:8080
Server name: coffee-67c6f7c5fd-c58l2
Dat#e: 07/Apr/2020:08:24:27 +0000
URI: /coffee
Request ID: e831901e441303ad59fb02214c49d84a

$ curl http://123.123.123.44/coffee
Server address: 10.100.2.23:8080
Server name: coffee-67c6f7c5fd-98vh5
Date: 07/Apr/2020:08:24:28 +0000
URI: /coffee
Request ID: e78427e68a1cd61ec633b9328359874e
```

同様に、エンドポイント`/tea`に対するリクエストは`tea-svc`サービスに伝達され、`tea`Podがレスポンスします。

```
$ curl http://123.123.123.44/tea
Server address: 10.100.2.24:8080
Server name: tea-7df475c6-lxqsx
Date: 07/Apr/2020:08:25:03 +0000
URI: /tea
Request ID: 59303a5a5baa60802b463b1856c8ce8d
```

定義されていないURIにリクエストを送ると、イングレスコントローラーが`404 Not Found`をレスポンスします。

```
$ curl http://123.123.123.44/
<html>
<head><title>404 Not Found</title></head>
<body>
<center><h1>404 Not Found</h1></center>
<hr><center>nginx/1.17.8</center>
</body>
</html>
```

#### リソース削除
テストに使用したリソースは、作成する時に使用したマニフェストを利用して削除できます。

```
$ kubectl delete -f cafe-ingress-uri.yaml
ingress.extensions "cafe-ingress-uri" deleted

$ kubectl delete -f cafe.yaml
deployment.apps "coffee" deleted
service "coffee-svc" deleted
deployment.apps "tea" deleted
service "tea-svc" deleted
```

### ホストを元にサービス分岐
イングレスコントローラーは、ホスト名を元にサービスを分岐できます。下の図はホスト名を元にサービスを分岐する簡単な例です。

![ingress-02.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/ingress-02.png)

#### サービスとPod作成
[URIを元にサービス分岐](/Container/Kubernetes/ja/user-guide/#uri)と同じマニフェストを利用してサービスとPodを作成します。

#### イングレス作成
ホスト名に応じてサービスを接続するイングレスマニフェストを作成します。`tea.cafe.example.com`ホストに入ったリクエストは`tea-svc`サービスに接続し、`coffee.cafe.example.com`ホストに入ったリクエストは`coffee-svc`サービスに接続します。

```yaml
# cafe-ingress-host.yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: cafe-ingress-host
spec:
  rules:
  - host: tea.cafe.example.com
    http:
      paths:
      - path: /
        backend:
          serviceName: tea-svc
          servicePort: 80
  - host: coffee.cafe.example.com
    http:
      paths:
      - path: /
        backend:
          serviceName: coffee-svc
          servicePort: 80
```

イングレスを作成した後、**ADDRESS**フィールドにIPが設定されている必要があります。

```
$ kubectl apply -f cafe-ingress-host.yaml
ingress.extensions/cafe-ingress-host created

$ kubectl get ingress
NAME                HOSTS                                          ADDRESS          PORTS   AGE
cafe-ingress-host   tea.cafe.example.com,coffee.cafe.example.com   123.123.123.44   80      4m29s
```

#### HTTP Request転送
外部ホストから、イングレスのADDRESSに設定されたIPへHTTPリクエストを転送します。ただし、ホスト名を利用してサービスを分岐するようにイングレスを構成したため、ホスト名を利用してリクエストを送る必要があります。

> [参考]
> 任意のホスト名を使用してテストするにはcurlの--resolveオプションを使用します。--resolveオプションは`{ホスト名}:{ポート番号}:{IP}`形式で入力します。これは{ホスト名}に送る{ポート番号}に対するリクエストを{IP}で解釈(resolve)するという意味です。
> `/etc/host`ファイルを開き、`{IP} {ホスト名}`形式で追加することもできます。

ホスト`coffee.cafe.example.com`へリクエストを送ると、`coffee-svc`サービスに伝達され、`coffee`Podがレスポンスします。

```
$ curl --resolve coffee.cafe.example.com:80:123.123.123.44 http://coffee.cafe.example.com/
Server address: 10.100.2.25:8080
Server name: coffee-67c6f7c5fd-2bbzf
Date: 07/Apr/2020:08:45:39 +0000
URI: /
Request ID: 29fd8a244b9f0a5ff5f35d1dc35edccf
```

ホスト`tea.cafe.example.com`へリクエストを送ると、`tea-svc`サービスに伝達され、`tea`Podがレスポンスします。

```
$ curl --resolve tea.cafe.example.com:80:123.123.123.44 http://tea.cafe.example.com/
Server address: 10.100.3.52:8080
Server name: tea-7df475c6-q8mdx
Date: 07/Apr/2020:08:53:44 +0000
URI: /
Request ID: fe61c1589d3ab8ef4ca4507245251ef3
```

不明なホストへリクエストを送ると、イングレスコントローラーが`404 Not Found`をレスポンスします。

```
$ curl http://123.123.123.44
<html>
<head><title>404 Not Found</title></head>
<body>
<center><h1>404 Not Found</h1></center>
<hr><center>nginx/1.17.8</center>
</body>
</html>

$ curl --resolve test.example.com:80:123.123.123.44 http://test.example.com/
<html>
<head><title>404 Not Found</title></head>
<body>
<center><h1>404 Not Found</h1></center>
<hr><center>nginx/1.17.8</center>
</body>
</html>
```

## Kubernetesダッシュボード
NHN Cloud Kubernetesサービスは、基本Web UIダッシュボード(dashboard)を提供します。Kubernetesダッシュボードの詳細は、[Web UI (ダッシュボード)](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)文書を参照してください。

### ダッシュボードサービス公開
ユーザーKubernetesにはダッシュボードを公開するための`kubernetes-dashboard`サービスオブジェクトがあらかじめ作成されています。

```
$ kubectl get svc kubernetes-dashboard -n kube-system
NAME                   TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
kubernetes-dashboard   ClusterIP   10.254.95.176   <none>        443/TCP   2d4h

$ kubectl describe svc kubernetes-dashboard -n kube-system
Name:              kubernetes-dashboard
Namespace:         kube-system
Labels:            k8s-app=kubernetes-dashboard
Annotations:       kubectl.kubernetes.io/last-applied-configuration:
                     {"apiVersion":"v1","kind":"Service","metadata":{"annotations":{},"labels":{"k8s-app":"kubernetes-dashboard"},"name":"kubernetes-dashboard"...
Selector:          k8s-app=kubernetes-dashboard
Type:              ClusterIP
IP:                10.254.95.176
Port:              <unset>  443/TCP
TargetPort:        8443/TCP
Endpoints:         10.100.2.3:8443
Session Affinity:  None
Events:
...
```

しかし、`kubernetes-dashboard`サービスオブジェクトはClusterIPタイプのため、まだクラスターの外部に公開されていません。ダッシュボードを外部へ公開するには、サービスオブジェクトをLoadBalancerタイプに変更するか、イングレスコントローラーとイングレスオブジェクトを作成する必要があります。

#### LoadBalancerサービスオブジェクトに変更

サービスオブジェクトを`LoadBalancer`タイプに変更すると、クラスターの外部にNHN Cloud Load Balancerが作成され、ロードバランサー、サービスオブジェクトと接続されます。ロードバランサーと接続したサービスオブジェクトを照会すると、**EXTERNAL-IP**フィールドにロードバランサーのIPが表示されます。`LoadBalancer`タイプのサービスオブジェクトの詳細は[LoadBalancerサービス](/Container/Kubernetes/ja/user-guide/#loadbalancer)を参照してください。下の図は`LoadBalancer`タイプのサービスを利用してダッシュボードを外部に公開する構造を表しています。

![dashboard-01.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/dashboard-01.png)

次のように、`kubernetes-dashboard`サービスオブジェクトのタイプを`LoadBalancer`に変更します。

```
$ kubectl -n kube-system patch svc/kubernetes-dashboard -p '{"spec":{"type":"LoadBalancer"}}'
service/kubernetes-dashboard patched
```

`kubernetes-dashboard`サービスオブジェクトが`LoadBalancer`タイプに変更した後、しばらくすると**EXTERNAL-IP**フィールドでロードバランサーのIPを確認できます。

```
$ kubectl get svc -n kube-system
NAME                   TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)                  AGE
...
kubernetes-dashboard   LoadBalancer   10.254.95.176   123.123.123.81   443:30963/TCP            2d23h
```

> [参考]
> 作成されたロードバランサーは、**Network > Load Balancer**ページで確認できます。
> ロードバランサーのIPは、外部からアクセスできるFloating IPです。**Network > Floating IP**ページで確認できます。

Webブラウザで`https://{EXTERNAL-IP}`に接続すると、Kubernetesダッシュボードページがローディングされます。ログインするために必要なトークンは[ダッシュボードアクセストークン](/Container/Kubernetes/ja/user-guide/#_23)を参照してください。

> [参考]
> Kubernetesダッシュボードは、自動作成されるプライベート証明書を使用するため、Webブラウザの種類とセキュリティ設定によっては安全ではないページと表示される場合があります。

#### イングレス(Ingress)を利用したサービス公開

イングレスは、クラスター内部の複数のサービスにアクセスするためのルーティングを提供するネットワークオブジェクトです。イングレスオブジェクトの設定はイングレスコントローラーで起動します。`kubernetes-dashboard`サービスオブジェクトは、イングレスを通して公開できます。イングレスとイングレスコントローラーの詳細は[イングレスコントローラー](/Container/Kubernetes/ja/user-guide/#_16)を参照してください。下の図はイングレスを通してダッシュボードを外部に公開する構造を表しています。

![dashboard-02.png](http://static.toastoven.net/prod_infrastructure/container/kubernetes/dashboard-02.png)

[NGINX Ingress Controllerインストール](/Container/Kubernetes/ja/user-guide/#nginx-ingress-controller)を参照して`NGINX Ingress Controller`をインストールし、`LoadBalancer`タイプのサービスを作成します。そして、次のようにイングレスオブジェクトを作成するためのマニフェストを作成します。

```yaml
# kubernetes-dashboard-ingress-tls-passthrough.yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: k8s-dashboard-ingress
  namespace: kube-system
  annotations:
    ingress.kubernetes.io/ssl-passthrough: "true"
    kubernetes.io/ingress.allow-http: "false"
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/backend-protocol: HTTPS
    nginx.ingress.kubernetes.io/proxy-body-size: 100M
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.org/ssl-backend: kubernetes-dashboard
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: kubernetes-dashboard
          servicePort: 443
        path: /
  tls:
  - secretName: kubernetes-dashboard-certs
```

マニフェストを適用してイングレスを作成し、`ingress-nginx`サービスオブジェクトの**EXTERNAL-IP**フィールドを確認します。

```
$ kubectl apply -f kubernetes-dashboard-ingress-tls-passthrough.yaml
ingress.extensions/k8s-dashboard-ingress created

$ kubectl get service/ingress-nginx -n ingress-nginx
NAME            TYPE           CLUSTER-IP       EXTERNAL-IP      PORT(S)                      AGE
ingress-nginx   LoadBalancer   10.254.211.113   123.123.123.29   80:32680/TCP,443:31631/TCP   19h
```

Webブラウザで`https://{EXTERNAL-IP}`に接続すると、Kubernetesダッシュボードページがローディングされます。ログインするために必要なトークンは[ダッシュボードアクセストークン](/Container/Kubernetes/ja/user-guide/#_23)を参照してください。

### ダッシュボードアクセストークン
Kubernetesダッシュボードにログインするにはトークンが必要です。トークンは次のコマンドで取得できます。

```
# SECRET_NAME=$(kubectl -n kube-system get secrets | grep "kubernetes-dashboard-token" | cut -f1 -d ' ')

$ kubectl describe secret $SECRET_NAME -n kube-system | grep -E '^token' | cut -f2 -d':' | tr -d " "
eyJhbGc...-QmXA
```

出力されたトークンをブラウザのトークン入力ウィンドウに入力すると、クラスター管理者権限を付与されたユーザーとしてログインできます。


## パシステントボリューム
パシステントボリューム(Persistent Volume、PV)は物理保存装置(volume)を表現するKubernetesのリソースです。1つのPVは1つのNHN Cloud Block Storageと接続されます。詳細は[パシステントボリューム](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)文書を参照してください。

PVをPodに接続して使用するには、パシステントボリュームクレーム(Persistent Volume Claims、PVC)オブジェクトが必要です。PVCは容量、読み取り/書き込みモードなど、ボリュームの要求事項を定義します。

PVとPVCで、ユーザーは使用したいボリュームのプロパティを定義し、システムはユーザーの要求事項通りにボリュームリソースを割り当てる方式で、リソースの使用と管理を分離します。

### PV/PVCのライフサイクル
PVとPVCは4段階のライフサイクル(life cycle)に従います。

* プロビジョニング(provisioning)
ユーザーが直接ボリュームを確保してPVを作成(static provisioning)したり、[ストレージクラス](https://kubernetes.io/docs/concepts/storage/storage-classes/)を使用して動的に作成(dynamic provisioning)できます。

* バインディング(binding)
PVとPVCを1:1でバインディングします。動的プロビジョニングでPVを作成した場合、バインディングも自動的に実行されます。

* 使用(using)
PVをPodにマウントして使用します。

* 返却(reclaiming)
使用を終えたボリュームを回収します。回収方法は削除(Delete)、保存(Retain)、再使用(Recycle)があります。

| 方法 | 説明 |
| --- | --- |
| 削除(Delete) | PVを削除する時、接続されたボリュームを一緒に削除します。|
| 保存(Retain) | PVを削除する時、接続されたボリュームを削除しません。ボリュームはユーザーが直接削除または、再使用できます。|
| 再使用(Recycle) | PVを削除する時、接続されたボリュームを削除せず、再使用できる状態にします。この方法は使用を中断(deprecated)しています。|


### 静的プロビジョニング

静的プロビジョニング(static provisioning)は、ユーザーが直接ブロックストレージを準備する必要があります。NHN Cloud Webコンソールの**Storage > Block Storage**サービスページで**ブロックストレージ作成**ボタンを押して、PVと接続するブロックストレージを作成します。ブロックストレージガイドの[ブロックストレージ作成](/Storage/Block%20Storage/ja/console-guide/#_2)を参照してください。

PVを作成するにはブロックストレージのIDが必要です。**Storage > Block Storage**サービスページのブロックストレージリストから使用するブロックストレージを選択します。下部**情報**タブのブロックストレージ名項目でIDを確認できます。

> [注意]
> ブロックストレージと、Podを起動するノードグループインスタンスのアベイラビリティゾーンは、同じにする必要があります。アベイラビリティゾーンが異なると接続できません。

ストレージクラスマニフェストを作成します。NHN Cloud Block Storageを使用するには**provisioner**を必ず`kubernetes.io/cinder`に設定する必要があります。

```yaml
# storage_class.yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: sc-default
provisioner: kubernetes.io/cinder
```

ストレージクラスを作成し、確認します。

```
$ kubectl apply -f storage_class.yaml
storageclass.storage.k8s.io/sc-default created

$ kubectl get sc
NAME         PROVISIONER            AGE
sc-default   kubernetes.io/cinder   8s
```

ブロックストレージと、接続するPVマニフェストを作成します。**spec.storageClassName**にはストレージクラス名を入力します。NHN Cloud Block Storageを使用するには**spec.accessModes**は`ReadWriteOnce`に設定する必要があります。**spec.presistentVolumeReclaimPolicy**は`Delete`または`Retain`に設定できます。

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

PVCを作成した後、PVの状態を照会すると、**CLAIM**項目にPVC名が指定され、**STATUS**項目が`Bound`に変更されていることを確認できます。

```
$ kubectl get pv -o wide
NAME            CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                STORAGECLASS   REASON   AGE   VOLUMEMODE
pv-static-001   10Gi       RWO            Delete           Bound    default/pvc-static   sc-default              79s   Filesystem
```


### 動的プロビジョニング

動的プロビジョニング(dynamic provisioning)は、ストレージクラスに定義されたプロパティを参照して自動的にブロックストレージを作成します。ストレージクラスマニフェストの**parameters.type**にNHN Cloud Block Storageタイプを指定できます。指定しない場合は、HDDタイプに設定されます。

| タイプ | 設定値 |
| --- | --- |
| HDD | General HDD |
| SSD | General SSD |

ブロックストレージはノードグループと同じアベイラビリティゾーン(availability zone)に作成すると接続できます。ストレージクラスマニフェストの**parameters.availability**に、ブロックストレージを作成するアベイラビリティゾーンを指定できます。接続するノードグループのアベイラビリティゾーンは、ノードグループリストで確認できます。

> [注意]
> ストレージクラスマニフェストにアベイラビリティゾーンを指定しない場合、任意のアベイラビリティゾーンにブロックストレージを作成します。ブロックストレージがノードグループと異なるアベイラビリティゾーンに作成されると接続ができない場合があるため、アベイラビリティゾーンを指定する必要があります。

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

動的プロビジョニングは、PVを作成する必要がありません。したがってPVCマニフェストには**spec.volumeName**を設定しません。

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
  storageClassName: sc-ssd
```

PVCを作成すると、PVが自動的に作成されます。PVに接続されたブロックストレージも自動的に作成され、NHN Cloud Webコンソール**Storage > Block Storage**サービスページのブロックストレージリストで確認できます。

```
$ kubectl apply -f pvc-dynamic.yaml
persistentvolumeclaim/pvc-dynamic created

$ kubectl get sc,pv,pvc
NAME                                     PROVISIONER            AGE
storageclass.storage.k8s.io/sc-default   kubernetes.io/cinder   10m

NAME                                                        CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                 STORAGECLASS   REASON   AGE
persistentvolume/pvc-c63da3f9-dfcb-4cae-a9a9-67137994febc   10Gi       RWO            Delete           Bound    default/pvc-dynamic   sc-default              16s

NAME                                STATUS   VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS   AGE
persistentvolumeclaim/pvc-dynamic   Bound    pvc-c63da3f9-dfcb-4cae-a9a9-67137994febc   10Gi       RWO            sc-default     17s
```

> [注意]
> 動的プロビジョニングで作成されたブロックストレージは、Webコンソールで削除できません。また、クラスターを削除する時、自動的に削除されません。したがって、クラスターを削除する前にPVCを全て削除する必要があります。PVCを削除しないでクラスターを削除すると、課金される場合があります。動的プロビジョニングを作成したPVCのreclaimPolicyは基本的に`Delete`が設定されているため、PVCを削除すればPVとブロックストレージが削除されます。


### PodにPVCマウント

PodにPVCをマウントするには、Podマニフェストにマウント情報を定義する必要があります。`spec.volumes.persistenVolumeClaim.claimName`に使用するPVC名を入力します。そして`spec.containers.volumeMounts.mountPath`にマウントするパスを入力します。

下記の例は、静的プロビジョニングで作成したPVCをPodの`/usr/share/nginx/html`にマウントします。

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

Podを作成し、ブロックストレージがマウントされているかを確認します。

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

NHN Cloud Webコンソール**Storage > Block Storage**サービスページでも、ブロックストレージの接続情報を確認できます。
