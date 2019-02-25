# NFS Server Provisioner

[NFS Server Provisioner](https://github.com/kubernetes-incubator/external-storage/tree/master/nfs) は、Kubernetes用のツリー外動的プロビジョニングプログラムです。
あなたはそれを使用して、ほとんどどこでも動作する共有ストレージを素早く簡単に展開することができます。

このチャートはKubernetes[外部ストレージプロジェクト](https://github.com/kubernetes-incubator/external-storage)の `nfs` プロビジョニングツールをデプロイします。
このプロビジョニングツールには `NFS server` が組み込まれています。
既存の `NFS server` に接続するためのものではありません。
既存の `NFS server` がある場合は、代わりに [NFS Client Provisioner](https://github.com/kubernetes-incubator/external-storage/tree/master/nfs-client) を使用することを検討してください。
