# Longhorn

参考URL： [github.com](https://github.com/rancher/longhorn)
／[rancher.com](https://rancher.com/microservices-block-storage/)

LonghornはKubernetes用の分散ブロックストレージシステムです。
Longhornは軽量で信頼性があり、そして使いやすいです。
1つの簡単なコマンドで、Longhornを既存のKubernetesクラスタにデプロイできます。
Longhornが展開されると、Kubernetesクラスタに永続的なボリュームサポートが追加されます。

Longhornは、コンテナとマイクロサービスを使用して分散ブロックストレージを実装しています。
Longhornは、ブロックデバイスボリュームごとに専用のストレージコントローラを作成し、複数のノードに格納されている複数のレプリカにまたがってそのボリュームを同期的に複製します。
ストレージコントローラとレプリカ自体は、Kubernetesを使って編成されています。
Longhornはスナップショット、バックアップをサポートしています。
また、スナップショットとバックアップを定期的にスケジュールすることもできます。

あなたはLonghornとそのデザインの詳細を[ここ](rancherlonghorn.md)で読むことができます。

---

## Current status(現在の状況)

Longhornは進行中の作業です。 現時点ではアルファ品質のソフトウェアです。 私達はそれに取り組み続けているので私達はあなたのコメントに感謝します。

Longhornの最新リリースはv0.3.3で、デフォルトのエンジンイメージとしてLonghorn Engine v0.3.3に同梱されています。

## Source code

Longhornは100％オープンソースソフトウェアです。 プロジェクトのソースコードは多数のリポジトリに分散しています。

1. Longhorn engine -- Core controller/replica logic https://github.com/rancher/longhorn-engine
2. Longhorn manager -- Longhorn orchestration, includes Flexvolume driver for Kubernetes https://github.com/rancher/longhorn-manager
3. Longhorn UI -- Dashboard https://github.com/rancher/longhorn-ui

---
## Requirements(必要条件)

### Minimal Requirements(最小要件)

1. Docker v1.13+
2. Kubernetes v1.8 - v1.12
    - Kubernetes v1.13のサポートは進行中の作業です。
3. Kubernetesクラスタのすべてのノードにopen-iscsiがインストールされていることを確認します。
GKEに関しては、ゲストOSイメージとしてUbuntuを推奨しています。
なぜならそれはすでにopen-iscsiを含んでいるからです。
    - Debian/Ubuntu の場合、`apt-get install open-iscsi` を使用してインストールします。
    - RHEL/CentOSの場合は、`yum install iscsi-initiator-utils` を使用してインストールします。

### Kubernetes driver Requirements(Kubernetesドライバーの要件)

LonghornをKubernetesで使用すると、Longhorn Container Storage Interface（CSI）ドライバまたは Longhorn Flexvolumeドライバを介して永続的なストレージを提供できます。
Longhornは、Kubernetesクラスタ構成に応じて、いずれかのドライバを自動的にデプロイします。
ユーザーはデプロイメントyamlファイルでドライバを指定することもできます。CSIが好ましい。

#### Environment check script(環境チェックスクリプト)

セットアップを正しく構成するのに十分な情報をユーザーが入手できるようにするためのスクリプトを作成しました。

インストールする前に、次のコマンドを実行してください。

```
curl -sSfL https://raw.githubusercontent.com/rancher/longhorn/master/scripts/environment_check.sh | bash

```

結果の例：

```
daemonset.apps/longhorn-environment-check created
waiting for pods to become ready (0/3)
all pods ready (3/3)

  MountPropagation is enabled!

cleaning up...
daemonset.apps "longhorn-environment-check" deleted
clean up complete
```

MountPropagation機能ゲートのステータスを書き留めてください。

#### Requirement for the CSI driver(CSIドライバーの要件)

1. Kubernetes v1.10+
    - CSIはこのバージョンのKubernetes用のベータ版で、デフォルトで有効になっています。
2. マウント伝搬機能ゲートが有効
    - Kubernetes v1.10ではデフォルトで有効になっています。
    しかし、RKEの初期のバージョンの中にはそれを有効にしないものもあります。
3. 上記の条件が満たされない場合、LonghornはFlexvolumeドライバにフォールバックします。

#### Check if your setup satisfied CSI requirement(セットアップがCSIの要件を満たしているかどうかを確認してください)

1. 次のコマンドを使用して、Kubernetesサーバのバージョンを確認してください。

```
kubectl version
```

結果：

```
Client Version: version.Info{Major:"1", Minor:"10", GitVersion:"v1.10.3", GitCommit:"2bba0127d85d5a46ab4b778548be28623b32d0b0", GitTreeState:"clean", BuildDate:"2018-05-21T09:17:39Z", GoVersion:"go1.9.3", Compiler:"gc", Platform:"linux/amd64"}
Server Version: version.Info{Major:"1", Minor:"10", GitVersion:"v1.10.1", GitCommit:"d4ab47518836c750f9949b9e0d387f20fb92260b", GitTreeState
```

2. サーバーバージョンはv1.10以上である必要があります。

#### Requirement for the Flexvolume driver(Flexvolumeドライバーの要件)

1. Kubernetes v1.8+
2. curb、findmnt、grep、awk、blkidがKubernetesクラスタのすべてのノードにインストールされていることを確認します。

## Upgrading(アップグレード)

Longhorn App v0.1またはv0.2をv0.3にアップグレードする方法については、[このドキュメント]()を参照してください。

## Deployment(展開)
---
この間略

---

## Highlight features

### Snapshot

Longhornのスナップショットは、ホストの物理ディスク上のボリュームデータと同じ場所に格納されている、特定の時点におけるボリュームの状態を表します。
スナップショットの作成はLonghornですぐにできます。

ユーザーはUIを使用して以前に撮影したスナップショットに戻ることができます。
Longhornは分散ブロックストレージなので、以前のスナップショットに戻すときにLonghornボリュームがホストからマウント解除されていることを確認してください。
そうしないと、ノードファイルシステムが混乱し、ファイルシステムが破損する可能性があります。

#### Note about the block level snapshot(ブロックレベルのスナップショットについての注意)

Longhornはクラッシュコンシステントなブロックストレージソリューションです。

ブロックレイヤに書き込む前に、OSがコンテンツをキャッシュに保持するのは普通のことです。
ただし、すべてのレプリカが停止している場合は、コンテンツがOSレベルのキャッシュに保持されていてLonghornシステムにまだ転送されていないため、Longhornはシャットダウン前の即時の変更を含まない可能性があります。
それはあなたのデスクトップが停電のためにダウンした場合、電源を再開した後に、あなたはハードドライブにいくつかの奇妙なファイルを見つけることができるのと似ています。

データを常にブロック層に書き込むように強制するには、ユーザーはノード上で手動でsyncコマンドを実行するか、ディスクをマウント解除します。
どちらの状況でも、OSはキャッシュからブロック層にコンテンツを書き込みます。

### Backup

Longhornのバックアップは、Longhornシステムの外部にある2次ストレージ（Longhornのバックアップストア）に格納されている、特定の時点におけるボリュームの状態を表します。
バックアップの作成にはネットワークを介したデータのコピーが含まれるため、時間がかかります。

バックアップを作成するには、対応するスナップショットが必要です。
また、以前作成したスナップショットをバックアップすることもできます。

バックアップストアは、NFSサーバーまたはS3互換サーバーです。

バックアップターゲットは、Longhornのバックアップストアを表します。
バックアップターゲットは、Settings / General / BackupTargetで設定できます。

バックアップ先の設定方法については、[こちら]()を参照してください。

### Recurring snapshot and backup(定期的なスナップショットとバックアップ)

Longhornは、ボリュームの定期的なスナップショットとバックアップをサポートしています。
ユーザーがスナップショットまたはバックアップを取得するタイミング、および保持する必要があるスナップショット/バックアップの数のみを設定する必要があります。
Longhornは、その時点でユーザーのスナップショット/バックアップを自動的に作成します。
ノードに接続されています。

ユーザーは、ボリューム詳細ページで、定期的なスナップショットとバックアップの設定を見つけることができます。

### Changing replica count of the volumes(ボリュームのレプリカ数の変更)

デフォルトのレプリカ数は設定で変更できます。

また、ボリュームが接続されている場合、ユーザーはUIでボリュームのレプリカ数を変更できます。

Longhornは常に、各ボリュームに対して少なくとも一定数の健全な複製を維持しようとします。
現在の正常なレプリカ数が指定したレプリカ数より少ない場合、Longhornは新しいレプリカの再構築を開始します。
現在の正常なレプリカ数が指定されたレプリカ数より多い場合、Longhornは何もしません。
後者の状況では、ユーザーが1つ以上の正常なレプリカを削除した場合、または正常なレプリカが失敗した場合、正常なレプリカの総数が指定したレプリカ数を下回らない限り、Longhornは新しいレプリカの再構築を開始しません。

---

以下略
