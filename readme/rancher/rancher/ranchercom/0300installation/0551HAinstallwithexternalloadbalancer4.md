# HA Install with External Load Balancer (TCP/Layer 4)

> ### **重要：**  
> ### RKEアドオンのインストールはRancher v2.0.8までしかサポートされていません。
> HA RancherをインストールするにはRancher helm chartを使用してください。
> 詳しくは、[HAインストール - インストールの概要](https://rancher.com/docs/rancher/v2.x/en/installation/ha/#installation-outline)を参照してください。
>
> 現在RKEアドオンインストール方法を使用している場合は、helmチャートの使用に移る方法の詳細について、[HA RKEアドオンインストールからの移行](https://rancher.com/docs/rancher/v2.x/en/upgrades/upgrades/migrating-from-rke-add-on/)を参照してください。

この手順では、Rancher Kubernetes Engine（RKE）を使用して3ノードクラスタを設定する方法について説明します。
クラスターの唯一の目的は、Rancher用のポッドを実行することです。
設定は次のものに基づいています。

- Layer 4 load balancer (TCP)
- [NGINX ingress controller with SSL termination (HTTPS)](https://kubernetes.github.io/ingress-nginx/)

レイヤ４ロードバランサを使用するＨＡ設定では、ロードバランサは TCP/UDP プロトコル（すなわちトランスポートレベル）を介してランチャークライアント接続を受け入れる。
その後、ロードバランサはリクエスト自体を読み込まずに、これらの接続を個々のクラスタノードに転送します。
ロードバランサーは転送先のパケットを読み取ることができないため、ルーティングの決定は限られています。

##### HA Rancherはレイヤ4ロードバランサを使用してインストールします。入力コントローラでのSSL終了を示しています

![画像](../pictures/080001rancher2ha.svg)

## Installation Outline(インストール概要)

高可用性構成でのRancherのインストールには複数の手順があります。
この概要を確認して、実行する必要がある各手順について学んでください。

1.  [Provision Linux Hosts](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-4-lb/#1-provision-linux-hosts)
1. [Configure Load Balancer](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-4-lb/#2-configure-load-balancer)
1. [Configure DNS](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-4-lb/#3-configure-dns)
1. [Install RKE](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-4-lb/#4-install-rke)
1. [Download RKE Config File Template](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-4-lb/#5-download-rke-config-file-template)
1. [Configure Nodes](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-4-lb/#6-configure-nodes)
1. [Configure Certificates](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-4-lb/#7-configure-certificates)
1. [Configure FQDN](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-4-lb/#8-configure-fqdn)
1. [Configure Rancher version](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-4-lb/#9-configure-rancher-version)
1. [Back Up Your RKE Config File](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-4-lb/#10-back-up-your-rke-config-file)
1. [Run RKE](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-4-lb/#11-run-rke)
1. [Back Up Auto-Generated Config File](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-4-lb/#12-back-up-auto-generated-config-file)

## 1. Provision Linux Hosts

[要件](https://rancher.com/docs/rancher/v2.x/en/installation/requirements/)に従って3つのLinuxホストをプロビジョニングします。

## 2. Configure Load Balancer

レイヤ4ロードバランサ（TCP）としてNGINXを使用します。
NGINXはすべての接続をあなたのRancherノードのひとつに転送します。
Amazon NLBを使用する場合は、この手順を省略して[Amazon NLB設定](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-4-lb/nlb/)を使用できます。

> **Note：**  
> この構成では、ロードバランサーはLinuxホストの前に配置されています。
> ロードバランサーは、NGINXを実行できる、使用可能な任意のホストにすることができます。
> 
> 注意点：Rancherノードをロードバランサーとして使用しないでください。

### A. Install NGINX

ロードバランサーホストにNGINXをインストールすることから始めます。
NGINXはすべての既知のオペレーティングシステムに利用可能なパッケージを持っています。
NGINXのインストールについては、それらの[インストールドキュメント](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/)を参照してください。

`stream` モジュールが必要です。
これは公式のNGINXパッケージを使用するときに存在します。
ご使用のオペレーティングシステムに NGINX `stream` モジュールをインストールして有効にする方法については、OSのマニュアルを参照してください。

### B. Create NGINX Configuration

NGINXをインストールした後、あなたはあなたのノードのためのIPアドレスでNGINX設定ファイル、`nginx.conf` を更新する必要があります。

1. 以下のコードサンプルをコピーして、お気に入りのテキストエディタに貼り付けます。
`nginx.conf` として保存します。

1. `nginx.conf` から、`IP_NODE_1`、`IP_NODE_2`、および `IP_NODE_3`を[Linuxホスト](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-4-lb/#1-provision-linux-hosts)のIPに置き換えます。

    > **Note：**  
    > このNginxの設定はほんの一例であり、あなたの環境には適さないかもしれません。
    > 完全なドキュメンテーションについては、[NGINXロードバランシング -  TCPとUDPロードバランサ](https://docs.nginx.com/nginx/admin-guide/load-balancer/tcp-udp-load-balancer/)を見てください。

    Example NGINX config:
    
    ```
    worker_processes 4;
    worker_rlimit_nofile 40000;

    events {
        worker_connections 8192;
    }

    http {
        server {
            listen         80;
            return 301 https://$host$request_uri;
        }
    }

    stream {
        upstream rancher_servers {
            least_conn;
            server IP_NODE_1:443 max_fails=3 fail_timeout=5s;
            server IP_NODE_2:443 max_fails=3 fail_timeout=5s;
            server IP_NODE_3:443 max_fails=3 fail_timeout=5s;
        }
        server {
            listen     443;
            proxy_pass rancher_servers;
        }
    }
    ```

1. `nginx.conf`をロードバランサーに保存します。
    Path： `/etc/nginx/nginx.conf` 

1. 次のコマンドを実行して、NGINX設定にアップデートをロードします。
    ```
    # nginx -s reload
    ```

### Option - Run NGINX as Docker container

NGINXをパッケージとしてオペレーティングシステムにインストールする代わりに、Dockerコンテナとして実行することができます。
編集したサンプルNGINX設定を `/etc/nginx.conf` として保存し、次のコマンドを実行してNGINXコンテナを起動します。

```
docker run -d --restart=unless-stopped \
  -p 80:80 -p 443:443 \
  -v /etc/nginx.conf:/etc/nginx/nginx.conf \
  nginx:1.14
```

## 3. Configure DNS

Rancherへのアクセスに使用する完全修飾ドメイン名（FQDN）を選択してください（例：`rancher.yourdomain.com` ）。

1. DNSサーバーにログインして、[ロードバランサー](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-4-lb/#2-configure-load-balancer)のIPアドレスを指す `DNS A` レコードを作成します。

1. `DNS A` が正しく機能していることを確認します。
任意の端末から次のコマンドを実行し、`HOSTNAME.DOMAIN.COM` を選択したFQDNに置き換えます。

    ```
    nslookup HOSTNAME.DOMAIN.COM
    ```

    **結果：** ターミナルに次のような出力が表示されます。

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

RKEはあなたのKubernetesクラスタをインストールして設定するために `.yml` 設定ファイルを使います。
使用するSSL証明書に応じて、2つのテンプレートから選択できます。

1. 使用しているSSL証明書に応じて、次のいずれかのテンプレートをダウンロードしてください。

    - [Template for self-signed certificate](https://raw.githubusercontent.com/rancher/rancher/master/rke-templates/3-node-certificate.yml)  
    `3-node-certificate.yml`
    - [Template for certificate signed by recognized CA](https://raw.githubusercontent.com/rancher/rancher/master/rke-templates/3-node-certificate-recognizedca.yml)  
    `3-node-certificate-recognizedca.yml`

    > 詳細設定オプション：  
    > - Rancher APIを使ったすべてのトランザクションの記録が欲しいですか？
    > RKE設定ファイルを編集して[API監査](https://rancher.com/docs/rancher/v2.x/en/admin-settings/api-audit-log/)機能を有効にします。
    > 詳しくは、[RKE構成ファイル](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/api-auditing/)でそれを有効にする方法を参照してください。
    > - あなたのRKEテンプレートに利用可能な他の設定オプションを知りたいですか？
    > [RKE Documentation：Config Options](https://rancher.com/docs/rke/v0.1.x/en/config-options/)を参照してください。

1. ファイル名を `rancher-cluster.yml` に変更してください。

## 6. Configure Nodes

`rancher-cluster.yml` 設定ファイルのテンプレートを作成したら、Linuxセクションを指すようにnodesセクションを編集します。

1. お気に入りのテキストエディタで `rancher-cluster.yml` を開きます。

1. [Linuxホスト](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-4-lb/#1-provision-linux-hosts)の情報で `nodes`セクションを更新します。

    クラスタ内の各ノードで、次のプレースホルダを更新します。
    `IP_ADDRESS_X`と`USER`。
    指定されたユーザーはDocketソケットにアクセスできるはずです。
    指定されたユーザーでログインして `docker ps` を実行することでこれをテストできます。

    > **Note：**  
    > RHEL/CentOSを使用している場合、[https://bugzilla.redhat.com/show_bug.cgi?id=1527565](https://bugzilla.redhat.com/show_bug.cgi?id=1527565) のため、SSHユーザーはrootになれません。
    RHEL/CentOS 固有の要件については、[オペレーティングシステムの要件](https://rancher.com/docs/rke/v0.1.x/en/os/)を参照してください。

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

1. **オプション：**  
デフォルトで、`rancher-cluster.yml` はあなたのデータのバックアップスナップショットを取るように設定されています。
これらのスナップショットを無効にするには、以下に示すように `backup`指示句の設定を `false`に変更します。

    ```
    services:
    etcd:
        backup: false   
    ```

## 7. Configure Certificates

セキュリティ上の理由から、Rancherを使用する場合はSSL（Secure Sockets Layer）が必要です。
SSLは、あなたがログインしたり、クラスタとやり取りするときのように、すべてのRancherネットワーク通信を保護します。

以下のオプションから選択してください。

- オプションA  - 自分の証明書を持参する：自己署名

    > **前提条件：**  
    > 自己署名証明書を作成します。
    >
    > - 証明書ファイルは[PEM形式](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-4-lb/#pem)でなければなりません。
    > 証明書ファイルは[base64](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-4-lb/#base64)でエンコードされている必要があります。
    > - 証明書ファイルで、すべての中間証明書をチェーンに含めます。
    > 最初にあなたの証明書と一緒にあなたの証明書を注文し、続いて中間物を注文します。
    > 例については、[中間証明書](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-4-lb/#cert-order)を参照してください。

    1. In `kind: Secret` with `name: cattle-keys-ingress`:
        - `<BASE64_CRT>` を証明書ファイルのbase64エンコード文字列に置き換えます。  
        （通常は `cert.pem` または `domain.crt` と呼ばれる）
        - `<BASE64_KEY>` を証明書キーファイルのbase64エンコード文字列に置き換えます。  
        （通常は `key.pem` または `domain.key` と呼ばれます）

        > **Note：**  
        > base64でエンコードされた文字列は、`tls.crt` または `tls.key` と同じ行に、最初に改行を入れずに、その間または最後に入れる必要があります。

        **結果：** 
        値を置き換えた後、ファイルは次の例のようになります。
        （base64でエンコードされた文字列は異なるはずです）

        ```
        ---
        apiVersion: v1
        kind: Secret
        metadata:
            name: cattle-keys-ingress
            namespace: cattle-system
        type: Opaque
        data:
            tls.crt: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUM1RENDQWN5Z0F3SUJBZ0lKQUlHc25NeG1LeGxLTUEwR0NTcUdTSWIzRFFFQkN3VUFNQkl4RURBT0JnTlYKQkFNTUIzUmxjM1F0WTJFd0hoY05NVGd3TlRBMk1qRXdOREE1V2hjTk1UZ3dOekExTWpFd05EQTVXakFXTVJRdwpFZ1lEVlFRRERBdG9ZUzV5Ym1Ob2NpNXViRENDQVNJd0RRWUpLb1pJaHZjTkFRRUJCUUFEZ2dFUEFEQ0NBUW9DCmdnRUJBTFJlMXdzekZSb2Rib2pZV05DSHA3UkdJaUVIMENDZ1F2MmdMRXNkUUNKZlcrUFEvVjM0NnQ3bSs3TFEKZXJaV3ZZMWpuY2VuWU5JSGRBU0VnU0ducWExYnhUSU9FaE0zQXpib3B0WDhjSW1OSGZoQlZETGdiTEYzUk0xaQpPM1JLTGdIS2tYSTMxZndjbU9zWGUwaElYQnpUbmxnM20vUzlXL3NTc0l1dDVwNENDUWV3TWlpWFhuUElKb21lCmpkS3VjSHFnMTlzd0YvcGVUalZrcVpuMkJHazZRaWFpMU41bldRV0pjcThTenZxTTViZElDaWlwYU9hWWQ3RFEKYWRTejV5dlF0YkxQNW4wTXpnOU43S3pGcEpvUys5QWdkWDI5cmZqV2JSekp3RzM5R3dRemN6VWtLcnZEb05JaQo0UFJHc01yclFNVXFSYjRSajNQOEJodEMxWXNDQXdFQUFhTTVNRGN3Q1FZRFZSMFRCQUl3QURBTEJnTlZIUThFCkJBTUNCZUF3SFFZRFZSMGxCQll3RkFZSUt3WUJCUVVIQXdJR0NDc0dBUVVGQndNQk1BMEdDU3FHU0liM0RRRUIKQ3dVQUE0SUJBUUNKZm5PWlFLWkowTFliOGNWUW5Vdi9NZkRZVEJIQ0pZcGM4MmgzUGlXWElMQk1jWDhQRC93MgpoOUExNkE4NGNxODJuQXEvaFZYYy9JNG9yaFY5WW9jSEg5UlcvbGthTUQ2VEJVR0Q1U1k4S292MHpHQ1ROaDZ6Ci9wZTNqTC9uU0pYSjRtQm51czJheHFtWnIvM3hhaWpYZG9kMmd3eGVhTklvRjNLbHB2aGU3ZjRBNmpsQTM0MmkKVVlCZ09iN1F5KytRZWd4U1diSmdoSzg1MmUvUUhnU2FVSkN6NW1sNGc1WndnNnBTUXhySUhCNkcvREc4dElSYwprZDMxSk1qY25Fb1Rhc1Jyc1NwVmNGdXZyQXlXN2liakZyYzhienBNcE1obDVwYUZRcEZzMnIwaXpZekhwakFsCk5ZR2I2OHJHcjBwQkp3YU5DS2ErbCtLRTk4M3A3NDYwCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
            tls.key: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFb3dJQkFBS0NBUUVBdEY3WEN6TVZHaDF1aU5oWTBJZW50RVlpSVFmUUlLQkMvYUFzU3gxQUlsOWI0OUQ5ClhmanEzdWI3c3RCNnRsYTlqV09keDZkZzBnZDBCSVNCSWFlcHJWdkZNZzRTRXpjRE51aW0xZnh3aVkwZCtFRlUKTXVCc3NYZEV6V0k3ZEVvdUFjcVJjamZWL0J5WTZ4ZDdTRWhjSE5PZVdEZWI5TDFiK3hLd2k2M21uZ0lKQjdBeQpLSmRlYzhnbWlaNk4wcTV3ZXFEWDJ6QVgrbDVPTldTcG1mWUVhVHBDSnFMVTNtZFpCWWx5cnhMTytvemx0MGdLCktLbG81cGgzc05CcDFMUG5LOUMxc3MvbWZRek9EMDNzck1Xa21oTDcwQ0IxZmIydCtOWnRITW5BYmYwYkJETnoKTlNRcXU4T2cwaUxnOUVhd3l1dEF4U3BGdmhHUGMvd0dHMExWaXdJREFRQUJBb0lCQUJKYUErOHp4MVhjNEw0egpwUFd5bDdHVDRTMFRLbTNuWUdtRnZudjJBZXg5WDFBU2wzVFVPckZyTnZpK2xYMnYzYUZoSFZDUEN4N1RlMDVxClhPa2JzZnZkZG5iZFQ2RjgyMnJleVByRXNINk9TUnBWSzBmeDVaMDQwVnRFUDJCWm04eTYyNG1QZk1vbDdya2MKcm9Kd09rOEVpUHZZekpsZUd0bTAwUm1sRysyL2c0aWJsOTVmQXpyc1MvcGUyS3ZoN2NBVEtIcVh6MjlpUmZpbApiTGhBamQwcEVSMjNYU0hHR1ZqRmF3amNJK1c2L2RtbDZURDhrSzFGaUtldmJKTlREeVNXQnpPbXRTYUp1K01JCm9iUnVWWG4yZVNoamVGM1BYcHZRMWRhNXdBa0dJQWxOWjRHTG5QU2ZwVmJyU0plU3RrTGNzdEJheVlJS3BWZVgKSVVTTHM0RUNnWUVBMmNnZUE2WHh0TXdFNU5QWlNWdGhzbXRiYi9YYmtsSTdrWHlsdk5zZjFPdXRYVzkybVJneQpHcEhUQ0VubDB0Z1p3T081T1FLNjdFT3JUdDBRWStxMDJzZndwcmgwNFZEVGZhcW5QNTBxa3BmZEJLQWpmanEyCjFoZDZMd2hLeDRxSm9aelp2VkowV0lvR1ZLcjhJSjJOWGRTUVlUanZUZHhGczRTamdqNFFiaEVDZ1lFQTFBWUUKSEo3eVlza2EvS2V2OVVYbmVrSTRvMm5aYjJ1UVZXazRXSHlaY2NRN3VMQVhGY3lJcW5SZnoxczVzN3RMTzJCagozTFZNUVBzazFNY25oTTl4WE4vQ3ZDTys5b2t0RnNaMGJqWFh6NEJ5V2lFNHJPS1lhVEFwcDVsWlpUT3ZVMWNyCm05R3NwMWJoVDVZb2RaZ3IwUHQyYzR4U2krUVlEWnNFb2lFdzNkc0NnWUVBcVJLYWNweWZKSXlMZEJjZ0JycGkKQTRFalVLMWZsSjR3enNjbGFKUDVoM1NjZUFCejQzRU1YT0kvSXAwMFJsY3N6em83N3cyMmpud09mOEJSM0RBMwp6ZTRSWDIydWw4b0hGdldvdUZOTTNOZjNaNExuYXpVc0F0UGhNS2hRWGMrcEFBWGthUDJkZzZ0TU5PazFxaUNHCndvU212a1BVVE84b1ViRTB1NFZ4ZmZFQ2dZQUpPdDNROVNadUlIMFpSSitIV095enlOQTRaUEkvUkhwN0RXS1QKajVFS2Y5VnR1OVMxY1RyOTJLVVhITXlOUTNrSjg2OUZPMnMvWk85OGg5THptQ2hDTjhkOWN6enI5SnJPNUFMTApqWEtBcVFIUlpLTFgrK0ZRcXZVVlE3cTlpaHQyMEZPb3E5OE5SZDMzSGYxUzZUWDNHZ3RWQ21YSml6dDAxQ3ZHCmR4VnVnd0tCZ0M2Mlp0b0RLb3JyT2hvdTBPelprK2YwQS9rNDJBOENiL29VMGpwSzZtdmxEWmNYdUF1QVZTVXIKNXJCZjRVYmdVYndqa1ZWSFR6LzdDb1BWSjUvVUxJWk1Db1RUNFprNTZXWDk4ZE93Q3VTVFpZYnlBbDZNS1BBZApTZEpuVVIraEpnSVFDVGJ4K1dzYnh2d0FkbWErWUhtaVlPRzZhSklXMXdSd1VGOURLUEhHCi0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==
        ```
    1. In `kind: Secret` with `name: cattle-keys-server`、`<BASE64_CA>`をCA証明書ファイルのbase64エンコード文字列に置き換えます。  
    （通常は `ca.pem` または `ca.crt` と呼ばれる）

        > **Note：**  
        > base64でエンコードされた文字列は、`cacerts.pem` と同じ行にあります。
        > 先頭、その間、最後に改行はありません。

        **結果：**
        ファイルは次の例のようになります。  
        （base64でエンコードされた文字列は異なります）

        ```
        ---
        apiVersion: v1
        kind: Secret
        metadata:
        name: cattle-keys-server
        namespace: cattle-system
        type: Opaque
        data:
        cacerts.pem: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNvRENDQVlnQ0NRRHVVWjZuMEZWeU16QU5CZ2txaGtpRzl3MEJBUXNGQURBU01SQXdEZ1lEVlFRRERBZDAKWlhOMExXTmhNQjRYRFRFNE1EVXdOakl4TURRd09Wb1hEVEU0TURjd05USXhNRFF3T1Zvd0VqRVFNQTRHQTFVRQpBd3dIZEdWemRDMWpZVENDQVNJd0RRWUpLb1pJaHZjTkFRRUJCUUFEZ2dFUEFEQ0NBUW9DZ2dFQkFNQmpBS3dQCndhRUhwQTdaRW1iWWczaTNYNlppVmtGZFJGckJlTmFYTHFPL2R0RUdmWktqYUF0Wm45R1VsckQxZUlUS3UzVHgKOWlGVlV4Mmo1Z0tyWmpwWitCUnFiZ1BNbk5hS1hocmRTdDRtUUN0VFFZdGRYMVFZS0pUbWF5NU45N3FoNTZtWQprMllKRkpOWVhHWlJabkdMUXJQNk04VHZramF0ZnZOdmJ0WmtkY2orYlY3aWhXanp2d2theHRUVjZlUGxuM2p5CnJUeXBBTDliYnlVcHlad3E2MWQvb0Q4VUtwZ2lZM1dOWmN1YnNvSjhxWlRsTnN6UjVadEFJV0tjSE5ZbE93d2oKaG41RE1tSFpwZ0ZGNW14TU52akxPRUc0S0ZRU3laYlV2QzlZRUhLZTUxbGVxa1lmQmtBZWpPY002TnlWQUh1dApuay9DMHpXcGdENkIwbkVDQXdFQUFUQU5CZ2txaGtpRzl3MEJBUXNGQUFPQ0FRRUFHTCtaNkRzK2R4WTZsU2VBClZHSkMvdzE1bHJ2ZXdia1YxN3hvcmlyNEMxVURJSXB6YXdCdFJRSGdSWXVtblVqOGo4T0hFWUFDUEthR3BTVUsKRDVuVWdzV0pMUUV0TDA2eTh6M3A0MDBrSlZFZW9xZlVnYjQrK1JLRVJrWmowWXR3NEN0WHhwOVMzVkd4NmNOQQozZVlqRnRQd2hoYWVEQmdma1hXQWtISXFDcEsrN3RYem9pRGpXbi8walI2VDcrSGlaNEZjZ1AzYnd3K3NjUDIyCjlDQVZ1ZFg4TWpEQ1hTcll0Y0ZINllBanlCSTJjbDhoSkJqa2E3aERpVC9DaFlEZlFFVFZDM3crQjBDYjF1NWcKdE03Z2NGcUw4OVdhMnp5UzdNdXk5bEthUDBvTXl1Ty82Tm1wNjNsVnRHeEZKSFh4WTN6M0lycGxlbTNZQThpTwpmbmlYZXc9PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
        ```

- オプションB  - 自分の証明書を持参する：認識されたCAによって署名された

    > **Note：**
    > 自己署名証明書を使用している場合は、ここをクリックして先に進んでください。

    認識された認証局によって署名された証明書を使用している場合は、証明書ファイルと証明書キーファイル用にbase64でエンコードされた文字列を生成する必要があります。
    証明書ファイルにチェーン内のすべての[中間証明書](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-4-lb/#cert-order)が含まれていることを確認してください。
    の場合の証明書の順序は、最初に自分の証明書、次に中間証明書の順になります。
    どの中間証明書を含める必要があるかについては、CSP（証明書サービスプロバイダ）のドキュメントを参照してください。

    In the `kind: Secret` with `name: cattle-keys-ingress`:

    - `<BASE64_CRT>` を証明書ファイルのbase64エンコード文字列（通常は `cert.pem` または `domain.crt` と呼ばれる）に置き換えます。
    - `<BASE64_KEY>` を証明書キーファイルのbase64エンコード文字列（通常は `key.pem` または `domain.key` と呼びます）に置き換えます。

    値を置き換えた後、ファイルは次の例のようになります（base64でエンコードされた文字列は異なるはずです）。

    > **Note：**  
    > base64でエンコードされた文字列は、`tls.crt` または `tls.key` と同じ行に、最初に改行を入れずに、その間または最後に入れる必要があります。

    ```
    ---
    apiVersion: v1
    kind: Secret
    metadata:
    name: cattle-keys-ingress
    namespace: cattle-system
    type: Opaque
    data:
    tls.crt: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUM1RENDQWN5Z0F3SUJBZ0lKQUlHc25NeG1LeGxLTUEwR0NTcUdTSWIzRFFFQkN3VUFNQkl4RURBT0JnTlYKQkFNTUIzUmxjM1F0WTJFd0hoY05NVGd3TlRBMk1qRXdOREE1V2hjTk1UZ3dOekExTWpFd05EQTVXakFXTVJRdwpFZ1lEVlFRRERBdG9ZUzV5Ym1Ob2NpNXViRENDQVNJd0RRWUpLb1pJaHZjTkFRRUJCUUFEZ2dFUEFEQ0NBUW9DCmdnRUJBTFJlMXdzekZSb2Rib2pZV05DSHA3UkdJaUVIMENDZ1F2MmdMRXNkUUNKZlcrUFEvVjM0NnQ3bSs3TFEKZXJaV3ZZMWpuY2VuWU5JSGRBU0VnU0ducWExYnhUSU9FaE0zQXpib3B0WDhjSW1OSGZoQlZETGdiTEYzUk0xaQpPM1JLTGdIS2tYSTMxZndjbU9zWGUwaElYQnpUbmxnM20vUzlXL3NTc0l1dDVwNENDUWV3TWlpWFhuUElKb21lCmpkS3VjSHFnMTlzd0YvcGVUalZrcVpuMkJHazZRaWFpMU41bldRV0pjcThTenZxTTViZElDaWlwYU9hWWQ3RFEKYWRTejV5dlF0YkxQNW4wTXpnOU43S3pGcEpvUys5QWdkWDI5cmZqV2JSekp3RzM5R3dRemN6VWtLcnZEb05JaQo0UFJHc01yclFNVXFSYjRSajNQOEJodEMxWXNDQXdFQUFhTTVNRGN3Q1FZRFZSMFRCQUl3QURBTEJnTlZIUThFCkJBTUNCZUF3SFFZRFZSMGxCQll3RkFZSUt3WUJCUVVIQXdJR0NDc0dBUVVGQndNQk1BMEdDU3FHU0liM0RRRUIKQ3dVQUE0SUJBUUNKZm5PWlFLWkowTFliOGNWUW5Vdi9NZkRZVEJIQ0pZcGM4MmgzUGlXWElMQk1jWDhQRC93MgpoOUExNkE4NGNxODJuQXEvaFZYYy9JNG9yaFY5WW9jSEg5UlcvbGthTUQ2VEJVR0Q1U1k4S292MHpHQ1ROaDZ6Ci9wZTNqTC9uU0pYSjRtQm51czJheHFtWnIvM3hhaWpYZG9kMmd3eGVhTklvRjNLbHB2aGU3ZjRBNmpsQTM0MmkKVVlCZ09iN1F5KytRZWd4U1diSmdoSzg1MmUvUUhnU2FVSkN6NW1sNGc1WndnNnBTUXhySUhCNkcvREc4dElSYwprZDMxSk1qY25Fb1Rhc1Jyc1NwVmNGdXZyQXlXN2liakZyYzhienBNcE1obDVwYUZRcEZzMnIwaXpZekhwakFsCk5ZR2I2OHJHcjBwQkp3YU5DS2ErbCtLRTk4M3A3NDYwCi0tLS0tRU5EIENFUlRJRklDQVRFLS0tLS0K
    tls.key: LS0tLS1CRUdJTiBSU0EgUFJJVkFURSBLRVktLS0tLQpNSUlFb3dJQkFBS0NBUUVBdEY3WEN6TVZHaDF1aU5oWTBJZW50RVlpSVFmUUlLQkMvYUFzU3gxQUlsOWI0OUQ5ClhmanEzdWI3c3RCNnRsYTlqV09keDZkZzBnZDBCSVNCSWFlcHJWdkZNZzRTRXpjRE51aW0xZnh3aVkwZCtFRlUKTXVCc3NYZEV6V0k3ZEVvdUFjcVJjamZWL0J5WTZ4ZDdTRWhjSE5PZVdEZWI5TDFiK3hLd2k2M21uZ0lKQjdBeQpLSmRlYzhnbWlaNk4wcTV3ZXFEWDJ6QVgrbDVPTldTcG1mWUVhVHBDSnFMVTNtZFpCWWx5cnhMTytvemx0MGdLCktLbG81cGgzc05CcDFMUG5LOUMxc3MvbWZRek9EMDNzck1Xa21oTDcwQ0IxZmIydCtOWnRITW5BYmYwYkJETnoKTlNRcXU4T2cwaUxnOUVhd3l1dEF4U3BGdmhHUGMvd0dHMExWaXdJREFRQUJBb0lCQUJKYUErOHp4MVhjNEw0egpwUFd5bDdHVDRTMFRLbTNuWUdtRnZudjJBZXg5WDFBU2wzVFVPckZyTnZpK2xYMnYzYUZoSFZDUEN4N1RlMDVxClhPa2JzZnZkZG5iZFQ2RjgyMnJleVByRXNINk9TUnBWSzBmeDVaMDQwVnRFUDJCWm04eTYyNG1QZk1vbDdya2MKcm9Kd09rOEVpUHZZekpsZUd0bTAwUm1sRysyL2c0aWJsOTVmQXpyc1MvcGUyS3ZoN2NBVEtIcVh6MjlpUmZpbApiTGhBamQwcEVSMjNYU0hHR1ZqRmF3amNJK1c2L2RtbDZURDhrSzFGaUtldmJKTlREeVNXQnpPbXRTYUp1K01JCm9iUnVWWG4yZVNoamVGM1BYcHZRMWRhNXdBa0dJQWxOWjRHTG5QU2ZwVmJyU0plU3RrTGNzdEJheVlJS3BWZVgKSVVTTHM0RUNnWUVBMmNnZUE2WHh0TXdFNU5QWlNWdGhzbXRiYi9YYmtsSTdrWHlsdk5zZjFPdXRYVzkybVJneQpHcEhUQ0VubDB0Z1p3T081T1FLNjdFT3JUdDBRWStxMDJzZndwcmgwNFZEVGZhcW5QNTBxa3BmZEJLQWpmanEyCjFoZDZMd2hLeDRxSm9aelp2VkowV0lvR1ZLcjhJSjJOWGRTUVlUanZUZHhGczRTamdqNFFiaEVDZ1lFQTFBWUUKSEo3eVlza2EvS2V2OVVYbmVrSTRvMm5aYjJ1UVZXazRXSHlaY2NRN3VMQVhGY3lJcW5SZnoxczVzN3RMTzJCagozTFZNUVBzazFNY25oTTl4WE4vQ3ZDTys5b2t0RnNaMGJqWFh6NEJ5V2lFNHJPS1lhVEFwcDVsWlpUT3ZVMWNyCm05R3NwMWJoVDVZb2RaZ3IwUHQyYzR4U2krUVlEWnNFb2lFdzNkc0NnWUVBcVJLYWNweWZKSXlMZEJjZ0JycGkKQTRFalVLMWZsSjR3enNjbGFKUDVoM1NjZUFCejQzRU1YT0kvSXAwMFJsY3N6em83N3cyMmpud09mOEJSM0RBMwp6ZTRSWDIydWw4b0hGdldvdUZOTTNOZjNaNExuYXpVc0F0UGhNS2hRWGMrcEFBWGthUDJkZzZ0TU5PazFxaUNHCndvU212a1BVVE84b1ViRTB1NFZ4ZmZFQ2dZQUpPdDNROVNadUlIMFpSSitIV095enlOQTRaUEkvUkhwN0RXS1QKajVFS2Y5VnR1OVMxY1RyOTJLVVhITXlOUTNrSjg2OUZPMnMvWk85OGg5THptQ2hDTjhkOWN6enI5SnJPNUFMTApqWEtBcVFIUlpLTFgrK0ZRcXZVVlE3cTlpaHQyMEZPb3E5OE5SZDMzSGYxUzZUWDNHZ3RWQ21YSml6dDAxQ3ZHCmR4VnVnd0tCZ0M2Mlp0b0RLb3JyT2hvdTBPelprK2YwQS9rNDJBOENiL29VMGpwSzZtdmxEWmNYdUF1QVZTVXIKNXJCZjRVYmdVYndqa1ZWSFR6LzdDb1BWSjUvVUxJWk1Db1RUNFprNTZXWDk4ZE93Q3VTVFpZYnlBbDZNS1BBZApTZEpuVVIraEpnSVFDVGJ4K1dzYnh2d0FkbWErWUhtaVlPRzZhSklXMXdSd1VGOURLUEhHCi0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==
    ```

## 8. Configure FQDN

設定ファイルには `<FQDN>` への参照が2つあります（このステップに1つと、次のステップに1つ）。 両方とも[DNSの設定](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-4-lb/#3-configure-dns)で選択したFQDNと置き換える必要があります。

In the `kind: Ingress` with `name: cattle-ingress-http`：

- `<FQDN>` を[DNSの設定](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-4-lb/#3-configure-dns)で選択したFQDNに置き換えます。

`<FQDN>`を[DNSの構成](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-4-lb/#3-configure-dns)で選択したFQDNに置き換えた後、ファイルは次の例のようになります（`rancher.yourdomain.com`がこの例で使用されているFQDNです）。

```
 ---
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
    tls:
    - secretName: cattle-keys-ingress
      hosts:
      - rancher.yourdomain.com
```

`.yml` ファイルを保存して閉じます。

## 9. Configure Rancher version

置き換える必要がある最後の参照は `<RANCHER_VERSION>` です。
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

`.yml` ファイルを閉じたら、安全な場所にバックアップしてください。
Rancherをアップグレードするときが来たら、このファイルをもう一度使うことができます。

## 11. Run RKE

すべての設定が整ったら、RKEを使用してRancherを起動します。
このアクションを完了するには、`rke up` コマンドを実行し、 `-- config`パラメータを使用して設定ファイルを指定します。

1. ワークステーションから、`rancher-cluster.yml` とダウンロードした `rke` バイナリが同じディレクトリにあることを確認してください。

1. ターミナルインスタンスを開きます。
あなたの設定ファイルと `rke` を含むディレクトリに変更してください。

1. 下記のいずれかの `rke up` コマンドを入力してください。

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

インストール中に、RKEは自動的に `kube_config_rancher-cluster.yml` という名前の設定ファイルをRKEバイナリと同じディレクトリに生成します。
このファイルをコピーして安全な場所にバックアップしてください。
このファイルは後でRancher Serverをアップグレードするときに使用します。

## What’s Next?

いくつかの選択肢があります。

- 惨事が発生した場合に備えて、Rancher Serverのバックアップを作成します。
[高可用性のバックアップと復元](https://rancher.com/docs/rancher/v2.x/en/backups/backups/ha-backups/)
- Kubernetesクラスタを作成します。[Kubernetesクラスタのプロビジョニング](https://rancher.com/docs/rancher/v2.x/en/cluster-provisioning/)

## FAQ and Troubleshooting

### How Do I Know if My Certificates are in PEM Format?

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
### How Can I Encode My PEM Files in base64?

証明書をbase64でエンコードするには

1. PEMファイルが存在する場所にディレクトリを変更します。
1. 次のいずれかのコマンドを実行してください。
`FILENAME` を証明書の名前に置き換えます。
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
コマンドの出力（以下のコマンド例を参照）が `Verify return code: 0 (ok)` で終わっていれば、証明書チェーンは有効です。
`ca.pem` ファイルは、`rancher/rancher` のコンテナーに追加したものと同じでなければなりません。
認識された認証局によって署名された証明書を使用するときは、`-CAfile` パラメーターを省略することができます。

**Command:**

```
openssl s_client -CAfile ca.pem -connect rancher.yourdomain.com:443 -servername rancher.yourdomain.com
...
    Verify return code: 0 (ok)
```

