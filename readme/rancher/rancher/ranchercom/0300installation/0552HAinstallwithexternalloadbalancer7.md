# HA Install with External Load Balancer (HTTPS/Layer 7)

> **重要：**  
> RKEアドオンのインストールはRancher v2.0.8までしかサポートされていません。
> 
> HA RancherをインストールするにはRancher helm chartを使用してください。
> 詳しくは、[HAインストール - インストールの概要](https://rancher.com/docs/rancher/v2.x/en/installation/ha/#installation-outline)を参照してください。
>
> 現在RKEアドオンインストール方法を使用している場合は、helmチャートの使用に移る方法の詳細について、[HA RKEアドオンインストールからの移行](https://rancher.com/docs/rancher/v2.x/en/upgrades/upgrades/migrating-from-rke-add-on/)を参照してください。

この手順では、Rancher Kubernetes Engine（RKE）を使用して3ノードクラスタを設定する方法について説明します。
クラスターの唯一の目的は、Rancher用のポッドを実行することです。
設定は次のものに基づいています。

- Layer 7 Loadbalancer with SSL termination (HTTPS)
- [NGINX Ingress controller(HTTP)](https://kubernetes.github.io/ingress-nginx/)

レイヤ７ロードバランサを使用するＨＡ設定では、ロードバランサは、HTTPプロトコル（すなわちアプリケーションレベル）を介してランチャークライアント接続を受け入れる。
このアプリケーションレベルのアクセスにより、ロードバランサはクライアントの要求を読み取り、負荷を最適に分散するロジックを使用してそれらの要求をクラスタノードにリダイレクトできます。

###### HA Rancherは、ロードバランサでのSSL終了を示すレイヤ7ロードバランサを使用してインストールします。

![画像](../pictures/090001rancher2ha.svg)

## Installation Outline(インストール概要)

高可用性構成でのRancherのインストールには複数の手順があります。
この概要を確認して、実行する必要がある各手順について学んでください。

1. [Provision Linux Hosts](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-7-lb/#1-provision-linux-hosts)
1. [Configure Load Balancer](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-7-lb/#2-configure-load-balancer)
1. [Configure DNS](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-7-lb/#3-configure-dns)
1. [Install RKE](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-7-lb/#4-install-rke)
1. [Download RKE Config File Template](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-7-lb/#5-download-rke-config-file-template)
1. [Configure Nodes](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-7-lb/#6-configure-nodes)
1. [Configure Certificates](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-7-lb/#7-configure-certificates)
1. [Configure FQDN](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-7-lb/#8-configure-fqdn)
1. [Configure Rancher version](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-7-lb/#9-configure-rancher-version)
1. [Back Up Your RKE Config File](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-7-lb/#10-back-up-your-rke-config-file)
1. [Run RKE](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-7-lb/#11-run-rke)
1. [Back Up Auto-Generated Config File](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-7-lb/#12-back-up-auto-generated-config-file)

## 1. Provision Linux Hosts

[要件](https://rancher.com/docs/rancher/v2.x/en/installation/requirements/)に従って3つのLinuxホストをプロビジョニングします。

## 2. Configure Load Balancer

Rancherの前でロードバランサーを使用する場合、コンテナがポート通信をポート80またはポート443からリダイレクトする必要はありません。
ヘッダー `X-Forwarded-Proto:https` を渡すことで、このリダイレクトは無効になります。
これは、SSLを外部で終了させるときに想定される構成です。

ロードバランサは、以下をサポートするように設定する必要があります。

- **WebSocket** connections
- **SPDY** / **HTTP/2** protocols
- 以下のヘッダを渡す/設定する：
    | Header | Value | Description |
    | --- | --- | --- |
    | `Host` | FQDNは以前はRancherに到達していました。 | クライアントから要求されたサーバーを識別します。 |
    | `X-Forwarded-Proto` | `https` | クライアントがロードバランサーへの接続に使用したプロトコルを識別します。<br><br>**Note：** このヘッダが存在する場合、`rancher/rancher` はHTTP を HTTPS にリダイレクトしません。 |
    | `X-Forwarded-Port` | ランチャーに到達するために使用されるPort | クライアントがロードバランサへの接続に使用していたプロトコルを識別します。 |
    | `X-Forwarded-For` | クライアント接続のIP | クライアントの発信元IPアドレスを識別します。 |

ヘルスチェックはノードの `/healthz` エンドポイントで実行できます。これはHTTP 200を返します。

次のロードバランサの設定例があります。
- [Amazon ALB configuration](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-7-lb/alb/)
- [NGINX configuration](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-7-lb/nginx/)

## 3. Configure DNS

Rancherへのアクセスに使用する完全修飾ドメイン名（FQDN）を選択してください（例：`rancher.yourdomain.com`）。

1. DNSサーバーにログインして、[ロードバランサー](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-7-lb/#2-configure-load-balancer)のIPアドレスを指す `DNS A` レコードを作成します。

1. `DNS A` が正しく機能していることを確認します。
任意の端末から次のコマンドを実行し、`HOSTNAME.DOMAIN.COM` を選択したFQDNに置き換えます。
    ```
    nslookup HOSTNAME.DOMAIN.COM
    ```

    **結果：** Terminalに次のような出力が表示されます。
    ```
    $ nslookup rancher.yourdomain.com
    Server:         YOUR_HOSTNAME_IP_ADDRESS
    Address:        YOUR_HOSTNAME_IP_ADDRESS#53

    Non-authoritative answer:
    Name:   rancher.yourdomain.com
    Address: HOSTNAME.DOMAIN.COM
    ```

## 4. Install RKE

RKE（Rancher Kubernetes Engine）は、あなたのLinuxホストにKubernetesをインストールするのに使用できる、高速で多用途のKubernetesインストーラです。
RKEを使用してクラスターをセットアップし、Rancherを実行します。

1. [RKEインストール](https://rancher.com/docs/rke/v0.1.x/en/installation/)の指示に従ってください。

1. 次のコマンドを実行して、RKEが実行可能になったことを確認します。

    ```
    rke --version
    ```

## 5. Download RKE Config File Template

RKEはYAML設定ファイルを使ってKubernetesクラスタをインストールして設定します。
使用するSSL証明書に応じて、2つのテンプレートから選択できます。

1. 使用しているSSL証明書に応じて、次のいずれかのテンプレートをダウンロードしてください。
    - [自己署名証明書のテンプレート](https://raw.githubusercontent.com/rancher/rancher/master/rke-templates/3-node-externalssl-certificate.yml)  
    `3-node-externalssl-certificate.yml`
    - [認識されたCAによって署名された証明書のテンプレート](https://raw.githubusercontent.com/rancher/rancher/master/rke-templates/3-node-externalssl-recognizedca.yml)  
    `3-node-externalssl-recognizedca.yml`

    > 詳細設定オプション：  
    > - Rancher APIを使ったすべてのトランザクションの記録が欲しいですか？
    > RKE設定ファイルを編集して[API監査](https://rancher.com/docs/rancher/v2.x/en/admin-settings/api-audit-log/)機能を有効にします。
    > 詳しくは、[RKE構成ファイル](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/api-auditing/)でそれを有効にする方法を参照してください。
    > - あなたのRKEテンプレートに利用可能な他の設定オプションを知りたいですか？
    > [RKE Documentation：Config Options](https://rancher.com/docs/rke/v0.1.x/en/config-options/)を参照してください。

1. ファイル名を `rancher-cluster.yml` に変更してください。

## 6. Configure Nodes

`rancher-cluster.yml` 設定ファイルのテンプレートを作成したら、Linuxセクションを指すようにnodesセクションを編集します。

1. テキストエディタで `rancher-cluster.yml` を開きます。

1. [Linuxホスト](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-7-lb/#1-provision-linux-hosts)の情報で `nodes` セクションを更新します。

    クラスタ内の各ノードで、次のプレースホルダを更新します：`IP_ADDRESS_X`と`USER`。
    指定されたユーザーはDocketソケットにアクセスできるはずです。
    指定されたユーザーでログインして`docker ps`を実行することでこれをテストできます。

    > **Note：**  
    > RHEL / CentOSを使用している場合、[https://bugzilla.redhat.com/show_bug.cgi？id=1527565](https://bugzilla.redhat.com/show_bug.cgi?id=1527565)のため、SSHユーザーはrootになれません。
    RHEL/CentOS固有の要件については、[オペレーティングシステムの要件](https://rancher.com/docs/rke/v0.1.x/en/os/)を参照してください。

    ```
    nodes:
        # The IP address or hostname of the node
    - address: IP_ADDRESS_1
        # User that can login to the node and has access to the Docker socket (i.e. can execute `docker ps` on the node)
        # When using RHEL/CentOS, this can't be root due to https://bugzilla.redhat.com/show_bug.cgi?id=1527565
        user: USER
        role: [controlplane,etcd,worker]
        # Path the SSH key that can be used to access to node with the specified user
        ssh_key_path: ~/.ssh/id_rsa
    - address: IP_ADDRESS_2
        user: USER
        role: [controlplane,etcd,worker]
        ssh_key_path: ~/.ssh/id_rsa
    - address: IP_ADDRESS_3
        user: USER
        role: [controlplane,etcd,worker]
        ssh_key_path: ~/.ssh/id_rsa
    ```

1. **オプション：** デフォルトで、`rancher-cluster.yml` はあなたのデータのバックアップスナップショットを取るように設定されています。
これらのスナップショットを無効にするには、以下に示すように `backup` 指示句の設定を `false` に変更します。

```
services:
  etcd:
    backup: false   
```

## 7. Configure Certificates

セキュリティ上の理由から、Rancherを使用する場合はSSL（Secure Sockets Layer）が必要です。
SSLは、あなたがログインしたり、クラスタとやり取りするときのように、すべてのRancherネットワーク通信を保護します。

以下のオプションから選択してください。

- [オプションA  - 自分の証明書を持参する：自己署名](055201optiona.md)
- [オプションB  - 自分の証明書を持参する：認識されたCAによって署名された](055202optionb.md)

## 8. Configure FQDN

RKE設定ファイルに `<FQDN>`への参照が1つあります。
この参照を[3. DNSの設定](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-7-lb/#3-configure-dns)で選択したFQDNに置き換えます。

1. `rancher-cluster.yml` を開きます。

1. In the `kind: Ingress` with `name: cattle-ingress-http`：
`<FQDN>`を[3. DNSの設定](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-7-lb/#3-configure-dns)で選択したFQDNに置き換えます。

    **結果：**   
    値を置き換えた後、ファイルは次の例のようになります（base64でエンコードされた文字列は異なるはずです）。

    ```
    apiVersion: extensions/v1beta1
        kind: Ingress
        metadata:
            namespace: cattle-system
            name: cattle-ingress-http
            annotations:
                nginx.ingress.kubernetes.io/proxy-connect-timeout: "30"
                nginx.ingress.kubernetes.io/proxy-read-timeout: "1800"   # Max time in seconds for ws to remain shell window open
                nginx.ingress.kubernetes.io/proxy-send-timeout: "1800"   # Max time in seconds for ws to remain shell window open
        spec:
            rules:
            - host: rancher.yourdomain.com
                http:
                    paths:
                    - backend:
                        serviceName: cattle-service
                        servicePort: 80
    ```

1. ファイルを保存して閉じます。

## 9. Configure Rancher version

置き換える必要がある最後の参照は `<RANCHER_VERSION>`です。
これは安定版としてマークされているRancherバージョンに置き換える必要があります。
Rancherの最新の安定版リリースは [GitHub README](https://github.com/rancher/rancher/blob/master/README.md) にあります。
バージョンが実際のバージョン番号であり、`stable` や `latest` のような名前付きタグではないことを確認してください。
以下の例は、`v2.0.6` に設定されたバージョンを示しています。

```
      spec:
        serviceAccountName: cattle-admin
        containers:
        - image: rancher/rancher:v2.0.6
          imagePullPolicy: Always
```

## 10. Back Up Your RKE Config File

RKE設定ファイル `rancher-cluster.yml` を閉じた後、安全な場所にバックアップしてください。
Rancherをアップグレードするときが来たら、このファイルをもう一度使うことができます。

## 11. Run RKE

すべての設定が整ったら、RKEを使用してRancherを起動します。
このアクションを完了するには、`rke up` コマンドを実行し、 `--config` パラメータを使用して設定ファイルを指定します。

1. ワークステーションから、`rancher-cluster.yml` とダウンロードした `rke` バイナリが同じディレクトリにあることを確認してください。

1. ターミナルインスタンスを開きます。
あなたの設定ファイルと `rke` を含むディレクトリに変更してください。

1. 下記のいずれかの `ke up` コマンドを入力してください。

    ```
    rke up --config rancher-cluster.yml
    ```

    **結果：** 出力は以下のスニペットのようになるはずです。

    ```
    INFO[0000] Building Kubernetes cluster
    INFO[0000] [dialer] Setup tunnel for host [1.1.1.1]
    INFO[0000] [network] Deploying port listener containers
    INFO[0000] [network] Pulling image [alpine:latest] on host [1.1.1.1]
    ...
    INFO[0101] Finished building Kubernetes cluster successfully
    ```

## 12. Back Up Auto-Generated Config File

インストール中に、RKEは自動的に `kube_config_rancher-cluster.yml` という設定ファイルを `rancher-cluster.yml` ファイルと同じディレクトリに生成します。
このファイルをコピーして安全な場所にバックアップしてください。
このファイルは後でRancher Serverをアップグレードするときに使用します。

## What’s Next?

- **推奨事項：** 災害発生時にRancher Serverをバックアップする方法については、[バックアップの作成 - 高可用性のバックアップと復元](https://rancher.com/docs/rancher/v2.x/en/backups/backups/ha-backups/)を参照してください。

- Kubernetesクラスタを作成します。[クラスタの作成](https://rancher.com/docs/rancher/v2.x/en/cluster-provisioning/)

## FAQ and Troubleshooting

### How Do I Know if My Certificates are in PEM Format?

次のような特徴によってPEM形式を認識できます。
- ファイルは次のヘッダで始まります。  
    ```-----BEGIN CERTIFICATE-----```
- ヘッダーの後には長い文字列が続きます。 本当に長い
- ファイルはフッターで終わります。  
    ```-----END CERTIFICATE-----```

**PEM証明書の例：**

```
----BEGIN CERTIFICATE-----
MIIGVDCCBDygAwIBAgIJAMiIrEm29kRLMA0GCSqGSIb3DQEBCwUAMHkxCzAJBgNV
... more lines
VWQqljhfacYPgp8KJUJENQ9h5hZ2nSCrI+W00Jcw4QcEdCI8HL5wmg==
-----END CERTIFICATE-----
```

### How Can I Encode My PEM Files in base64?

証明書をbase64でエンコードするには

1. PEMファイルが存在する場所にディレクトリを変更します。

1. 次のいずれかのコマンドを実行してください。  `FILENAME` を証明書の名前に置き換えます。
    ```
    # MacOS
    cat FILENAME | base64
    # Linux
    cat FILENAME | base64 -w0
    # Windows
    certutil -encode FILENAME FILENAME.base64
    ```

### How Can I Verify My Generated base64 String For The Certificates?

base64で証明書を復号化するには

1. 生成されたbase64文字列をコピーします。

1. 次のいずれかのコマンドを実行してください。
`YOUR_BASE64_STRING` を以前にコピーしたbase64文字列に置き換えます。
    ```
    # MacOS
    echo YOUR_BASE64_STRING | base64 -D
    # Linux
    echo YOUR_BASE64_STRING | base64 -d
    # Windows
    certutil -decode FILENAME.base64 FILENAME.verify
    ```

### What is the Order of Certificates if I Want to Add My Intermediate(s)?

証明書を追加する順序は次のとおりです。

```
-----BEGIN CERTIFICATE-----
%YOUR_CERTIFICATE%
-----END CERTIFICATE-----
-----BEGIN CERTIFICATE-----
%YOUR_INTERMEDIATE_CERTIFICATE%
-----END CERTIFICATE-----
```

### How Do I Validate My Certificate Chain?

`openssl` バイナリを使用して証明書チェーンを検証できます。
コマンドの出力（以下のコマンド例を参照）が`Verify return code: 0 (ok)` で終わっていれば、証明書チェーンは有効です。
`ca.pem` ファイルは、`rancher/rancher`のコンテナーに追加したものと同じでなければなりません。
認識された認証局によって署名された証明書を使用するときは、`-CAfile` パラメーターを省略することができます。

**Command:**
```
openssl s_client -CAfile ca.pem -connect rancher.yourdomain.com:443 -servername rancher.yourdomain.com
...
    Verify return code: 0 (ok)
```









