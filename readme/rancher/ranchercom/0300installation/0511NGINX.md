# NGINX

NGINXは、接続をRancherノードの1つに転送するLayer 4 load balancer（TCP）として設定されます。

> **Note:**  
> この構成では、ロードバランサーはノードの前に配置されています。
> ロードバランサは、NGINXを実行できる任意のホストにすることができます。
> 
> 注意点：Rancherノードをロードバランサーとして使用しないでください。

## Install NGINX(インストール NGINX)

ロードバランサーとして使用したいノードにNGINXをインストールすることから始めます。
NGINXはすべての既知のオペレーティングシステムに利用可能なパッケージを持っています。
テストされたバージョンは`1.14`と`1.15`です。
NGINXのインストールについては、それらの[インストールドキュメント](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/)を参照してください。

`stream` モジュールが必要です。
これは公式のNGINXパッケージを使用するときに存在します。
オペレーティングシステムに NGINX `stream` モジュールをインストールして有効にする方法については、OSのマニュアルを参照してください。

## Create NGINX Configuration(NGINXの設定)

NGINXをインストールしたら、NGINX設定ファイル `nginx.conf` を自分のノードのIPアドレスで更新する必要があります。

1. 以下のコードサンプルをコピーして、お気に入りのテキストエディタに貼り付けます。
`nginx.conf` として保存します。

1. `nginx.conf` から、`<IP_NODE_1>`、`<IP_NODE_2>`、および `<IP_NODE_3>` の両方の発生（ポート80とポート443）を自分の[ノード](https://rancher.com/docs/rancher/v2.x/en/installation/ha/create-nodes-lb/)のIPに置き換えます。

    > **Note：**  
    > すべての設定オプションについては [NGINX Documentation：TCPとUDPの負荷分散](https://docs.nginx.com/nginx/admin-guide/load-balancer/tcp-udp-load-balancer/)を参照してください。

    NGINX設定の例

    ```
    worker_processes 4;
    worker_rlimit_nofile 40000;

    events {
        worker_connections 8192;
    }

    stream {
        upstream rancher_servers_http {
            least_conn;
            server <IP_NODE_1>:80 max_fails=3 fail_timeout=5s;
            server <IP_NODE_2>:80 max_fails=3 fail_timeout=5s;
            server <IP_NODE_3>:80 max_fails=3 fail_timeout=5s;
        }
        server {
            listen     80;
            proxy_pass rancher_servers_http;
        }

        upstream rancher_servers_https {
            least_conn;
            server <IP_NODE_1>:443 max_fails=3 fail_timeout=5s;
            server <IP_NODE_2>:443 max_fails=3 fail_timeout=5s;
            server <IP_NODE_3>:443 max_fails=3 fail_timeout=5s;
        }
        server {
            listen     443;
            proxy_pass rancher_servers_https;
        }
    }
    ```

1. Path： `/etc/nginx/nginx.conf` の `nginx.conf` をロードバランサーに保存します。

1. 次のコマンドを実行して、NGINX設定にアップデートをロードします。
    ```
    # nginx -s reload
    ```

## Option - Run NGINX as Docker container(オプション -  DockerコンテナーとしてNGINXを実行する)

NGINXをパッケージとしてオペレーティングシステムにインストールする代わりに、Dockerコンテナとして実行することができます。
編集したサンプルNGINX設定を/etc/nginx.confとして保存し、次のコマンドを実行してNGINXコンテナを起動します。

```
docker run -d --restart=unless-stopped \
  -p 80:80 -p 443:443 \
  -v /etc/nginx.conf:/etc/nginx/nginx.conf \
  nginx:1.14
```

