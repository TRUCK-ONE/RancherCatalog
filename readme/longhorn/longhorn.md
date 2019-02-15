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

Longhorn App v0.1またはv0.2をv0.3にアップグレードする方法については、[このドキュメント](0200/upgrade.md)を参照してください。

## Deployment(展開)

KubernetesクラスタでLonghornの展開を作成するのは簡単です。

```
kubectl apply -f https://raw.githubusercontent.com/rancher/longhorn/master/deploy/longhorn.yaml
```

Google Kubernetes Engine（GKE）ユーザーの場合は、続行する前に[こちら](0200/gke.md)を参照してください。

あなたがyamlファイルで見ることができるように、LonghornマネージャとLonghornドライバは、Longhornシステムと呼ばれる別の名前空間のデーモンセットとして配置されるでしょう。

利用可能な2つのドライバ（CSIとFlexvolume）のうちの1つは、ユーザーの環境に基づいて自動的に選択されます。
ユーザーは必要に応じて自動選択を無効にすることもできます。 詳細は[こちら](0200/driver.md)をご覧ください。

一方のドライバを介して作成および使用されたボリュームが、もう一方のドライバを使用してKubernetesによって再認識されることはありません。
そのため、古いドライバを使用して既存のボリュームを作成している場合は、（アップグレード中などに）ドライバを切り替えないでください。

これらのポッドが次のように正しく起動されたことを確認したら、Longhornを正常にデプロイしました。

LonghornがCSIドライバを使用して展開された場合（csi-attacher/csi-provisioner/longhorn-csi-pluginが存在します）：

```
# kubectl -n longhorn-system get pod
NAME                                        READY     STATUS    RESTARTS   AGE
csi-attacher-0                              1/1       Running   0          6h
csi-provisioner-0                           1/1       Running   0          6h
engine-image-ei-57b85e25-8v65d              1/1       Running   0          7d
engine-image-ei-57b85e25-gjjs6              1/1       Running   0          7d
engine-image-ei-57b85e25-t2787              1/1       Running   0          7d
longhorn-csi-plugin-4cpk2                   2/2       Running   0          6h
longhorn-csi-plugin-ll6mq                   2/2       Running   0          6h
longhorn-csi-plugin-smlsh                   2/2       Running   0          6h
longhorn-driver-deployer-7b5bdcccc8-fbncl   1/1       Running   0          6h
longhorn-manager-7x8x8                      1/1       Running   0          6h
longhorn-manager-8kqf4                      1/1       Running   0          6h
longhorn-manager-kln4h                      1/1       Running   0          6h
longhorn-ui-f849dcd85-cgkgg                 1/1       Running   0          5d
```

またはFlexVolumeドライバ（Longhorn-flexvolume-driverが存在する）の場合：

```
# kubectl -n longhorn-system get pod
NAME                                        READY     STATUS    RESTARTS   AGE
engine-image-ei-57b85e25-8v65d              1/1       Running   0          7d
engine-image-ei-57b85e25-gjjs6              1/1       Running   0          7d
engine-image-ei-57b85e25-t2787              1/1       Running   0          7d
longhorn-driver-deployer-5469b87b9c-b9gm7   1/1       Running   0          2h
longhorn-flexvolume-driver-lth5g            1/1       Running   0          2h
longhorn-flexvolume-driver-tpqf7            1/1       Running   0          2h
longhorn-flexvolume-driver-v9mrj            1/1       Running   0          2h
longhorn-manager-7x8x8                      1/1       Running   0          9h
longhorn-manager-8kqf4                      1/1       Running   0          9h
longhorn-manager-kln4h                      1/1       Running   0          9h
longhorn-ui-f849dcd85-cgkgg                 1/1       Running   0          5d
```

### Access the UI

UI用の外部サービスIPを取得するには、`kubectl -n longhorn-system get svc` を使用します。

```
NAME                TYPE           CLUSTER-IP      EXTERNAL-IP      PORT(S)        AGE
longhorn-backend    ClusterIP      10.20.248.250   <none>           9500/TCP       58m
longhorn-frontend   LoadBalancer   10.20.245.110   100.200.200.123   80:30697/TCP   58m
```

KubernetesクラスタがLoadBalancerの作成をサポートしている場合、ユーザーはLonghornフロントエンドのEXTERNAL-IP（上記の場合は100.200.200.123）を使用してLonghorn UIにアクセスできます。
それ以外の場合、ユーザーは <node_ip>:<port>（上記の場合、portは30697）を使用してUIにアクセスできます。

Longhorn UIはLonghornマネージャAPIに接続し、システムの概要、ボリューム操作、およびスナップショット/バックアップ操作を提供します。
ユーザーがLonghorn UIをチェックアウトすることを強くお勧めします。

現在のUIは現時点では認証されていないことに注意してください。

## Use Longhorn with Kubernetes

Longhornは、Longhornドライバの1つを通して、永続的なボリュームをKubernetesに直接提供します。
どのドライバを使用していても、Kubernetes StorageClass を使用して永続ボリュームをプロビジョニングできます。

次のコマンドを使用して、longhornという名前のデフォルトのLonghorn StorageClass を作成します。

```
kubectl create -f https://raw.githubusercontent.com/rancher/longhorn/master/examples/storageclass.yaml
```

これでLonghornを使ってポッドを作成できます。

```
kubectl create -f https://raw.githubusercontent.com/rancher/longhorn/master/examples/pvc.yaml
```

YAML は2つの部分から成ります

1. Longhorn StorageClassを使用してPVCを作成します。
   
    ```
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: longhorn-volv-pvc
    spec:
      accessModes:
        - ReadWriteOnce
      storageClassName: longhorn
      resources:
        requests:
          storage: 2Gi
    ```

2. 永続的なボリュームとしてポッドで使用します。

    ```
    apiVersion: v1
    kind: Pod
    metadata:
      name: volume-test
      namespace: default
    spec:
      containers:
      - name: volume-test
        image: nginx:stable-alpine
        imagePullPolicy: IfNotPresent
        volumeMounts:
        - name: volv
          mountPath: /data
        ports:
        - containerPort: 80
      volumes:
      - name: volv
        persistentVolumeClaim:
          claimName: longhorn-volv-pvc
    ```

より多くの例は./examples/にあります。

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

バックアップ先の設定方法については、[こちら](0200/backup.md)を参照してください。

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

## Other features(その他の機能)

### Multiple disks
### iSCSI
### Base image

## Usage guide(利用ガイド)

### Restoring Stateful Set volumes
### [Google Kubernetes Engine](0200/gke.md)
### [Upgrade](0200/upgrade.md)

## Troubleshooting

トラブルシューティングガイドは[こちら]()をご覧ください。

## Uninstall Longhorn

1. Kubernetesクラスタへの損傷を防ぐために、Longhornボリューム（PersistentVolume、PersistentVolumeClaim、StorageClass、Deployment、StatefulSet、DaemonSetなど）を使用してすべてのKubernetesワークロードを削除することをお勧めします。

2. アンインストールジョブを作成してシステムからCRDsをクリーンにパージし、成功を待ちます。

    ```
    kubectl create -f https://raw.githubusercontent.com/rancher/longhorn/master/uninstall/uninstall.yaml
    kubectl -n longhorn-system get job/longhorn-uninstall -w
    ```

出力例：

    ```
    $ kubectl create -f https://raw.githubusercontent.com/rancher/longhorn/master/uninstall/uninstall.yaml
    job.batch/longhorn-uninstall created
    $ kubectl -n longhorn-system get job/longhorn-uninstall -w
    NAME                 DESIRED   SUCCESSFUL   AGE
    longhorn-uninstall   1         0            3s
    longhorn-uninstall   1         1            45s
    ^C
    ```

3. 残りの部品を取り外します。
    
    ```
    kubectl delete -f https://raw.githubusercontent.com/rancher/longhorn/master/deploy/longhorn.yaml
    ```

## License

Copyright (c) 2014-2019 [Rancher Labs, Inc.](https://rancher.com/)

Apache License、Version 2.0（以下「ライセンス」）に基づきライセンスされています。
ライセンスに準拠している場合を除き、このファイルを使用することはできません。
あなたは、ライセンスのコピーを以下から入手することができます。

http://www.apache.org/licenses/LICENSE-2.0

適用法で義務付けられている場合、または書面で合意されている場合を除き、ライセンスに基づいて頒布されるソフトウェアは、明示的または黙示的を問わず、「現状のまま」ベースで保証されます。
ライセンスに基づく許可および制限を規定する特定の言語については、ライセンスを参照してください。
