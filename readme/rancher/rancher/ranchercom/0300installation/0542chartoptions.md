# Chart Options

## Common Options

| Option | Default Value | Description |
| --- | --- | --- |
| `hostname` | "" | `string` - Rancherサーバーの完全修飾ドメイン名 |
| `ingress.tls.source` | "rancher" | `string` - 入力の証明書を入手する場所。 - “rancher, letsEncrypt, secret” |
| `letsEncrypt.email` | "" | `string` - あなたのメールアドレス |
| `letsEncrypt.environment` | "production" | `string` - 有効なオプション： "staging,production" |
| `privateCA` | false | `bool` - あなたの証明書がプライベートCAによって署名されている場合はtrueに設定 |

## Advanced Options

| Option | Default Value | Description |
| --- | --- | --- |
| `additionalTrustedCAs` | false | `bool` - [その他の信頼できるCA](https://rancher.com/docs/rancher/v2.x/en/installation/ha/helm-rancher/chart-options/#additional-trusted-cas)を参照 |
| `addLocal` | "auto" | `string` -  Rancherに「ローカル」Rancherサーバークラスタの検出と[インポートを行わせる「ローカルクラスタ」](https://rancher.com/docs/rancher/v2.x/en/installation/ha/helm-rancher/chart-options/#import-local-cluster)をインポートする |
| `auditLog.destination` | "sidecar" | `string` - サイドカーコンテナコンソールまたはhostPathボリュームへのストリーム - "sidecar、hostPath" |
| `auditLog.hostPath` | "/var/log/rancher/audit" | `string` - ホスト上のログファイルの保存先 |
| `auditLog.level` | 0 | `int` - [API監査ログ](https://rancher.com/docs/rancher/v2.x/en/admin-settings/api-audit-log/)レベルを設定します。 0はオフです。 [0-3] |
| `auditLog.maxAge` | 1 | `int` - 古い監査ログファイルを保持する最大日数 |
| `auditLog.maxBackups` | 1 | `int` - 保持する監査ログファイルの最大数 |
| `auditLog.maxSize` | 100 | `int` - ローテーションされる前の監査ログファイルの最大サイズ（メガバイト単位） |
| `debug` | false | `bool` -  Rancherサーバーにデバッグフラグを設定する |
| `imagePullSecrets` | [] | `list` - プライベートレジストリの認証情報を含むSecretリソースの名前のリスト |
| `ingress.extraAnnotations` | {} | `map` - 入力をカスタマイズするための追加の注釈 |
| `proxy` | "" | `string` - Rancher用のHTTP [S]プロキシサーバー |
| `noProxy` | "127.0.0.0/8,10.0.0.0/8,172.16.0.0/12,192.168.0.0/16" | `string` - プロキシを使用しないホスト名またはIPアドレスのカンマ区切りリスト |
| `resources` | {} | `map` - rancherポッドのリソース要求と制限 |
| `rancherImage` | "rancher/rancher" | `string` - rancher image source |
| `rancherImageTag` | チャートバージョンと同じ | `string` - rancher/rancher image tag |
| `tls` | "ingress" | `string` - 詳細については、[外部TLS終了](https://rancher.com/docs/rancher/v2.x/en/installation/ha/helm-rancher/chart-options/#external-tls-termination)を参照してください。  - "ingress, external" |

## API Audit Log

[API監査ログ](https://rancher.com/docs/rancher/v2.x/en/admin-settings/api-audit-log/)の有効化

他のコンテナログと同じようにこのログを収集できます。
Rancherサーバークラスターの `System` プロジェクトに対して、[Rancher Toolsの下にあるLoggingサービス](https://rancher.com/docs/rancher/v2.x/en/tools/logging/)を有効にします。

```
--set auditLog.level=1
```

デフォルトで監査ログを有効にすると、ランチャーポッドにサイドカーコンテナが作成されます。
このコンテナ（`rancher-audit-log`）はログを`標準出力`に送ります。
他のコンテナログと同じようにこのログを収集できます。
RancherサーバークラスターまたはSystem Projectの[Rancher ToolsでLoggingサービス](https://rancher.com/docs/rancher/v2.x/en/tools/logging/)を有効にします。

サイドカーコンテナにストリーミングするのではなく、ホストシステムと共有されているボリュームにログを転送するには、 `auditLog.destination` を `hostPath` に設定します。
宛先を `hostPath`に設定するときは、ログローテーションのために他のauditLogパラメータを調整することをお勧めします。

## Import `local` Cluster

デフォルトでは、Rancherサーバーは実行中の`ローカル`クラスターを検出してインポートします。
`ローカル`クラスタへのアクセス権を持つユーザは、基本的にRancherサーバによって管理されているすべてのクラスタへの「root」アクセス権を持ちます。

これがご使用の環境で問題になる場合は、最初のインストールでこのオプションを「false」に設定できます。

> **Note：**  
> このオプションは、最初のRancherインストールでのみ有効です。
> 詳細については、[問題16522](https://github.com/rancher/rancher/issues/16522)を参照してください。

```
--set addLocal="false"
```

## Customizing your Ingress

Rancherサーバーで異なる入力をカスタマイズまたは使用するには、独自のIngressアノテーションを設定することができます。

カスタム証明書の発行者の設定例：

```
--set ingress.extraAnnotations.'certmanager\.k8s\.io/cluster-issuer'=ca-key-pair
```

## HTTP Proxy

Rancherはいくつかの機能（helmチャート）のためにインターネットアクセスを必要とします。
プロキシを使用してプロキシサーバーを設定してください。

IP例外を `noProxy` リストに追加します。
必ずサービスクラスタIP範囲（デフォルト：10.43.0.1⁄16）とワーカークラスタ `controlplane` ノードを追加してください。 
RancherはこのリストのCIDR表記範囲をサポートしています。

```
--set proxy="http://<username>:<password>@<proxy_url>:<proxy_port>/"
--set noProxy="127.0.0.0/8\,10.0.0.0/8\,172.16.0.0/12\,192.168.0.0/16"
```

## Additional Trusted CAs

プライベートレジストリ、カタログ、または証明書を傍受するプロキシがある場合は、信頼できるCAを追加でRancherに追加する必要があります。

```
--set additionalTrustedCAs=true
```

Rancherデプロイメントが作成されたら、CA証明書をpem形式で `ca-additional.pem` という名前のファイルにコピーし、`kubectl` を使用して `cattle-system` ネームスペースに `tls-ca-additional` 秘密を作成します。

```
kubectl -n cattle-system create secret generic tls-ca-additional --from-file=ca-additional.pem
```

## Private Registry and Air Gap Installs

Rancherをプライベートレジストリにインストールする方法の詳細については、以下を参照してください。

- [Air Gap: Single Node Install](https://rancher.com/docs/rancher/v2.x/en/installation/air-gap-single-node/)
- [Air Gap: High Availability Install](https://rancher.com/docs/rancher/v2.x/en/installation/air-gap-high-availability/)

## External TLS Termination

ロードバランサをレイヤ4バランサとして設定し、通常の 80/tcp および 443/tcp を Rancher Managementクラスタノードに転送することをお勧めします。
クラスタ上の入力コントローラは、ポート80のhttpトラフィックをポート443のhttpsにリダイレクトします。

Rancherクラスターの外部にあるL7ロードバランサー（入力）で SSL/TLS を終了させることができます。
`--set tls = external` オプションを使用して、ロードバランサーをすべてのRancherクラスターノードのポートhttp 80に指定します。
これにより、httpポート80でRancherインターフェイスが公開されます。
Rancherクラスタへの直接接続が許可されているクライアントは暗号化されません。
これを行う場合は、ネットワークレベルでの直接アクセスを自分のロードバランサーだけに制限することをお勧めします。

> **Note：**  
> プライベートCA署名付き証明書を使用している場合は、 `--set privateCA=true`を追加し、[TLS秘密の追加 - プライベートCA署名付き証明書の使用](https://rancher.com/docs/rancher/v2.x/en/installation/ha/helm-rancher/tls-secrets/#using-a-private-ca-signed-certificate)を参照してRancher用のCA証明書を追加します。

あなたのロードバランサは長命のウェブソケット接続をサポートしなければならず、そしてランチャーがリンクを正しくルーティングできるようにプロキシヘッダを挿入する必要があるでしょう。

### Required Headers

- `Host`
- `X-Forwarded-Proto`
- `X-Forwarded-Port`
- `X-Forwarded-For`

### Recommended Timeouts

- Read Timeout: `1800 seconds`
- Write Timeout: `1800 seconds`
- Connect Timeout: `30 seconds`

### Health Checks

Rancherは `/healthz` エンドポイントのヘルスチェックに `200` 応答します。

### Example NGINX config

このNGINX設定はNGINX 1.14でテストされています。

> **Note：**  
> このNGINX設定はほんの一例であり、あなたの環境には適さないかもしれません。
> 完全なドキュメントは [NGINXロードバランス - HTTPロードバランス](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/)を参照してください。

- `IP_NODE1`、`IP_NODE2`、および `IP_NODE3` をクラスタ内のノードのIPアドレスに置き換えます。
- 両方の `FQDN` をRancherのDNS名に置き換えます。
- `/certs/fullchain.pem` と `/certs/privkey.pem` をそれぞれサーバー証明書とサーバー証明書キーの場所に置き換えます。

```
worker_processes 4;
worker_rlimit_nofile 40000;

events {
    worker_connections 8192;
}

http {
    upstream rancher {
        server IP_NODE_1:80;
        server IP_NODE_2:80;
        server IP_NODE_3:80;
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

