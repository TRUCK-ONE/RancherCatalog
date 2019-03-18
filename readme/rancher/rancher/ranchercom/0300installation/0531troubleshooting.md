# Troubleshooting

## Helm commands show forbidden

正しいServiceAccountを指定せずにHelmがクラスタ内で開始されると、コマンドhelm initは成功しますが、他のほとんどのhelmコマンドを実行することはできません。
以下のエラーが表示されます。

```
Error: configmaps is forbidden: User "system:serviceaccount:kube-system:default" cannot list configmaps in the namespace "kube-system"
```

これを解決するには、サーバーコンポーネント（`tiller`）を削除し、正しい `ServiceAccount` を追加する必要があります。
クラスタから `tiller` を削除するには、`helm reset --force` を使用します。 
`helm version --server` を使用して削除されていないか確認してください。

```
helm reset --force
Tiller (the Helm server-side component) has been uninstalled from your Kubernetes Cluster.
helm version --server
Error: could not find tiller
```

`tiller` が削除されたことを確認したら、[Helmの初期化（tillerのインストール）](https://rancher.com/docs/rancher/v2.x/en/installation/ha/helm-init/)の手順に従って正しい `ServiceAccount` で `tiller` をインストールしてください。

