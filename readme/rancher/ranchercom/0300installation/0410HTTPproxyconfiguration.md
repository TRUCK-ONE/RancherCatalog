# HTTP Proxy Configuration

Rancherをプロキシの背後で操作していて、プロキシを介してサービス（カタログの取得など）にアクセスしたい場合は、プロキシに関するRancher情報を提供する必要があります。
RancherはGoで書かれているので、以下に示すように共通のプロキシ環境変数を使用します。

`NO_PROXY` に、プロキシの使用から除外する必要があるネットワークアドレス、ネットワークアドレス範囲、およびドメインが含まれていることを確認してください。

| Environment variable | Purpose |
| --- | --- |
| HTTP_PROXY | HTTP接続を開始するときに使用するプロキシアドレス |
| HTTPS_PROXY | Proxy address to use when initiating HTTPS connection(s) |
| NO_PROXY | 接続開始時にプロキシの使用から除外するネットワークアドレス、ネットワークアドレス範囲、およびドメイン |

> **Note:**  
> ネットワーク範囲（CIDR）表記を使用するには、NO_PROXYを大文字にする必要があります。

## Single Node Installation

Rancherコンテナーへの環境変数の受け渡しは、`-e KEY = VALUE` または ` --env KEY = VALUE` を使用して実行できます。
[単一ノードインストール](0400singlenodeinstall.md)での `NO_PROXY` に必要な値は、次のとおりです。

- localhost
- 127.0.0.1
- 0.0.0.0

以下の例は `http://192.168.0.1:3128` でアクセス可能なプロキシサーバーに基づいており、ネットワーク範囲 `192.168.10.0/24` およびドメイン `example.com` の下のすべてのホスト名にアクセスするときのプロキシの使用を除外しています。

```
docker run -d --restart=unless-stopped \
  -p 80:80 -p 443:443 \
  -e HTTP_PROXY="http://192.168.10.1:3128" \
  -e HTTPS_PROXY="http://192.168.10.1:3128" \
  -e NO_PROXY="localhost,127.0.0.1,0.0.0.0,192.168.10.0/24,example.com" \
  rancher/rancher:latest
```