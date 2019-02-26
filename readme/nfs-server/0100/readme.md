# nfs-provisioner

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
---

中途略

---

## Running

Kubernetesクラスタにnfs-provisionerをデプロイするには、[デプロイ](https://github.com/kubernetes-incubator/external-storage/blob/master/nfs/docs/deployment.md)を参照してください。

一度配備されたnfs-provisionerを使うには、[使い方](https://github.com/kubernetes-incubator/external-storage/blob/master/nfs/docs/usage.md)を見てください。


## Changelog(変更履歴)

ここで外部ストレージで行われたリリースには対応するgitタグがありません（外部ストレージのgitタグはライブラリのバージョン管理のために予約されています）ので、リリースを追跡するためにこのREADME、changelog、またはQuayを確認してください。

---

以下略
