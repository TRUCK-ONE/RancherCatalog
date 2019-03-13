# High Availability (HA) Install

実稼働環境では、ユーザーベースが常にRancher Serverにアクセスできるように、高可用性構成でRancherをインストールすることをお勧めします。
Kubernetesクラスタにインストールすると、Rancherはクラスタのetcdデータベースと統合し、高可用性のためにKubernetesスケジューリングを利用します。

この手順では、RKEを使用して3ノードクラスタを設定し、Helmパッケージマネージャを使用してRancherチャートをインストールする方法について説明します。

> **重要：**  
> 最高のパフォーマンを得るために、このKubernetesクラスタをRancherの実行専用にすることをお勧めします。
> Rancherを実行するためのKubernetesクラスタをセットアップした後、ワークロードを実行するときに[作成します。 できます](https://rancher.com/docs/rancher/v2.x/en/cluster-provisioning/#cluster-creation-in-rancher)。

## Recommended Architecture(推奨アーキテクチャ)

- Rancher用DNSはレイヤ4ロードバランサ（TCP）に解決されるべきです。
- ロードバランサは、ポートTCP/80とTCP/443をKubernetesクラスタ内の3つすべてのノードに転送する必要があります。
- 入力コントローラはHTTPをHTTPSにリダイレクトし、ポートTCP/443でSSL/TLSを終了します。
- 入力コントローラは、Rancher展開のポッド上のポートTCP/80にトラフィックを転送します。

###### HA Rancherはレイヤ4ロードバランサを使用してインストールします。入力コントローラでのSSL終了を示しています
![画像](../pictures/070001rancher2ha.svg)
###### HA Rancherはレイヤ4ロードバランサ（TCP）を使用してインストールします。入力コントローラでのSSL終了を示しています。

## Required Tools(必要なツール)

このインストールには、次のCLIツールが必要です。
これらのツールがインストールされ、あなたの `$PATH` に利用可能であることを確認してください。

- [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl)  -  Kubernetesコマンドラインツール。
- [rke](https://rancher.com/docs/rke/v0.1.x/en/installation/)  -  Rancher Kubernetes Engine、Kubernetesクラスタを構築するためのcli。
- [helm](https://helm.sh/docs/using_helm/#installing-helm)  -  Kubernetes用のパッケージ管理。

> **重要：**  
> Helm v2.12.0およびcert-managerの問題のため、Helm v2.12.1以降を使用してください。

## Installation Outline(インストール概要)

- [Create Nodes and Load Balancer](https://rancher.com/docs/rancher/v2.x/en/installation/ha/create-nodes-lb/)(ノードとロードバランサを作成する)
- [RKEでKubernetesをインストールする](https://rancher.com/docs/rancher/v2.x/en/installation/ha/kubernetes-rke/)(RKEでKubernetesをインストールする)
- [Initialize Helm (tiller)](https://rancher.com/docs/rancher/v2.x/en/installation/ha/helm-init/)(初期化 Helm（ティラー）)
- [Install Rancher](https://rancher.com/docs/rancher/v2.x/en/installation/ha/helm-rancher/)(Rancherのインストール)

## Additional Install Options(追加のインストールオプション)

- [Migrating from an HA RKE Add-on Install](https://rancher.com/docs/rancher/v2.x/en/upgrades/upgrades/migrating-from-rke-add-on/)(HA RKEアドオンインストールからの移行)

## Previous Methods(以前の方法)

[RKE add-on install](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/)
(RKEアドオンインストール)

> **重要：**  
> RKEアドオンのインストールはRancher v2.0.8までしかサポートされていません。
> 
> HA RancherをインストールするにはRancher helm chartを使用してください。
> 詳しくは、[HAインストール - インストールの概要](https://rancher.com/docs/rancher/v2.x/en/installation/ha/#installation-outline)を参照してください。
> 
> 現在RKEアドオンインストール方法を使用している場合は、Helm chart の使用に移る方法の詳細について、[HA RKEアドオンインストールからの移行](https://rancher.com/docs/rancher/v2.x/en/upgrades/upgrades/migrating-from-rke-add-on/)を参照してください。

