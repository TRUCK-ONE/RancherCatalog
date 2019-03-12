# Single Node Install with External Load Balancer

Rancher Serverコンテナではなくロードバランサで TLS/SSL を終了するという特別な要件がある開発およびテスト環境では、Rancherを展開し、それと連動するようにロードバランサを設定します。
このインストール手順では、単一のコンテナーを使用してRancherを展開する方法について説明し、次にレイヤー7 Nginxロードバランサーの設定例を示します。

> 外部ロードバランサーをスキップしたいですか？  
> 代わりに[シングルノードインストール](0400singlenodeinstall.md)を参照してください。

## インストール概要

1. Provision Linux Host
1. Choose an SSL Option and Install Rancher
1. Configure Load Balancer

## 1. Provision Linux Host

Rancher Serverを起動するには、[要件](0200noderequirements.md)に従って単一のLinuxホストをプロビジョニングしてください。

## 2. SSLオプションを選択してRancherをインストールする

セキュリティ上の理由から、Rancherを使用する場合はSSL（Secure Sockets Layer）が必要です。
SSLは、あなたがログインしたり、クラスタとやり取りするときのように、すべてのRancherネットワーク通信を保護します。


> あなたはしたいですか…
> - エアギャップの取り付けを完了しますか？
> - Rancher APIですべての取引を記録しますか？
> 
> 続行する前に、以下の[詳細オプション](https://rancher.com/docs/rancher/v2.x/en/installation/single-node/single-node-install-external-lb/#advanced-options)を参照してください。

以下のオプションから選択してください。

#### オプションA  - 自分の証明書を持参する：自己署名

自己署名証明書を使用して通信を暗号化することを選択した場合は、証明書をロードバランサー（後で実行します）とRancherコンテナーにインストールする必要があります。
Dockerコマンドを実行してRancherを展開し、自分の証明書に向けます。

> 前提条件：  
> 自己署名証明書を作成します。
> - 証明書ファイルは[PEM形式](https://rancher.com/docs/rancher/v2.x/en/installation/single-node/single-node-install-external-lb/#pem)でなければなりません。

**自己署名証明書を使用してRancherをインストールするには**

1. Dockerコマンドを実行してRancherをデプロイする間に、Dockerを自分のCA証明書ファイルに向けます。

```
docker run -d --restart=unless-stopped \
  -p 80:80 -p 443:443 \
  -v /etc/your_certificate_directory/cacerts.pem:/etc/rancher/ssl/cacerts.pem \
  rancher/rancher:latest
```

#### オプションB  - 自分の証明書を持参する：公認CAによる署名

クラスタが一般向けの場合は、認定されたCAによって署名された証明書を使用するのが最善です。

> 前提条件：  
> 証明書ファイルは[PEM形式](https://rancher.com/docs/rancher/v2.x/en/installation/single-node/single-node-install-external-lb/#pem)でなければなりません。

**認識されたCAによって署名された証明書を使用してRancherをインストールするには：**

認定されたCAによって署名された証明書を使用する場合は、証明書をRancherコンテナにインストールする必要はありません。
デフォルトのCA証明書が生成されて保存されていないことを確認する必要があります。
これを行うには、 `--no-cacerts` パラメータをコンテナに渡します。

1. 次のコマンドを入力してください。

```
docker run -d --restart=unless-stopped \
-p 80:80 -p 443:443 \
rancher/rancher:latest --no-cacerts
```

## 3.Configure Load Balancer(ロードバランサの設定)

Rancherコンテナーの前でロードバランサーを使用する場合、コンテナーがポート通信をポート80またはポート443からリダイレクトする必要はありません。
ヘッダーX-Forwarded-Proto：httpsヘッダーを渡すことによって、このリダイレクトは無効になります。

ロードバランサまたはプロキシは、以下をサポートするように設定する必要があります。

- WebSocket 接続
- SPDY / HTTP/2 プロトコル
- 以下のヘッダを渡す/設定する：
  | Header | Value | Description |
  | --- | --- | --- |
  | `Host` | ホスト名は以前はRancherに到達していました。 | クライアントから要求されたサーバーを識別します。 |
  | `X-Forwarded-Proto` | `https` | クライアントがロードバランサまたはプロキシへの接続に使用していたプロトコルを識別します。<br>**Note:** このヘッダが存在する場合、 `rancher/rancher` はHTTPをHTTPSにリダイレクトしません。
  | `X-Forwarded-Port` | ランチャーに到達するために使用される Port | クライアントがロードバランサまたはプロキシへの接続に使用していたプロトコルを識別します。 |
  | `X-Forwarded-For` | クライアント接続のIP | クライアントの発信元IPアドレスを識別します。 |


### Example Nginx configuration(Nginxの設定例)

このNGINX設定はNGINX 1.14でテストされています。

> Note:  
> このNginx設定はほんの一例であり、あなたの環境には適さないかもしれません。
> 完全なドキュメントは[NGINXロードバランス -  HTTPロードバランス](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/)を参照してください。

- `rancher-server` をRancherコンテナーを実行しているノードのIPアドレスまたはホスト名に置き換えます。
- 両方の `FQDN` をRancherのDNS名に置き換えます。
- `/certs/fullchain.pem` と` /certs/privkey.pem` をそれぞれサーバー証明書とサーバー証明書キーの場所に置き換えます。

```
worker_processes 4;
worker_rlimit_nofile 40000;

events {
    worker_connections 8192;
}

http {
    upstream rancher {
        server rancher-server:80;
    }

    map $http_upgrade $connection_upgrade {
        default Upgrade;
        ''      close;
    }

    server {
        listen 443 ssl http2;
        server_name FQDN;
        ssl_certificate /certs/fullchain.pem;
        ssl_certificate_key /certs/privkey.pem;

        location / {
            proxy_set_header Host $host;
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_set_header X-Forwarded-Port $server_port;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_pass http://rancher;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection $connection_upgrade;
            # This allows the ability for the execute shell window to remain open for up to 15 minutes. Without this parameter, the default is 1 minute and will automatically close.
            proxy_read_timeout 900s;
            proxy_buffering off;
        }
    }

    server {
        listen 80;
        server_name FQDN;
        return 301 https://$server_name$request_uri;
    }
}
```

## What’s Next?

- **推奨：** [単一ノードのバックアップと復元](https://rancher.com/docs/rancher/v2.x/en/backups/backups/single-node-backups/)を確認してください。
今すぐバックアップする必要のあるデータはありませんが、通常のRancherの使用後にバックアップを作成することをお勧めします。
- Kubernetesクラスタを作成します。[Kubernetesクラスタのプロビジョニング](https://rancher.com/docs/rancher/v2.x/en/cluster-provisioning/)

## FAQ and Troubleshooting

#### 自分の証明書がPEM形式かどうかはどうすればわかりますか？

次のような特徴によってPEM形式を認識できます。

- ファイルは次のヘッダで始まります。  
`-----BEGIN CERTIFICATE-----`
- ヘッダーの後には長い文字列が続きます。 本当に長い
- ファイルはフッターで終わります。  
`-----END CERTIFICATE-----`

**PEM証明書の例：**
```
----BEGIN CERTIFICATE-----
MIIGVDCCBDygAwIBAgIJAMiIrEm29kRLMA0GCSqGSIb3DQEBCwUAMHkxCzAJBgNV
... more lines
VWQqljhfacYPgp8KJUJENQ9h5hZ2nSCrI+W00Jcw4QcEdCI8HL5wmg==
-----END CERTIFICATE-----
```
***PEM証明書キーの例：***
```
-----BEGIN RSA PRIVATE KEY-----
MIIGVDCCBDygAwIBAgIJAMiIrEm29kRLMA0GCSqGSIb3DQEBCwUAMHkxCzAJBgNV
... more lines
VWQqljhfacYPgp8KJUJENQ9h5hZ2nSCrI+W00Jcw4QcEdCI8HL5wmg==
-----END RSA PRIVATE KEY-----
```

鍵が次の例のようになっている場合は、[証明書の鍵をPKCS8からPKCS1に変換する方法はありますか](https://rancher.com/docs/rancher/v2.x/en/installation/single-node/single-node-install-external-lb/#convert-pkcs8)を参照してください。

```
-----BEGIN PRIVATE KEY-----
MIIGVDCCBDygAwIBAgIJAMiIrEm29kRLMA0GCSqGSIb3DQEBCwUAMHkxCzAJBgNV
... more lines
VWQqljhfacYPgp8KJUJENQ9h5hZ2nSCrI+W00Jcw4QcEdCI8HL5wmg==
-----END PRIVATE KEY-----
```

#### 証明書の鍵をPKCS8からPKCS1に変換する方法はありますか？

PKCS8証明書キーファイルを使用している場合、Rancherは次の行をログに記録します。
```
ListenConfigController cli-config [listener] failed with : failed to read private key: asn1: structure error: tags don't match (2 vs {class:0 tag:16 length:13 isCompound:true})
```
これを機能させるには、以下のコマンドを使用してキーをPKCS8からPKCS1に変換する必要があります。
```
openssl rsa -in key.pem -out convertedkey.pem
```
Rancher用の証明書キーファイルとして `convertkey.pem` を使用できるようになりました。

#### 私が中級者を追加したい場合の証明書の順序は何ですか？

証明書を追加する順序は次のとおりです。
```
-----BEGIN CERTIFICATE-----
%YOUR_CERTIFICATE%
-----END CERTIFICATE-----
-----BEGIN CERTIFICATE-----
%YOUR_INTERMEDIATE_CERTIFICATE%
-----END CERTIFICATE-----
```

#### 証明書チェーンを検証する方法

`openssl` バイナリを使用して証明書チェーンを検証できます。
コマンドの出力（以下のコマンド例を参照）が `Verify return code: 0 (ok)` で終わっていれば、証明書チェーンは有効です。
`ca.pem` ファイルは、 `rancher/rancher` コンテナーに追加したものと同じでなければなりません。
認識された認証局によって署名された証明書を使用するときは、 `-CAfile` パラメーターを省略することができます。

Command:
```
openssl s_client -CAfile ca.pem -connect rancher.yourdomain.com:443
...
    Verify return code: 0 (ok)
```

### 高度なオプション

#### API Auditing

Rancher APIを使用してすべてのトランザクションを記録したい場合は、installコマンドに以下のフラグを追加して `API Audit Log` を有効にしてください。

```
-e AUDIT_LEVEL=1 \
-e AUDIT_LOG_PATH=/var/log/auditlog/rancher-api-audit.log \
-e AUDIT_LOG_MAXAGE=20 \
-e AUDIT_LOG_MAXBACKUP=20 \
-e AUDIT_LOG_MAXSIZE=100 \
```

#### Air Gap

このページにアクセスして[Air Gapのインストール](https://rancher.com/docs/rancher/v2.x/en/installation/air-gap-high-availability/)を完了する場合は、選択したオプションでインストールコマンドを実行するときに、プライベートレジストリURLをサーバタグの先頭に追加する必要があります。
`<REGISTRY.DOMAIN.COM：PORT>` をあなたの個人用レジストリURLと一緒に `rancher/rancher:latest` の前に追加してください。

例:
```
<REGISTRY.DOMAIN.COM:PORT>/rancher/rancher:latest
```

#### 永続データ

Rancherはデータストアとして `etcd` を使用します。
シングルノードインストールを使用している場合は、組み込み `etcd` が使用されています。
永続データは、コンテナー内の次のパスにあります：`/var/lib/rancher` 。
ホストボリュームをこの場所にバインドマウントして、それが実行されているホスト上のデータを保持することができます。
RancherOSを使用する場合は、データを保存するために使用できる[永続的な保存ディレクトリ](https://rancher.com/docs/os/v1.x/en/installation/system-services/system-docker-volumes/#user-volumes)を確認してください。

Command:
```
docker run -d --restart=unless-stopped \
  -p 80:80 -p 443:443 \
  -v /opt/rancher:/var/lib/rancher \
  rancher/rancher:latest
```

