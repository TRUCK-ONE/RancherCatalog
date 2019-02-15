# Google Kubernetes Engine

1. GKEクラスタは、Longhornのopen-iscsi依存関係を満たすために、Container-Optimized OSの代わりにUbuntu OSを使用する必要があります。
2. GKEはユーザーにRBACを有効にするために手動でクラスタ管理者として自分自身を主張することを要求します。
Longhornをインストールする前に、次のコマンドを実行してください。

```
kubectl create clusterrolebinding cluster-admin-binding --clusterrole=cluster-admin --user=<name@example.com>
```

name@example.comはGCEのユーザーのアカウント名です。
大文字と小文字は区別されます。
詳細については[この文書](https://cloud.google.com/kubernetes-engine/docs/how-to/role-based-access-control)を参照してください。