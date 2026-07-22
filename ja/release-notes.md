<!-- pre-align:aligned sig=8f6519afc382 -->

<a id="container-nhn-kubernetes-service-nks-release-notes"></a>
## Container > Kubernetes > リリースノート { #container-nhn-kubernetes-service-nks-release-notes }

<a id="aug-25-2026"></a>
### 2026. 08. 25. { #aug-25-2026 }

<a id="aug-25-2026-added-features"></a>
#### 新機能追加
* Kubernetes v1.35.5 をサポートします。

<a id="aug-25-2026-platform-version-updates"></a>
#### プラットフォームバージョンの更新
* 1.202608.0 追加
    * Kubernetes 互換バージョン: v1.31–v1.35

<a id="aug-25-2026-add-on-updates"></a>
#### アドオンの更新
* アドオンの追加
    * calico v3.28.2-nks4
    * calico v3.30.2-nks4
    * calico v3.31.4-nks2
    * cilium v1.18.0-nks2
    * cinder_csi_plugin v1.27.101-nks3
    * cinder_csi_plugin v1.27.102-nks4
    * coredns 1.8.4-nks3
    * metrics_server v0.4.4-nks3
    * nfs_csi_plugin v1.0.1-nks3
    * nfs_csi_plugin v1.0.2-nks2
    * nfs_csi_plugin v1.0.3-nks1
    * snapshot_controller v4.1.1-nks3

<a id="may-27-2026"></a>
### 2026. 05. 27. { #may-27-2026 }

<a id="may-27-2026-added-features"></a>
#### 新機能追加
* Kubernetes v1.34.3をサポートします。
    * k8s v1.34以降のバージョンでは、ワーカーノードイメージのCGroupバージョンによる制約事項があります。詳細については、[KubernetesバージョンとCGroupバージョンによる制約事項](/Container/NKS/ja/version-guide/#constraints-on-cgroup)を参照してください。
    * k8s v1.34以降のバージョンでは、ユーザー定義のcontainerdレジストリ設定機能が動作しません。詳細については、[ユーザー定義のcontainerdレジストリ設定機能](/Container/NKS/ja/version-guide/#containerd-registry-config)を参照してください。
* Cilium CNIをサポートします。

<a id="may-27-2026-platform-version-updates"></a>
#### プラットフォームバージョンアップデート
* 1.202605.0 追加
    * Kubernetes 互換バージョン: v1.30–v1.34
    * 機能追加
        * ワーカーノード CGroup v1 → v2 マイグレーションのサポート
        * kube-apiserver と Pod 間の通信のための konnectivity のサポート
        * k8s v1.34 クラスターの `ImageVolume` feature gate の有効化

<a id="may-27-2026-add-on-updates"></a>
#### アドオンの更新
* アドオンの追加
    * nfs_csi_plugin v1.0.2-nks1
    * cilium v1.18.0-nks1
    * calico v3.31.4-nks1
    * calico v3.28.2-nks3
    * calico v3.30.2-nks3

<a id="march-17-2026"></a>
### 2026. 03. 17. { #march-17-2026 }

<!-- TODO: translate body -->

<a id="march-17-2026-platform-version-updates"></a>
#### プラットフォームバージョンのアップデート
* 1.202602.1 追加
    * 機能改善
        * Service オブジェクトで作成された LB の安定性を強化

<a id="march-10-2026"></a>
### 2026. 03. 10. { #march-10-2026 }

<a id="march-10-2026-platform-version-updates"></a>
#### プラットフォームバージョンアップデート
* 1.202602.0 追加
    * Kubernetes 互換バージョン: v1.29–v1.33
    * 機能追加
        * Kubernetes テイント設定機能
        * Kubernetes コンポーネント設定機能 max-pods 設定のサポート
        * Secure Key Manager を使用した etcd データ暗号化のサポート
        * CGroup v2 OS イメージのサポート
    * 機能改善
        * ノードおよびノードグループ削除時のロードバランサートラフィック損失の改善

<a id="march-10-2026-add-on-updates"></a>
#### アドオン更新
* アドオン追加
    * calico v3.30.2-nks2
    * coredns 1.8.4-nks2
    * metrics_server v0.4.4-nks2
    * cinder_csi_plugin v1.27.102-nks3
    * snapshot_controller v4.1.1-nks2
    * nfs_csi_plugin v1.0.1-nks2

<a id="march-10-2026-added-features"></a>
#### 新規機能追加
* CGroup が v2 に設定された OS イメージをサポートします。
    * 2026年3月以降に配布される OS イメージは、CGroup が v2 に設定されています。
* Kubernetes コンポーネント設定機能で、kubelet の --max-pods を設定できます。
* Kubernetes テイント設定機能が追加されました。
* Secure Key Manager サービスを使用して、クラスターの機密データを暗号化/復号化できます。

<a id="december-23-2025"></a>
### 2025. 12. 23. { #december-23-2025 }

<a id="december-23-2025-platform-version-updates"></a>
#### プラットフォームバージョンの更新
* 1.202511.1 追加
    * ヘルスチェック確認ポートの設定エラーを修正しました。

<a id="december-23-2025-add-on-updates"></a>
#### アドオンのアップデート
* Cinder CSI Plugin v1.27.101-nks2、v1.27.102-nks2が追加されました。
    * 内部コンテナのバージョンが次のように変更されました。
        * csi-attacher: v3.0.2 → v3.3.0
        * csi-provisioner: v2.0.4 → v2.2.2
        * csi-snapshotter: v3.0.2 → v3.0.3
        * csi-resizer: v1.0.1 → v1.3.0
        * csi-node-driver-registrar: v2.0.1 → v2.3.0
        
<a id="november-25-2025"></a>
### 2025. 11. 25. { #november-25-2025 }

<a id="november-25-2025-changed-service-support-policy"></a>
#### サービスサポートポリシーの変更
* NKSのKubernetesバージョンサポートポリシーが変更されます。
    * 詳細は[バージョンガイド](/Container/NKS/ko/version-guide)を参照してください。

<a id="november-25-2025-updated-add-on"></a>
#### アドオンの更新
* アドオンの追加
    * Calico CNI v3.30.2-nks1
    * Cinder CSI Plugin v1.27.101-nks1, v1.27.102-nks1
    * Metrics Server v0.4.4-nks1
    * Snapshot Controller v4.1.1-nks1
    * NFS CSI Plugin v1.0.1-nks1

<a id="november-25-2025-added-features"></a>
#### 新規機能追加
* Kubernetes v1.33.4 をサポートします。
* コントロールプレーンとワーカーノードグループのプラットフォームバージョンの照会およびアップグレード機能が追加されました。
* ロードバランサーの詳細オプション設定にヘルスチェックポート設定機能が追加されました。
* ロードバランサーの詳細オプション設定にヘルスチェックホストヘッダー設定機能が追加されました。

<a id="july-15-2025"></a>
### 2025. 07. 15. { #july-15-2025 }

<a id="july-15-2025-image-update"></a>
#### イメージアップデート
* クラスター及びノードグループ作成時に以下のイメージは今後サポートされません。
    * 対象イメージ
        * Ubuntu Server 20.04.3 LTS - Container

<a id="may-27-2025"></a>
### 2025. 05. 27. { #may-27-2025 }

<a id="may-27-2025-added-features"></a>
#### 新規機能追加
* Kubernetes v1.32.3をサポートします。
* コントロールプレーンのKubernetesコンポーネントログ保存機能が追加されました。
* メトリクスベースのオートスケーラー機能が追加されました。
* ノードグループごとのFloating IP自動割り当て機能を使用できます。
* ノードグループごとのKubernetesラベル設定機能を使用できます。
* Addon管理機能を使用できます。
    * 詳細については、[使用ガイド](/Container/NKS/ja/user-guide/#addon-mgmt)を参照してください。

<a id="may-27-2025-feature-updates"></a>
#### 機能改善/変更
* クラスターCNI変更機能は今後サポートされません。
* 物理タイプのロードバランサーは今後サポートされません。
* Blue/Green Upgrade時、システムPodアップグレード段階が削除されました。
    * Calico, CoreDNSなどのシステムPodはアドオン管理機能を通じてアップデートできます。

<a id="march-4-2025"></a>
### 2025. 03. 04. { #march-4-2025 }

<a id="march-4-2025-added-features"></a>
#### 新規機能追加
* Kubernetes v1.31.4 をサポートします。
* OIDC (openID connect) 設定機能を使用できます。
* クラスターキーペアを変更できます。
    * クラスターキーペアを設定したクラスターは、サービスユーザーの権限で動作します。
    * サービスユーザーの権限で動作するクラスターは、オーナーを変更・管理する必要がありません。

<a id="march-4-2025-feature-updates"></a>
#### 機能改善/変更
* クラスターオーナー変更機能のサポートを終了します。

<a id="november-26-2024"></a>
### 2024. 11. 26. { #november-26-2024 }

<a id="november-26-2024-added-features"></a>
#### 新機能追加
* Kubernetes コンポーネントの設定機能を使用できます。

<a id="november-26-2024-feature-updates"></a>
#### 機能改善
* Podサブネットサイズに応じて、ノードあたりの作成可能な最大Pod数が自動的に設定されるように変更されました。

<a id="november-26-2024-image-update"></a>
#### イメージアップデート
イメージにインストールされたGPUドライバーのバージョンが変更されました。

| イメージ | 既存 | 新規 |
| --- | --- | --- |
| Rocky Linux 8.10 (2024.11.19)  | 535.183.06 | 535.216.01 |
| Ubuntu Server 20.04.6 LTS (2024.11.19)  | 535.183.06 | 535.216.01 |
| Ubuntu Server 22.04.6 LTS (2024.11.19)  | 535.183.06 | 535.216.01 |

イメージにインストールされたカーネルバージョンが変更されました。

| イメージ | 既存 | 新規 |
| --- | --- | --- |
| Rocky Linux 8.10 (2024.11.19)  | 4.18.0-553.8.1 | 4.18.0-553.22.1 |
| Ubuntu Server 20.04.6 LTS (2024.11.19)  | 5.4.0-190 | 5.4.0-198 |
| Ubuntu Server 22.04.6 LTS (2024.11.19)  | 5.15.0-117 | 5.15.0-124 |

イメージのワーカーノード作成に必要な最小ディスクサイズが変更されました。

| イメージ | 既存 | 新規 |
| --- | --- | --- |
| Rocky Linux 8.10 (2024.11.19) | 20GB | 30GB |
| Ubuntu Server 20.04.6 LTS (2024.11.19) | 20GB | 30GB |
| Ubuntu Server 22.04.6 LTS (2024.11.19) | 20GB | 30GB |

<a id="november-26-2024-deprecated-image-support"></a>
#### イメージサポートの中断
* CentOSイメージを使用して新規クラスター及びノードグループを作成できません。

<a id="october-29-2024"></a>
### 2024. 10. 29. { #october-29-2024 }

<a id="october-29-2024-feature-updates"></a>
#### 機能改善

* CNIアップデート
    * Kubernetes v1.27.3以上クラスターのCalico CNIバージョンがv3.28.0からv3.28.2に変更されました。

<a id="august-27-2024"></a>
### 2024. 08. 27. { #august-27-2024 }

<a id="august-27-2024-added-features"></a>
#### 新規機能追加
* ノードグループに追加のセキュリティグループを指定する機能が追加されました。
* ノードグループに追加のブロックストレージを指定する機能が追加されました。
* Kubernetes v1.30.3 をサポートします。
* ロードバランサーの静的ルート適用の有無を設定できます。
* NKS レジストリを有効化できます。

<a id="august-27-2024-deprecated-image-support"></a>
#### イメージサポート中断
* Debianイメージを使用して新規クラスター及びノードグループを作成できません。

<a id="july-23-2024"></a>
### 2024. 07. 23. { #july-23-2024 }

<a id="july-23-2024-added-features"></a>
#### 新機能の追加

* ロードバランサー詳細オプション設定でL7ルールと条件を適用できます。
* クラスタ作成時、Calico-VXLANとCalico-eBPF CNIを選択できます。

<a id="may-28-2024"></a>
### 2024. 05. 28. { #may-28-2024 }

<a id="may-28-2024-added-features"></a>
#### 新機能追加

* Kubernetes v1.29.3をサポートします。
* ロードバランサーのリスナープロトコルをTERMINATED_HTTPSに設定するとCertificate Managerの証明書を使用できます。
* クラスタの証明書を更新する機能が追加されました。
* クラスタ作成時に設定したサービスゲートウェイを変更する機能が追加されました。
* Resource Watcherサービスを通じて、クラスタで発生するイベントに関する通知を受け取ることができます。
    * 詳細は[Resource Watcher](/Governance%20&%20Audit/Resource%20Watcher/ko/overview)を参照してください。

<a id="march-26-2024"></a>
### 2024. 03. 26. { #march-26-2024 }

<a id="march-26-2024-feature-updates"></a>
#### 機能改善
* クラスタ作成時に入力するサービスゲートウェイの有効範囲が変更されました。
    * 変更前：クラスタのサブネットと同じサブネットに作成されたサービスゲートウェイ
    * 変更後:クラスタのVPCに含まれるサブネットに作成されたサービスゲートウェイ

<a id="february-27-2024"></a>
### 2024. 02. 27. { #february-27-2024 }

<a id="february-27-2024-added-features"></a>
#### 新機能の追加

* 強化されたセキュリティルールをクラスタに適用できます。
* クラスタAPIエンドポイントIPアクセス制御機能が追加されました。
* Kubernetes v1.28.3をサポートします。
* クラスタとノードグループ照会画面で作業履歴を確認できます。

<a id="november-28-2023"></a>
### 2023. 11. 28. { #november-28-2023 }

<a id="november-28-2023-added-features"></a>
#### 新規機能追加
* kubelet ユーザー定義引数の設定機能が追加されました。
* ロードバランサーの詳細オプション設定として、メンバーサブネット設定機能が追加されました。
* ロードバランサーの詳細オプション設定として、keep-alive タイムアウト値の設定機能が追加されました。
* 暗号化されたブロックストレージを使用して PV を作成する機能が追加されました。

<a id="november-28-2023-image-update"></a>
#### イメージアップデート
* クラスタ及びノードグループ作成時に使用可能な新規イメージが追加されました。
    * 対象イメージ
        * Ubuntu Server 22.04.3 LTS - Container (2023.11.21)
* 既存イメージがGPUワーカーノード機能をサポートするように変更されました。
    * 対象イメージ
        * Debian 11.8 Bullseye - Container (2023.11.21)
* イメージにインストールされているGPUドライバーのバージョンが変更されました。
    * 変更事項
        * nvidia-device-pluginのバージョンが470.199.02から535.104.12に変更されました。
        * cudaバージョンが11.4から12.2に変更されました。
        * nvidia-mig-managerバージョンが0.5.3から 0.5.5 に変更されました。
    * 対象イメージ
        * CentOS 7.9 - Container (2023.11.21)
        * Rocky Linux 8.8 - Container (2023.11.21)
        * Ubuntu Server 20.04.6 LTS - Container (2023.11.21)

<a id="august-29-2023"></a>
### 2023. 08. 29. { #august-29-2023 }

<a id="august-29-2023-added-features"></a>
#### 新機能追加

* Kubernetes v1.27.3をサポートします。
* クラスタ作成時にKubernetes APIエンドポイントのプロパティを設定できます。
* ワーカーノードで暗号化されたブロックストレージを使用できます。
* クラスタとノードグループリスト照会画面で、より詳細な状態情報を提供します。
* プロビジョニング時に新しいNASストレージを作成する機能が追加されました。

<a id="august-29-2023-feature-updates"></a>
#### 機能改善
* クラスタおよびノードグループ作成時に使用するイメージの配布版バージョンが変更されました。
    * 変更前
        * Rocky Linux 8.7 - Container (2023.07.25)
    * 変更後
        * Rocky Linux 8.8 - Container (2023.08.22)

<a id="august-29-2023-image-update"></a>
#### イメージアップデート 
* 変更事項
    * nvidia-device-pluginのバージョンが470.182.03から470.199.02に変更されました。
    * dcgmのバージョンが3.1.7から3.1.8に変更されました。
    * nvidia-mig-managerのバージョンが0.5.2から0.5.3に変更されました。
* 対象イメージ
    * CentOS 7.9 - Container (2023.08.22)
    * Rocky Linux 8.8 - Container (2023.08.22)
    * Ubuntu Server 20.04.6 LTS - Container (2023.08.22)


<a id="july-19-2023"></a>
### 2023. 07. 19. { #july-19-2023 }

<a id="july-19-2023-image-update"></a>
#### イメージアップデート
* ノードグループ作成時、一部のイメージでiptablesカーネルモジュールが正常に初期化されない問題を修正しました。
    * 問題イメージ：Rocky Linux 8.7 - Container (2023.05.25)
    * 解決イメージ：Rocky Linux 8.7 - Container (2023.07.25)
* GPUノードグループ作成時に一部のイメージでnvidia-container-runtimeモジュールが配布されない問題を修正しました。
    * 問題イメージ：CentOS 7.9 - Container (2023.05.25)
    * 解決イメージ：CentOS 7.9 - Container (2023.07.25)


<a id="may-30-2023"></a>
### 2023. 05. 30. { #may-30-2023 }

<a id="may-30-2023-added-features"></a>
#### 新機能の追加

* Kubernetes v1.26.3をサポートします。
* カスタムイメージをワーカーイメージとして活用する機能が追加されました。
    * 詳細な内容は[ユーザーガイド](/Container/NKS/ja/user-guide/#custom-image)を参照してください。
* クラスタサービスネットワーク、 Podネットワーク、 Podサブネットサイズの変更機能が追加されました。

<a id="may-30-2023-feature-updates"></a>
#### 機能改善

* クラスタおよびノードグループ作成時に使用するイメージの配布版バージョンが変更されました。
    * 変更前
        * Ubuntu Server 18.04.6 LTS - Container (2023.03.21)
        * Rocky Linux 8.6 - Container (2023.03.21)
    * 変更後
        * Ubuntu Server 20.04.6 LTS - Container (2023.05.25)
        * Rocky Linux 8.7 - Container (2023.05.25)


* イメージアップデート
    * 変更事項
        * nvidia-device-pluginバージョンが450.216.04から470.182.03に変更されました。
        * cudaバージョンが11.0.3から11.4に変更されました。
        * dcgmバージョンが3.0.0から3.1.7に変更されました。
        * Dockerバージョンが20.10.23から20.10.24に変更されました。
    * 対象イメージ
        * CentOS 7.9 - Container (2023.05.25)
        * Rocky Linux 8.7 - Container (2023.05.25)
        * Ubuntu Server 20.04.6 LTS - Container (2023.05.25)

<a id="march-28-2023"></a>
### 2023. 03. 28. { #march-28-2023 }

<a id="march-28-2023-added-features"></a>
#### 新機能追加

* クラスタCNI変更機能が追加されました。
    * 詳細については[使用ガイド](/Container/NKS/ja/user-guide/#_5)を参照してください。
* ノードグループのインスタンスタイプを変更できます。
* コンソールでKubernetesリソース照会機能を使用できます。

<a id="march-28-2023-feature-updates"></a>
#### 機能変更

* NKS APIアドレスドメインが変更されました。
    * 韓国(パンギョ)リージョン
        * 既存：https://kr1-api-kubernetes.infrastructure.cloud.toast.com
        * 変更：https://kr1-api-kubernetes-infrastructure.nhncloudservice.com
    * 韓国(ピョンチョン)リージョン
        * 既存：https://kr2-api-kubernetes.infrastructure.cloud.toast.com
        * 変更：https://kr2-api-kubernetes-infrastructure.nhncloudservice.com

<a id="march-28-2023-march-28-2023-feature-updates"></a>
#### 機能改善

* イメージアップデート
    * Ubuntu Server 18.04.6 LTS - Container (2023.02.21)
    * Debian 11.6 Bullseye - Container (2023.02.21)
    * Rocky Linux 8.6 - Container (2023.02.21)

<a id="january-31-2023"></a>
### 2023. 01. 31. { #january-31-2023 }

<a id="january-31-2023-added-features"></a>
#### 新機能の追加

* クラスタOWNER変更機能が追加されました。
    * 詳細については[使用ガイド](/Container/NKS/ja/user-guide/#_4)をご覧ください。
* Kubernetes v1.25.4をサポートします。
* クラスタ作成時、Kubernetes v1.21.6はサポートしなくなります」。ただし、使用中のクラスタには影響がありません。
* ロードバランサーのリスナーにプロキシプロトコル(Proxy Protocol)を設定できます。
* 物理ロードバランサーを作成できます。


<a id="december-27-2022"></a>
### 2022. 12. 27. { #december-27-2022 }

<a id="december-27-2022-added-features"></a>
#### 新機能の追加

* イメージ追加
    * Rocky Linux 8.6 - Container (2022.12)

<a id="november-29-2022"></a>
### 2022. 11. 29. { #november-29-2022 }

<a id="november-29-2022-feature-updates"></a>
#### 機能改善

* イメージアップデート
    * 変更事項
        * nvidia-device-pluginバージョンが450.156.00から450.191.01に変更されました。
        * Dockerバージョンが19.03から20.10に変更されました。
    * 対象イメージ
        * Ubuntu Server 18.04.6 LTS - Container (2022.11.22)
        * CentOS 7.9 - Container (2022.11.22)

<a id="november-29-2022-added-features"></a>
#### 新機能追加

* ノードの起動/停止機能を使用できます。
* さまざまなタイプのロードバランサーを作成できます。
* クラスタ名とノードグループ名をそれぞれ最大32文字で設定して作成できます。
* イメージ追加
    * Debian 11.5 Bullseye - Container (2022.11.22)

<a id="september-27-2022"></a>
### 2022. 09. 27. { #september-27-2022 }

<a id="september-27-2022-added-features"></a>
#### 新機能追加

* Kubernetes v1.24.3をサポートします。
* クラスタ作成時、Kubernetes v1.20.12のサポートは終了します。ただし、使用中のクラスタには影響がありません。

<a id="july-26-2022"></a>
### 2022. 07. 26. { #july-26-2022 }
* ノードグループ作成後にもユーザースクリプトを変更できます。
* ユーザースクリプト変更APIが追加されました。
* ワーカーノードグループのアップグレード時に最大ノード数と最大サービス不可ノード数を指定できます。

<a id="july-26-2022-added-features"></a>
#### 新機能追加

<!-- TODO: translate body -->

<a id="may-24-2022"></a>
### 2022. 05. 24. { #may-24-2022 }

<a id="may-24-2022-feature-updates"></a>
#### 機能改善
* 内部構造を改善し、サービスの性能と安定性が向上しました。

<a id="march-29-2022"></a>
### 2022. 03. 29. { #march-29-2022 }

<a id="march-29-2022-added-features"></a>
#### 新機能追加

* Kubernetes v1.23.3をサポートします。
* クラスタ作成時、Kubernetes v1.19.13はサポートしません。ただし、使用中のクラスタには影響がありません。
* ロードバランサーのリスナープロトコルをTERMINATED_HTTPSに設定すると、SSLバージョンをTLSv1.3に設定できます。

<a id="march-29-2022-feature-updates"></a>
#### 機能変更

* 機能名が変更されました。
    * 変更前：予約スクリプト
    * 変更後：ユーザースクリプト

<a id="january-25-2022"></a>
### 2022. 01. 25. { #january-25-2022 }

<a id="january-25-2022-feature-updates"></a>
#### 機能改善
* Kubernetesサービスの名前がNHN Kubernetes Service(NKS)に変更されました。

<a id="january-25-2022-added-features"></a>
#### 新機能追加

* 以下のKubernetesバージョンをサポートします。
    * v1.20.12
    * v1.21.6
    * v1.22.3

* 以下のKubneretesバージョンはクラスタ作成をサポートしません。ただし、使用中のクラスタには影響がありません。
    * v1.17.6
    * v1.18.19

* LoadBalancerタイプのサービスオブジェクトを作成するとき、リスナー固有の設定をサポートします。

* クラスタ作成およびノードグループ作成時にUbuntuワーカーイメージを使用できます。
    * イメージ追加
        * Ubuntu Server 18.04.6 LTS - Container (2022.01)

<a id="december-28-2021"></a>
### 2021. 12. 28. { #december-28-2021 }

<a id="december-28-2021-feature-updates"></a>
#### 機能改善

* GPUワーカーノードで使用するNVIDIAドライバーがアップデートされました。
    * 既存バージョン：450.119.04
    * 変更バージョン：450.156.00
* インスタンス作成時にPrometheus互換exporterが自動的にインストールされないように変更しました。
* イメージアップデート
    * CentOS 7.8 - Container (2021.12.21)

<a id="november-23-2021"></a>
### 2021. 11. 23. { #november-23-2021 }

<a id="november-23-2021-added-features"></a>
#### 新機能の追加
* Kubernetes サービス向けの Public API が公開されました。
    * Public API の詳細については、[API ガイド](/Container/NKS/ja/public-api)を参照してください。

<a id="october-26-2021"></a>
### 2021. 10. 26. { #october-26-2021 }

<a id="october-26-2021-added-features"></a>
#### 新機能追加

* Kubernetes v1.19.13をサポートします。
* LoadBalancerタイプのサービスオブジェクト作成時、ロードバランサーのさまざまなオプションを設定できます。 
* オートスケーラーの「増設後、削減遅延時間」設定の最小値が10分に変更されました。
* 新しいクラスタではワーカーノードグループが2個以上の場合、基本ワーカーノードグループを削除できます。

<a id="july-27-2021"></a>
### 2021. 07. 27. { #july-27-2021 }

<a id="july-27-2021-added-features"></a>
#### 新機能の追加

* ノードグループ作成時にユーザースクリプト機能を使用できます。
* ワーカーノードにコンテナログローテーションの設定が追加されました。
    * イメージアップデート
        * CentOS 7.8 - Container (2021.07.27)
    * コンテナログ管理については[問題解決ガイド](/Container/NKS/ja/troubleshooting-guide)を参照してください。

<a id="june-29-2021"></a>
### 2021. 06. 29. { #june-29-2021 }

<a id="june-29-2021-added-features"></a>
#### 新機能の追加

* Kubernetes v1.18.19をサポートします。
* クラスターバージョンをアップグレードできます。

<a id="march-23-2021"></a>
### 2021. 03. 23. { #march-23-2021 }

<a id="march-23-2021-added-features"></a>
#### 新機能追加

* ユーザークラスターで発生したイベントをNHN CloudTrailで確認できます。

<a id="march-23-2021-bug-fixes"></a>
#### バグ修正
* グラフィックが最適化されたインスタンスタイプ(g2)でノードグループを作成した時、正常に初期化されない問題が修正されました。

<a id="february-23-2021"></a>
### 2021. 02. 23. { #february-23-2021 }

<a id="february-23-2021-feature-updates"></a>
#### 機能改善
* Kubernetes承認コントローラー(admission controller)にPodSecurityPolicyプラグインが追加されました。
* クラスターおよびノードグループ作成時に使用するイメージの配布版バージョンが変更されました。
    * イメージアップデート
        * CentOS 7.8 - Container (2021.02.23)

<a id="january-26-2021"></a>
### 2021. 01. 26. { #january-26-2021 }
<a id="january-26-2021-bug-fixes"></a>
#### バグ修正
* インターネットゲートウェイが接続されていない環境でオートスケーラー機能が動作しない問題が修正されました。
    * イメージアップデート
        * CentOS 7.5 - Container (2021.01.26)

<a id="december-29-2020"></a>
### 2020. 12. 29. { #december-29-2020 }
<a id="december-29-2020-feature-updates"></a>
#### 機能改善
* Kubernetes CSR(Certificate Signing Request)機能を使用できます。

<a id="november-24-2020"></a>
### 2020. 11. 24. { #november-24-2020 }
<a id="november-24-2020-added-features"></a>
#### 新機能の追加
* オートスケーラー機能を使用できます。

<a id="november-24-2020-feature-updates"></a>
#### 機能改善
* クラスターを削除する時、残っているロードバランサーとFloating IPを削除します。

<a id="october-27-2020"></a>
### 2020. 10. 27. { #october-27-2020 }
<a id="october-27-2020-more-feature"></a>
#### 新機能の追加
* Kubernetes クラスターで GPU ベースのノードグループを使用できます。
    * イメージの更新
        * CentOS 7.5 - Container (2020.10.27)

<a id="09-22"></a>
### 2020. 09. 22. { #09-22 }
<a id="09-22-feature-updates"></a>
#### 機能改善
* 動作中のノードグループにノードを追加または削除できます。 

<a id="09-22-release-of-new-service"></a>
#### 新規サービスリリース
* 韓国(坪村)リージョンでもKubernetesサービスを使用できます。

<a id="august-25-2020"></a>
### 2020. 08. 25. { #august-25-2020 }
<a id="august-25-2020-feature-updates"></a>
#### 機能改善
* コンソールでKubernetesクラスターを作成する時、任意の領域(zone)を選択できます。

<a id="june-23-2020"></a>
### 2020. 06. 23. { #june-23-2020 }
<a id="june-23-2020-release-of-new-service"></a>
#### 新規サービスリリース
* コンソールからKubernetesクラスターを作成し、管理できます。
