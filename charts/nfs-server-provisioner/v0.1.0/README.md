# NFS Server Provisioner

[NFS Server Provisioner](https://github.com/kubernetes-incubator/external-storage/tree/master/nfs) は、Kubernetes 用のツリー外動的プロビジョニングプログラムです。
あなたはそれを使用して、ほとんどどこでも動作する共有ストレージを素早く簡単に展開することができます。

このチャートはKubernetes [外部ストレージプロジェクト](https://github.com/kubernetes-incubator/external-storage)のnfsプロビジョニングツールをデプロイします。
このプロビジョニングツールには `NFS server` が組み込まれています。
既存の `NFS server` に接続するためのものではありません。
既存の `NFS server` がある場合は、代わりに[NFS Client Provisioner](https://github.com/kubernetes-incubator/external-storage/tree/master/nfs-client)を使用することを検討してください。

## [更新履歴](history.md)

- ver0.1.0

## TL;DR;

```
$ helm install stable/nfs-server-provisioner
```

>警告：デフォルト設定でインストールしても機能しますが、このチャートでプロビジョニングされたダイナミックボリュームに保存されたデータは永続的ではありません。

## 前書き

このチャートは、[Helm](https://helm.sh) パッケージマネージャを使用して、[Kubernetes](http://kubernetes.io) クラスタ上の [nfs-server-provisioner](https://github.com/kubernetes-incubator/external-storage/tree/master/nfs) デプロイメントをブートストラップします。


## Configuration(設定)

次の表は、`kibana chart` の設定可能なパラメータと、デフォルト値

| Parameter                      | 説明                                                                                                     | Default                                                  |
|:-------------------------------|:----------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------|
| `imagePullSecrets`             | Specify image pull secrets                                                                                      | `nil` (does not add image pull secrets to deployed pods) |
| `image.repository`             | The image repository to pull from                                                                               | `quay.io/kubernetes_incubator/nfs-provisioner`           |
| `image.tag`                    | The image tag to pull from                                                                                      | `v1.0.8`                                                 |
| `image.pullPolicy`             | Image pull policy                                                                                               | `IfNotPresent`                                           |
| `service.type`                 | service type                                                                                                    | `ClusterIP`                                              |
| `service.nfsPort`              | nfs-server-provisioner NFS サービスが公開されているTCPポート                                                  | `2049`                                                   |
| `service.mountdPort`           | nfs-server-provisioner mountd サービスが公開されているTCPポート                                                 | `20048`                                                  |
| `service.rpcbindPort`          | nfs-server-provisioner RPC サービスが公開されているTCPポート                                                    | `51413`                                                  |
| `service.nfsNodePort`          | if `service.type` is `NodePort` and this is non-empty, sets the nfs-server-provisioner node port of the NFS service    | `nil`                                                    |
| `service.mountdNodePort`       | if `service.type` is `NodePort` and this is non-empty, sets the nfs-server-provisioner node port of the mountd service | `nil`                                                    |
| `service.rpcbindNodePort`      | if `service.type` is `NodePort` and this is non-empty, sets the nfs-server-provisioner node port of the RPC service    | `nil`                                                    |
| `persistence.enabled`          | Enable config persistence using PVC                                                                             | `false`                                                  |
| `persistence.storageClass`     | PVC Storage Class for config volume                                                                             | `nil`                                                    |
| `persistence.accessMode`       | PVC Access Mode for config volume                                                                               | `ReadWriteOnce`                                          |
| `persistence.size`             | PVC Storage Request for config volume                                                                           | `1Gi`                                                    |
| `storageClass.create`          | Enable creation of a StorageClass to consume this nfs-server-provisioner instance                                      | `true`                                                   |
| `storageClass.provisionerName` | The provisioner name for the storageclass                                                                       | `cluster.local/{release-name}-{chart-name}`              |
| `storageClass.defaultClass`    | Whether to set the created StorageClass as the clusters default StorageClass                                    | `false`                                                  |
| `storageClass.name`            | The name to assign the created StorageClass                                                                     | `nfs`                                                    |
| `storageClass.parameters`      | Parameters for StorageClass                                                                                     | `mountOptions: vers=4.1`                                 |
| `storageClass.reclaimPolicy`   | ReclaimPolicy field of the class, which can be either Delete or Retain                                          | `Delete`                                                    |
| `resources`                    | Resource limits for nfs-server-provisioner pod                                                                          | `{}`                                                     |
| `nodeSelector`                 | Map of node labels for pod assignment                                                                           | `{}`                                                     |
| `tolerations`                  | List of node taints to tolerate                                                                                 | `[]`                                                     |
| `affinity`                     | Map of node/pod affinities                                                                                      | `{}`                                                     |

```console
$ helm install stable/nfs-server-provisioner --name my-release \
  --set=image.tag=v1.0.8,resources.limits.cpu=200m
```

あるいは、上記のパラメーターの値を指定するYAMLファイルチャートをインストールしながら提供することができます。
例えば、

```console
$ helm install stable/nfs-server-provisioner --name my-release -f values.yaml
```

> **Tip**: 例としてデフォルトの [values.yaml](values.yaml) を使用できます。

## Persistence(持続性)

nfs-server-provisionerイメージには、その構成データと、それが管理する **動的ボリューム/コンテナ**の `/export` パスが格納されています。

チャートはこの場所に[Persistent Volume](http://kubernetes.io/docs/user-guide/persistent-volumes/) ボリュームをマウントします。
ボリュームは動的ボリュームプロビジョニングを使用して作成できます。
ただし、クラスターのデフォルトを受け入れるのではなく、使用するストレージクラスを明示的に指定するか、レプリカごとにボリュームを事前に作成することを強くお勧めします。

このチャートが複数のレプリカ `storageClass.defaultClass = true` および  `persistence.storageClass` を使用してデプロイされている場合、2番目のレプリカが1番目のレプリカを使用してストレージをプロビジョニングすることになります。

## Recommended Persistence Configuration Examples(推奨される持続性設定の例)

以下は、別のストレージクラスがある場合の推奨構成例です。
持続性を提供するために存在します：

    persistence:
      enabled: true
      storageClass: "standard"
      size: 200Gi

    storageClass:
      defaultClass: true

多くのクラスタでは、クラウドプロバイダの統合によって「標準」のストレージが作成されます。
ボリュームを作成するクラス（例：Google Compute Engineの永続ディスク、Amazon EBSボリューム）。

---

以下は、別のストレージクラスがある場合の推奨構成例です。
永続性を提供するために存在しません：

    persistence:
      enabled: true
      storageClass: "-"
      size: 200Gi

    storageClass:
      defaultClass: true

この構成では、使用するレプリカごとに `PersistentVolume` を作成する必要があります。 
Helmチャートをインストールし、作成された`PersistentVolumeClaim` を調べると、`PersistentVolume` のバインドに必要な名前がわかります。

必要な `PersistentVolume` の例:

    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: data-nfs-server-provisioner-0
    spec:
      capacity:
        storage: 200Gi
      accessModes:
        - ReadWriteOnce
      gcePersistentDisk:
        fsType: "ext4"
        pdName: "data-nfs-server-provisioner-0"
      claimRef:
        namespace: kube-system
        name: data-nfs-server-provisioner-0

---

以下は、hostPathボリュームを持つベアメタル上で実行するための推奨構成例です：

    persistence:
      enabled: true
      storageClass: "-"
      size: 200Gi

    storageClass:
      defaultClass: true

    nodeSelector:
      kubernetes.io/hostname: {node-name}

この構成では、使用するレプリカごとに `PersistentVolume` を作成する必要があります。 
Helmチャートをインストールし、作成された`PersistentVolumeClaim` を調べると、`PersistentVolume` のバインドに必要な名前がわかります。

必要な `PersistentVolume`の例：

    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: data-nfs-server-provisioner-0
    spec:
      capacity:
        storage: 200Gi
      accessModes:
        - ReadWriteOnce
      hostPath:
        path: /srv/volumes/data-nfs-server-provisioner-0
      claimRef:
        namespace: kube-system
        name: data-nfs-server-provisioner-0

> **Warning**：`hostPath` ボリュームはKubernetesによってマシン間で移行することはできません。
この例では、`nfs-server-provisioner`ポッドを単一ノードで実行するように制限しています。
これは実稼働環境には不適切です。
