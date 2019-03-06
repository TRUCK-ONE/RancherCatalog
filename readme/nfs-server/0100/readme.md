# nfs-provisioner

[参考](https://github.com/kubernetes-incubator/external-storage/tree/master/nfs)

```
quay.io/kubernetes_incubator/nfs-provisioner
```

nfs-provisionerは、Kubernetes 1.4以降用のツリー外動的プロビジョニングプログラムです。
あなたはそれを使用して、ほとんどどこでも動作する共有ストレージを素早く簡単に展開することができます。
あるいは、提案で詳述されている要件の実装例として役立つことによって、あなたがあなた自身のツリー外動的プロビジョニングプログラムを書くのを助けることができます。
使い方のデモはこちら、自分の書き方の例はこちら。

これは、ツリー内の動的プロビジョニングプログラムと同様に機能します。
StorageClassオブジェクトは、GCEやAWSなどのツリー内のプロビジョニングプロバイダを指定するのと同じように、そのプロビジョニングプロバイダとしてnfs-provisionerのインスタンスを指定できます。
次に、nfs-provisionerのインスタンスは、StorageClassを要求するPersistentVolumeClaimを監視し、それらに対してNFSベースのPersistentVolumeを自動的に作成します。
動的プロビジョニングの仕組みの詳細については、[ドキュメント](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)またはこのブログ投稿を参照してください。


## Quickstart

nfs-provisionerインスタンスの状態とデータを保存するボリュームを選択し、そのボリュームを `deploy/kubernetes/deployment.yaml` の `/export` にマウントします。
hostPathボリュームである必要はありません。
PVCになります。
ボリュームにはサポートされているファイルシステムが必要です。
Linux上のローカルファイルシステムはサポートされていますが、NFSはサポートされていません。

```
...
  volumeMounts:
    - name: export-volume
      mountPath: /export
volumes:
  - name: export-volume
    hostPath:
      path: /tmp/nfs-provisioner
...
```

指定するStorageClassのプロビジョニングを選択し、 `deploy/kubernetes/deployment.yaml` に設定します。

```
...
args:
  - "-provisioner=example.com/nfs"
...
```

配置を作成します。

```
$ kubectl create -f deploy/kubernetes/deployment.yaml
service "nfs-provisioner" created
deployment "nfs-provisioner" created
```

ClusterRole、ClusterRoleBinding、Role、およびRoleBindingを作成します（これは、クラスタでRBAC認証を使用する場合に必要です。これは、新しいバージョンのkubernetesのデフォルトです）。

```
$ kubectl create -f deploy/kubernetes/rbac.yaml
clusterrole.rbac.authorization.k8s.io/nfs-provisioner-runner created
clusterrolebinding.rbac.authorization.k8s.io/run-nfs-provisioner created
role.rbac.authorization.k8s.io/leader-locking-nfs-provisioner created
rolebinding.rbac.authorization.k8s.io/leader-locking-nfs-provisioner created
```

プロビジョニング `example.com/nfs` を使用して、 "example-nfs"という名前のStorageClassを作成します。

```
$ kubectl create -f deploy/kubernetes/class.yaml
storageclass "example-nfs" created
```

アノテーション `volume.beta.kubernetes.io/storage-class` を使用して PersistentVolumeClaim を作成します。 "example-nfs"

```
$ kubectl create -f deploy/kubernetes/claim.yaml
persistentvolumeclaim "nfs" created
```

PersistentVolumeはPersistentVolumeClaim用にプロビジョニングされています。
これで、要求はいくつかのポッドとバッキングNFSストレージによって読み書きされることになります。

```
$ kubectl get pv
NAME                                       CAPACITY   ACCESSMODES   RECLAIMPOLICY   STATUS      CLAIM         REASON    AGE
pvc-dce84888-7a9d-11e6-b1ee-5254001e0c1b   1Mi        RWX           Delete          Bound       default/nfs             23s
```

`PersistentVolumeClaim` を削除すると、プロビジョニングは `PersistentVolume` とそのデータを削除します。

プロビジョニングの配備を削除すると、そのプロビジョニング担当者がいなくなるまで、未解決の `PersistentVolume` は使用できなくなります。

## Running

Kubernetesクラスタにnfs-provisionerをデプロイするには、[デプロイ](0100deployment.md)を参照してください。

一度配備されたnfs-provisionerを使うには、[使い方](0200usage.md)を見てください。


## Changelog(変更履歴)

ここで外部ストレージで行われたリリースには対応するgitタグがありません（外部ストレージのgitタグはライブラリのバージョン管理のために予約されています）ので、リリースを追跡するためにこのREADME、changelog、またはQuayを確認してください。

---

以下略
