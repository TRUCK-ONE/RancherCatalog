# External etcd

デフォルトでは、RKEはetcdサーバーを起動しますが、RKEは外部のetcdを使うこともサポートします。
RKEはTLSを有効にしたetcdセットアップへの接続のみをサポートします。

> **Note：**  
> RKEは、etcdロールを持つノードと一緒に外部etcdサーバーを持つことを受け入れません。

```
services:
    etcd:
      path: /etcdcluster
      external_urls:
        - https://etcd-example.com:2379
      ca_cert: |-
        -----BEGIN CERTIFICATE-----
        xxxxxxxxxx
        -----END CERTIFICATE-----
      cert: |-
        -----BEGIN CERTIFICATE-----
        xxxxxxxxxx
        -----END CERTIFICATE-----
      key: |-
        -----BEGIN PRIVATE KEY-----
        xxxxxxxxxx
        -----END PRIVATE KEY-----
```

## External etcd Options

### Path

`path` は、etcdクラスタがエンドポイント上にある場所の場所を定義します。

### External URLs

`external_urls` は、etcdクラスターがホストされている場所のエンドポイントです。
etcdクラスターには複数のエンドポイントがある可能性があります。

### CA Cert/Cert/KEY

etcdサービスの認証およびアクセスに使用される証明書と秘密鍵。

