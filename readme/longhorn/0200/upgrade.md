# Upgrade

ここでは、以前の全リリースからLonghorn v0.3.1にアップグレードする方法について説明します。

## From v0.3.x

### From Longhorn deployment yaml

Longhorn v0.3.0のインストール中に設定を変更しなかった場合は、公式のLonghorn Deploymentの指示に従ってアップグレードしてください。

それ以外の場合は、公式のLonghorn Deploymentの指示からyamlファイルをダウンロードし、必要に応じて変更してから、kubectl apply -fを使用してアップグレードする必要があります。

### From Longhorn App (Rancher Catalog App)

Rancher UIで、[Catalog Apps]画面に移動して[Upgrade available]ボタンをクリックします。
設定を変更しないでください。
今すぐ設定を変更しないでください。 アップグレードをクリックします。

Longhorn UIにアクセスします。 画面の左下隅のバージョンが変わるまで、ページを定期的に更新します。
画面の右下にあるWebSocketインジケータが緑色に点灯するまで待ちます。
[設定]> [エンジンイメージ]に移動して、新しいエンジンイメージが[準備完了]になるまで待ちます。

## From v0.2 and older

Longhorn v0.2およびv0.1展開のアップグレード手順はもっと複雑です。

### Backup Existing Volumes

アップグレードの前に、各ボリュームのバックアップストアへの最近のバックアップを作成することをお勧めします。
クラスタ上のバックアップストアがまだない場合は、作成します。

この例ではNFSバックアップストアを使用します。

1. 以下のコマンドを実行してバックアップストアを作成します。

```
kubectl apply -f https://raw.githubusercontent.com/rancher/longhorn/master/deploy/backupstores/nfs-backupstore.yaml
```

2. Longhorn UI Settingsページで、Backup Targetをnfs://longhorn-test-nfs-svc.default:/opt/backupstore に設定し、Saveをクリックします。

各ボリュームの詳細ページに移動して、[スナップショットの作成]をクリックします（[スナップショットの作成]の前に、ホストのコマンドラインでsyncを実行することをお勧めします）。
新しいスナップショットをクリックして[バックアップ]をクリックします。
続行する前に、新しいバックアップがボリュームのバックアップリストに表示されるのを待ちます。

### Check For Issues

どのボリュームも劣化または障害状態になっていないことを確認してください。
処理を続行する前に、劣化したボリュームが修復されるのを待って、障害のあるボリュームを削除または修復します。

### Detach Volumes

ボリュームをデタッチするには、Longhornボリュームを使用してすべてのKubernetes Podをシャットダウンします。
これを達成する最も簡単な方法は、すべてのワークロードを削除し、アップグレード後にそれらを再作成することです。
これが望ましくない場合は、一部の作業負荷が中断される可能性があります。
ポッドをシャットダウンするために各ワークロードを変更する方法について説明します。

#### Deployment

`kubectl edit deploy/<name>` を使用してデプロイメントを編集します。 .spec.replicas を 0 に設定します。

#### StatefulSet

`kubectl edit statefulset/<name>` でステートフルセットを編集します。 .spec.replicas を 0 に設定します。

#### DaemonSet

この作業負荷を一時停止する方法はありません。 
`kubectl delete ds/<name>` でデーモンセットを削除します。

#### Pod

`kubectl delete pod/<name>` でポッドを削除します。
ワークロードコントローラによって管理されていないポッドを一時停止する方法はありません。

#### CronJob

`kubectl edit cronjob/<name>` でcronジョブを編集します。
.spec.suspend を true に設定します。
現在実行中のジョブが完了するのを待つか、関連するポッドを削除してそれらを終了します。

#### Job

シングルランジョブを完了させることを検討してください。
それ以外の場合は、`kubectl delete job/<name>` を使用してジョブを削除します。

#### ReplicaSet

`kubectl edit replicaset/<name>` を使用してレプリケートセットを編集します。
.spec.replicas を 0 に設定します。

#### ReplicationController

`kubectl edit rc/<name>` を使用してレプリケーションコントローラを編集します。
.spec.replicas を 0に設定します。

Kubernetesが使用しているボリュームがデタッチを完了するのを待ちます。

その後、Longhorn UIから残りのすべてのボリュームを切り離します。
これらのボリュームは、Longhorn UIまたはREST APIを介してKubernetesの外部で作成および添付された可能性があります。

### Uninstall the Old Version of Longhorn

設定ページのBackupTargetを書き留めます。
v0.1またはv0.2からアップグレードした後は、手動でBackupTargetを設定する必要があります。

Longhornコンポーネントを削除します。

Longhorn v0.1（Rancher 2.0のLonghorn Appを使用してインストールされている可能性が高い）の場合：

```
kubectl delete -f https://raw.githubusercontent.com/llparse/longhorn/v0.1/deploy/uninstall-for-upgrade.yaml
```

Longhorn v0.2の場合：

```
kubectl delete -f https://raw.githubusercontent.com/rancher/longhorn/v0.2/deploy/uninstall-for-upgrade.yaml
```

両方のコマンドがすべてのコンポーネントに対してNot foundを返した場合、Longhornはおそらく別のネームスペースにデプロイされています。
使用されているネームスペースを判別し、それに従ってNAMESPACEをここで調整します。

```
NAMESPACE=<some_longhorn_namespace>
curl -sSfL https://raw.githubusercontent.com/rancher/longhorn/v0.1/deploy/uninstall-for-upgrade.yaml|sed "s#^\( *\)namespace: longhorn#\1namespace: ${NAMESPACE}#g" > longhorn.yaml
kubectl delete -f longhorn.yaml
```

### Backup Longhorn System

Longhorn CRD yamlをローカルディレクトリにバックアップするので、後で復元または確認できます。

#### Upgrade from v0.1

Longhornのデフォルトデプロイネームスペースを変更するため、ユーザーはv0.1のCRDをバックアップする必要があります。
バックアップを調べて、Longhornがネームスペースlonghornで実行されていることを確認します。
それ以外の場合は、以下のNAMESPACEの値を変更します。

```
NAMESPACE=longhorn
kubectl -n ${NAMESPACE} get volumes.longhorn.rancher.io -o yaml > longhorn-v0.1-backup-volumes.yaml
kubectl -n ${NAMESPACE} get engines.longhorn.rancher.io -o yaml > longhorn-v0.1-backup-engines.yaml
kubectl -n ${NAMESPACE} get replicas.longhorn.rancher.io -o yaml > longhorn-v0.1-backup-replicas.yaml
kubectl -n ${NAMESPACE} get settings.longhorn.rancher.io -o yaml > longhorn-v0.1-backup-settings.yaml
```

それが終わったら、それらのファイルをチェックし、それらが空でないことを確認してください（あなたが既存のボリュームを持っていない限り）。

#### Upgrade from v0.2

バックアップを調べて、Longhornがネームスペースlonghorn-systemで実行されていることを確認します。
それ以外の場合は、以下のNAMESPACEの値を変更します。

```
NAMESPACE=longhorn-system
kubectl -n ${NAMESPACE} get volumes.longhorn.rancher.io -o yaml > longhorn-v0.2-backup-volumes.yaml
kubectl -n ${NAMESPACE} get engines.longhorn.rancher.io -o yaml > longhorn-v0.2-backup-engines.yaml
kubectl -n ${NAMESPACE} get replicas.longhorn.rancher.io -o yaml > longhorn-v0.2-backup-replicas.yaml
kubectl -n ${NAMESPACE} get settings.longhorn.rancher.io -o yaml > longhorn-v0.2-backup-settings.yaml
```

それが終わったら、それらのファイルをチェックし、それらが空でないことを確認してください（あなたが既存のボリュームを持っていない限り）。

### Delete CRDs in Different Namespace

これは、Longhorn App v0.1を実行しているRancherユーザーにのみ必要です。
デフォルトでは長くなっているネームスペースからすべてのCRDsを削除します。

```
NAMESPACE=longhorn
kubectl -n ${NAMESPACE} get volumes.longhorn.rancher.io -o yaml | sed "s/\- longhorn.rancher.io//g" | kubectl apply -f -
kubectl -n ${NAMESPACE} get engines.longhorn.rancher.io -o yaml | sed "s/\- longhorn.rancher.io//g" | kubectl apply -f -
kubectl -n ${NAMESPACE} get replicas.longhorn.rancher.io -o yaml | sed "s/\- longhorn.rancher.io//g" | kubectl apply -f -
kubectl -n ${NAMESPACE} get settings.longhorn.rancher.io -o yaml | sed "s/\- longhorn.rancher.io//g" | kubectl apply -f -
kubectl -n ${NAMESPACE} delete volumes.longhorn.rancher.io --all
kubectl -n ${NAMESPACE} delete engines.longhorn.rancher.io --all
kubectl -n ${NAMESPACE} delete replicas.longhorn.rancher.io --all
kubectl -n ${NAMESPACE} delete settings.longhorn.rancher.io --all
```

### Install Longhorn

#### Upgrade from v0.1

Longhorn v0.1を実行しているRancherユーザーの場合は、Rancherアプリの[アップグレード]ボタンをクリックしないでください。

1. Rancher UIのCatalog Apps画面からLonghorn Appを削除します。
2. Longhornアプリテンプレートバージョン0.3.1を起動します。
3. Longhorn Systemのデータを復元します。
この手順は、Longhorn App v0.1を実行しているRancherユーザーに必要です。
新しくインストールされたLonghornシステムはlonghorn-system名前空間にインストールされるので、以下のNAMESPACE変数を変更しないでください。

```
NAMESPACE=longhorn-system
sed "s#^\( *\)namespace: .*#\1namespace: ${NAMESPACE}#g" longhorn-v0.1-backup-settings.yaml | kubectl apply -f -
sed "s#^\( *\)namespace: .*#\1namespace: ${NAMESPACE}#g" longhorn-v0.1-backup-replicas.yaml | kubectl apply -f -
sed "s#^\( *\)namespace: .*#\1namespace: ${NAMESPACE}#g" longhorn-v0.1-backup-engines.yaml | kubectl apply -f -
sed "s#^\( *\)namespace: .*#\1namespace: ${NAMESPACE}#g" longhorn-v0.1-backup-volumes.yaml | kubectl apply -f -
```

#### Upgrade from v0.2

Rancherを使用していないLonghorn v0.2ユーザーの場合は、公式のLonghorn Deploymentの指示に従ってください。

#### Access UI and Set BackupTarget

longhorn-uiとlonghorn-managerのポッドが実行されるまで待ちます。

```
kubectl -n longhorn-system get pod -w
```
UIにアクセスします。

[設定]> [一般]で、[バックアップターゲット]を以前のバージョンで使用されていたバックアップターゲットに設定します。
この例では、これは nfs://longhorn-test-nfs-svc.default:/opt/backupstore です。

## Migrating Between Flexvolume and CSI Driver

あなたのLonghornアプリが最新であることを確認してください。
先に進む前に、上記の関連するアップグレード手順に従ってください。

ドライバ間の移行パスでは、各ボリュームのバックアップと復元が必要となり、APIとワークロードの両方のダウンタイムが発生します。
これは面倒なプロセスです。
先に進む前に、スイッチングドライバがもたらすメリットを検討してください。
作業量を減らすために、古いドライバを使用して重要でない作業負荷を削除することを検討してください。

### Flexvolume to CSI

CSIは、最新のツリー外Kubernetesストレージインタフェースです。

1. 既存のボリュームをバックアップします。
2. Rancher UIで、Catalog Apps画面に移動し、Longhornアプリを見つけて、Up to dateボタンをクリックします。
Kubernetes Driverの下で、flexvolumeを選択します。
Flexvolume Pathを空のままにすることをお勧めします。
アップグレードをクリックします。
3. CSIの復元手順に従って各ボリュームを復元します。
この手順はStatefulSetワークロードに合わせて調整されていますが、プロセスはすべてのワークロードでほぼ同じです。

### CSI to Flexvolume

CSIからFlexvolumeドライバに移行したい場合は、ぜひご連絡ください。
CSIは最新のツリー外ストレージインタフェースであり、Flexvolumeのexecベースのモデルに代わるものと期待されています。

1. 既存のボリュームをバックアップします。
2. Rancher UIで、Catalog Apps画面に移動し、Longhornアプリを見つけて、Up to dateボタンをクリックします。
Kubernetes Driverの下で、flexvolumeを選択します。
Flexvolume Pathを空のままにすることをお勧めします。
アップグレードをクリックします。
3. Flexvolumeの復元手順に従って各ボリュームを復元します。
この手順はStatefulSetワークロードに合わせて調整されていますが、プロセスはすべてのワークロードでほぼ同じです。


## Upgrade Engine Images

**エンジンイメージをアップグレードする前には必ずバックアップを作成してください。**

### Offline upgrade

ライブアップグレードが利用できない場合（たとえば、v0.1 / v0.2からv0.3へ）、またはボリュームが劣化状態のままになっている場合：

1. 該当するワークロードについてデタッチ手順を実行します。
2. 一括選択してすべてのボリュームを選択します。
[一括操作]ボタン[エンジンのアップグレード]をクリックして、一覧からエンジンイメージを選択します。
これは、このリリースのマネージャに付属のデフォルトエンジンです。
3. ボリュームの切り離し手順を逆にして、すべてのワークロードを再開します。
Kubernetesワークロードの一部ではないボリュームは、Longhorn UIからアタッチする必要があります。

### Live upgrade (beta feature since v0.3.3)

ライブアップグレードは健全なボリュームでのみ行われるべきです。

1. アップグレードしたいボリュームを選択します。
2. ドロップダウンで[アップグレードエンジン]をクリックします。
3. アップグレードしたいエンジンイメージを選択します。
    - UIは現在の画像をリストから除外するため、通常はリスト内の唯一のエンジン画像です。
4. OKをクリックします。

ライブアップグレード中、ユーザーには一時的に2倍の数のレプリカが表示されます。
アップグレードが完了すると、ユーザーには以前と同じ数のレプリカが表示され、ボリュームの[Engine Image]フィールドが更新されます。

ライブアップグレード後も、RancherまたはKubernetesはエンジン用の古いバージョンのイメージとレプリカ用の新しいバージョンのイメージを表示します。
期待通りです。
[Volume Detail]ページに新しいバージョンのイメージがボリュームイメージとして表示されていれば、アップグレードは成功です。

### Clean up the old image

すべての画像のアップグレードが完了したら、Longhorn UIから[設定]  -  [エンジンイメージ]を選択します。
これで、デフォルト以外のイメージを削除できるはずです。

## Note

アップグレードは常にトリッキーです。
ボリュームの最新のバックアップを保持することは重要です。
問題が発生した場合は、バックアップを使用してボリュームを復元できます。

問題がある場合は、https://github.com/rancher/longhorn/issues に報告して、バックアップyamlファイルとマネージャログを含めてください。
