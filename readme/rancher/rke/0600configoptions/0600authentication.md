# Authentication

RKEはx509認証戦略をサポートしています。
さらに、Kubernetes API ServerのPKI証明書に追加するSAN（Subject Alternative Names）のリストを定義することもできます。
一例として、これにより、単一ノードではなくロードバランサーを介してKubernetesクラスターAPIサーバーに接続することができます。

```
authentication:
    strategy: x509
    sans:
      - "10.18.160.10"
      - "my-loadbalancer-1234567890.us-west-2.elb.amazonaws.com"
```

