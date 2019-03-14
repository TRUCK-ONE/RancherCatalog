# Ingress Controllers

デフォルトでは、RKEはすべてのスケジュール可能なノードにNGINX入力コントローラをデプロイします。

> **Note：**  
> v0.1.8の時点では、作業者のみがスケジュール可能ノードと見なされていましたが、v0.1.8より前のバージョンでは、作業者ノードとコントロールプレーンノードはスケジュール可能ノードと見なされていました。

RKEは、`hostnetwork:true` を使用して入力コントローラをDaemonSetとして配置します。
そのため、コントローラが配置されている各ノードでポート `80`、`443` が開かれます。

入力コントローラに使用されるイメージは `system_images`[ディレクティブ](https://rancher.com/docs/rke/v0.1.x/en/config-options/system-images/)の下にあります。
Kubernetesのバージョンごとに、入力コントローラに関連付けられているデフォルトのイメージがありますが、`system_images` のimageタグを変更することでこれらをオーバーライドできます。

## Scheduling Ingress Controllers

特定のノードにイングレスコントローラのみをデプロイしたい場合は、イングレスに `node_selector` を設定できます。
`node_selector` のラベルは、展開される入力コントローラのノードのラベルと一致する必要があります。

```
nodes:
    - address: 1.1.1.1
      role: [controlplane,worker,etcd]
      user: root
      labels:
        app: ingress

ingress:
    provider: nginx
    node_selector:
      app: ingress
```

## Disabling the Default Ingress Controller

クラスタ構成のingress `provider` ディレクティブにnoneを指定すると、デフォルトのコントローラを `none` にできます。

```
ingress:
    provider: none
```

## Configuring NGINX Ingress Controller

NGINXの設定のために、Kubernetesで利用可能な設定オプションがあります。
[NGINX設定マップ](https://github.com/kubernetes/ingress-nginx/blob/master/docs/user-guide/nginx-configuration/configmap.md)、[コマンドラインextra_args](https://github.com/kubernetes/ingress-nginx/blob/master/docs/user-guide/cli-arguments.md)、および[注釈](https://kubernetes.github.io/ingress-nginx/user-guide/nginx-configuration/annotations/)のためのオプションのリストがあります。

```
ingress:
    provider: nginx
    options:
      map-hash-bucket-size: "128"
      ssl-protocols: SSLv2
    extra_args:
      enable-ssl-passthrough: ""
```

## Configuring an NGINX Default Certificate

TLS終了を使用して入力オブジェクトを構成するときは、暗号化/復号化に使用される証明書をそれに提供する必要があります。
入力を設定するたびに証明書を明示的に定義する代わりに、デフォルトで使用されるカスタム証明書を設定できます。

デフォルトの証明書を設定することは、証明書が複数のサブドメインに適用される可能性があるため、ワイルドカード証明書が使用される環境で特に役立ちます。

> **前提条件：**  
> - クラスタを作成するために使用された `cluster.yml` へのアクセス
> - デフォルトの証明書として使用するPEMエンコード証明書

1. PEMエンコード形式で証明書の鍵ペアを取得または生成します。
1. 次のコマンドを使用して、PEMでエンコードされた証明書からKubernetesシークレットを生成し、 `mycert.cert` および `mycert.key` を自分の証明書に置き換えます。
    ```
    kubectl -n ingress-nginx create secret tls ingress-default-cert --cert=mycert.cert --key=mycert.key -o yaml --dry-run=true > ingress-default-cert.yaml
    ```
1. RKE `cluster.yml` ファイルに `ingress-default-cert.yml` の内容をインラインで含めます。 例えば：
    ```
    addons: |-
      ---
    apiVersion: v1
    data:
        tls.crt: [ENCODED CERT]
        tls.key: [ENCODED KEY]
    kind: Secret
    metadata:
        creationTimestamp: null
        name: ingress-default-cert
        namespace: ingress-nginx
    type: kubernetes.io/tls
    ```
1. 以下の `default-ssl-certificate` 引数であなたの入力リソースを定義してください。
これはあなたの `cluster.yml` の `extra_args` の下で先に作成した秘密を参照します：
    ```
    ingress: 
      provider: "nginx"
    extra_args:
        default-ssl-certificate: "ingress-nginx/ingress-default-cert"
    ```
1. **オプション：** 既存のクラスター内の入力にデフォルト証明書を適用する場合は、Kubernetesが新しく構成された `extra_args` を使用して新しいポッドをスケジュールするように、NGINX入力コントローラーポッドを削除する必要があります。
    ```
    kubectl delete pod -l app=ingress-nginx -n ingress-nginx
    ```



