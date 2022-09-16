| 追加ネットワーク | 基本ワーカーノードグループに作成する追加ネットワーク/サブネット |

> [注意]
> クラスタ作成時、サブネット帯域が以下のネットワーク帯域と重ならないように設定する必要があります。
>  - 10.100.0.0/16
>  - 10.254.0.0/16
| v1.20.12 | 不可 | 可能 |
| v1.24.3 | 可能 | 可能 |
| 追加ネットワーク | 基本ワーカーノードグループに作成する追加ネットワーク/サブネット |
    * ただし、変更された内容はユーザースクリプト変更後に作成されるノードにのみ適用されます。 
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
ストレージの提供者情報を設定します。Kubernetesバージョンに応じてサポートされているストレージプロバイダー情報は次のとおりです。
#### ボリューム拡張許可(allowVolumeExpansion)
作成されたボリュームの拡張を許可するかどうかを設定します(未入力の場合はfalseが設定されます)。

* **True**：ボリューム拡張を許可します。
* **False**：ボリューム拡張を許可しません。
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
