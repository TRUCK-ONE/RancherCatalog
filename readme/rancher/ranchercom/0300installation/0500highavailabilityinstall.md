# High Availability (HA) Install

実稼働環境では、ユーザーベースが常にRancher Serverにアクセスできるように、高可用性構成でRancherをインストールすることをお勧めします。
Kubernetesクラスタにインストールすると、Rancherはクラスタのetcdデータベースと統合し、高可用性のためにKubernetesスケジューリングを利用します。

この手順では、RKEを使用して3ノードクラスタを設定し、Helmパッケージマネージャを使用してRancherチャートをインストールする方法について説明します。

> **重要：**  
> 最高のパフォーマンを得るために、このKubernetesクラスタをRancherの実行専用にすることをお勧めします。
> Rancherを実行するためのKubernetesクラスタをセットアップした後、ワークロードを実行するときに[作成します。 できます](https://rancher.com/docs/rancher/v2.x/en/cluster-provisioning/#cluster-creation-in-rancher)。

## Recommended Architecture(推奨アーキテクチャ)

---
---
---
---
---

以下略

