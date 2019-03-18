# Adding TLS Secrets

KubernetesはRancherのためのすべてのオブジェクトとサービスを作成します、しかしそれは我々が証明書とキーで `cattle-system` ネームスペースの `tls-rancher-ingress` 秘密を埋めるまで利用可能にならないでしょう。

サーバー証明書とそれに続く必要な中間証明書を組み合わせて、`tls.crt` というファイルにします。
証明書の鍵を `tls.key` というファイルにコピーします。

秘密を作成するには、`tls` 秘密タイプと一緒に `kubectl` を使用してください。

```
kubectl -n cattle-system create secret tls tls-rancher-ingress \
  --cert=tls.crt \
  --key=tls.key
```

## Using a Private CA Signed Certificate

プライベートCAを使用している場合、Rancherはサーバーへの接続を検証するためにRancher Agentによって使用されるCA証明書のコピーを必要とします。

CA証明書を `cacerts.pem` というファイルにコピーし、`kubectl` を使用して `cattle-system` ネームスペースに `tls-ca` 秘密を作成します。

> **重要：**  
> Rancherがそのファイル名を使用してCA証明書を設定するため、ファイルの名前が `cacerts.pem` であることを確認してください。

```
kubectl -n cattle-system create secret generic tls-ca \
  --from-file=cacerts.pem
```
