# Single Node Install

開発およびテスト環境では、単一のDockerコンテナを実行してRancherをインストールすることをお勧めします。
このインストールシナリオでは、単一のLinuxホストにDockerをインストールしてから、単一のDockerコンテナを使用して自分のホストにRancherをデプロイします。

> 外部ロードバランサーを使用したいですか？  
> 代わりに、[外部ロードバランサによる単一ノードインストール](https://rancher.com/docs/rancher/v2.x/en/installation/single-node/single-node-install-external-lb/)を参照してください。

## 1. Provision Linux Host

Rancher Serverを起動するには、[要件](./0200noderequirements.md)に従って単一のLinuxホストをプロビジョニングしてください。

## 2. Choose an SSL Option and Install Rancher

セキュリティ上の理由から、Rancherを使用する場合はSSL（Secure Sockets Layer）が必要です。
SSLは、あなたがログインしたり、クラスタとやり取りするときのように、すべてのRancherネットワーク通信を保護します。

> あなたはですか…  
> - プロキシを使用しますか？ [HTTPプロキシ設定](https://rancher.com/docs/rancher/v2.x/en/installation/single-node/proxy/)を参照してください。
> - サービスにアクセスするためのカスタムCAルート証明書を設定しますか？ [カスタムCAルート証明書](https://rancher.com/docs/rancher/v2.x/en/admin-settings/custom-ca-root-certificate/)を参照してください。
> - エアギャップの取り付けを完了しますか？ [Air Gap：シングルノードインストール](https://rancher.com/docs/rancher/v2.x/en/installation/air-gap-single-node/)を参照してください。
> - Rancher APIですべての取引を記録しますか？ [API監査]()を参照してください。

以下のオプションから選択してください。

### オプションA  - デフォルトの自己署名証明書

本人確認が問題にならない開発環境またはテスト環境にRancherをインストールする場合は、生成された自己署名証明書を使用してRancherをインストールしてください。
このインストールオプションでは、証明書を自分で生成する手間が省けます。

Linuxホストにログインしてから、以下の最小インストールコマンドを実行します。

```
docker run -d --restart=unless-stopped \
-p 80:80 -p 443:443 \
rancher/rancher:latest
```

### オプションB  - 自分の証明書を持参する：自己署名

チームがRancherサーバーにアクセスする開発環境またはテスト環境では、チームがRancherのインスタンスに接続していることをチームが確認できるように、インストールで使用する自己署名証明書を作成します。

> 前提条件：  
> OpenSSLまたは他の方法を使用して自己署名証明書を作成します。
> - 証明書ファイルは[PEM形式](https://rancher.com/docs/rancher/v2.x/en/installation/single-node/#pem)でなければなりません。
> - 証明書ファイルで、すべての中間証明書をチェーンに含めます。
最初にあなたの証明書と一緒にあなたの証明書を注文し、続いて中間物を注文します。
例として、 [SSL FAQ/Troubleshooting](https://rancher.com/docs/rancher/v2.x/en/installation/single-node/#cert-order) を見てください。

証明書を作成したら、以下のDockerコマンドを実行してRancherをインストールします。
-vフラグを使用して、証明書をコンテナにマウントするための証明書へのパスを指定します。

- `<CERT_DIRECTORY>` を証明書ファイルへのディレクトリパスに置き換えます。
- `<FULL_CHAIN.pem>` 、`<PRIVATE_KEY.pem>`、および `<CA_CERTS>` を自分の証明書の名前に置き替えます。

```
docker run -d --restart=unless-stopped \
	-p 80:80 -p 443:443 \
	-v /<CERT_DIRECTORY>/<FULL_CHAIN.pem>:/etc/rancher/ssl/cert.pem \
	-v /<CERT_DIRECTORY>/<PRIVATE_KEY.pem>:/etc/rancher/ssl/key.pem \
	-v /<CERT_DIRECTORY>/<CA_CERTS.pem>:/etc/rancher/ssl/cacerts.pem \
	rancher/rancher:latest
```

### オプションC  - 自分の証明書を持参する：承認されたCAによって署名された

アプリを公開している本番環境では、ユーザーベースがセキュリティの警告に遭遇しないように、認定されたCAによって署名された証明書を使用してください。

> 前提条件：  
> 証明書ファイルは[PEM形式](https://rancher.com/docs/rancher/v2.x/en/installation/single-node/#pem)でなければなりません。 

証明書を取得したら、下記のDockerコマンドを実行してください。

- `-v` フラグを使用して、証明書をコンテナにマウントするための証明書へのパスを指定します。
証明書は認識されたCAによって署名されているため、追加のCA証明書ファイルをマウントする必要はありません。
    - `<CERT_DIRECTORY>` を証明書ファイルへのディレクトリパスに置き換えます。 
    - `<FULL_CHAIN.pem>` と `<PRIVATE_KEY.pem>` を自分の証明書の名前に置き換えます。
- Rancherによって生成されたデフォルトのCA証明書を無効にするには、コンテナーへの引数として `--no-cacerts` を使用します。

```
docker run -d --restart=unless-stopped \
	-p 80:80 -p 443:443 \
	-v /<CERT_DIRECTORY>/<FULL_CHAIN.pem>:/etc/rancher/ssl/cert.pem \
	-v /<CERT_DIRECTORY>/<PRIVATE_KEY.pem>:/etc/rancher/ssl/key.pem \
	rancher/rancher:latest --no-cacerts
```

### オプションD  - 証明書を暗号化しましょう

実稼働環境では、[Let's Encrypt](https://letsencrypt.org/)証明書を使用することもできます。
Encryptがあなたのドメインを管理していることを確認するためにhttp-01チャレンジを使用しましょう。 
Rancherアクセスに使用するホスト名（たとえば、 `rancher.mydomain.com` ）を実行しているマシンのIPにポイントすることで、ドメインを制御していることを確認できます。
DNSにAレコードを作成することで、ホスト名をIPアドレスにバインドできます。

> 前提条件：
> - 暗号化はインターネットサービスです。
したがって、このオプションは内部/エアギャップネットワークでは使用できません。
> - DNSに、あなたのLinuxホストのIPアドレスをRancherアクセスに使用したいホスト名（例えば、 `rancher.mydomain.com` ）に結び付けるレコードを作成します。
> - Linuxホストで `TCP/80` ポートを開きます。 http-01を暗号化してみましょうチャレンジはどの送信元IPアドレスからでも発生する可能性があるため、ポート `TCP/80` はすべてのIPアドレスに対して開かれている必要があります。

前提条件を満たしたら、次のコマンドを実行して、Let's Encrypt証明書を使用してRancherをインストールできます。
`<YOUR.DNS.NAME>` をあなたのドメインに置き換えます。

```
docker run -d --restart=unless-stopped \
  -p 80:80 -p 443:443 \
  rancher/rancher:latest \
  --acme-domain <YOUR.DNS.NAME>
```

> 覚えておいてください：  
> 暗号化して、新しい証明書を要求するためのレート制限を提供しましょう。
したがって、コンテナを作成または破棄する頻度を制限してください。
詳しくは、[レート制限に関するドキュメントを暗号化する](https://letsencrypt.org/docs/rate-limits/)をご覧ください。


## What’s Next?

- 推奨：[単一ノードのバックアップと復元](https://rancher.com/docs/rancher/v2.x/en/backups/backups/single-node-backups/)を確認してください。
今すぐバックアップする必要のあるデータはありませんが、通常のRancherの使用後にバックアップを作成することをお勧めします。
- Kubernetesクラスタを作成します。
[Kubernetesクラスタのプロビジョニング](https://rancher.com/docs/rancher/v2.x/en/cluster-provisioning/)

## FAQ and Troubleshooting

### 自分の証明書がPEM形式かどうかはどうすればわかりますか。

次のような特徴によってPEM形式を認識できます。

- ファイルは次のヘッダで始まります。  
`-----BEGIN CERTIFICATE-----`
- ヘッダーの後には長い文字列が続きます。 本当に長い。
- ファイルはフッターで終わります。  
`-----END CERTIFICATE-----`

PEM証明書の例：
```
----BEGIN CERTIFICATE-----
MIIGVDCCBDygAwIBAgIJAMiIrEm29kRLMA0GCSqGSIb3DQEBCwUAMHkxCzAJBgNV
... more lines
VWQqljhfacYPgp8KJUJENQ9h5hZ2nSCrI+W00Jcw4QcEdCI8HL5wmg==
-----END CERTIFICATE-----
```

PEM証明書キーの例：
```
-----BEGIN RSA PRIVATE KEY-----
MIIGVDCCBDygAwIBAgIJAMiIrEm29kRLMA0GCSqGSIb3DQEBCwUAMHkxCzAJBgNV
... more lines
VWQqljhfacYPgp8KJUJENQ9h5hZ2nSCrI+W00Jcw4QcEdCI8HL5wmg==
-----END RSA PRIVATE KEY-----
```

鍵が次の例のようになっている場合は、[証明書の鍵をPKCS8からPKCS1に変換する方法はありますか](https://rancher.com/docs/rancher/v2.x/en/installation/single-node/#convert-pkcs8)を参照してください。

```
-----BEGIN PRIVATE KEY-----
MIIGVDCCBDygAwIBAgIJAMiIrEm29kRLMA0GCSqGSIb3DQEBCwUAMHkxCzAJBgNV
... more lines
VWQqljhfacYPgp8KJUJENQ9h5hZ2nSCrI+W00Jcw4QcEdCI8HL5wmg==
-----END PRIVATE KEY-----
```

### 証明書の鍵をPKCS8からPKCS1に変換する方法はありますか？

PKCS8証明書キーファイルを使用している場合、Rancherは次の行をログに記録します。

```
ListenConfigController cli-config [listener] failed with : failed to read private key: asn1: structure error: tags don't match (2 vs {class:0 tag:16 length:13 isCompound:true})
```

これを機能させるには、以下のコマンドを使用してキーをPKCS8からPKCS1に変換する必要があります。

```
openssl rsa -in key.pem -out convertedkey.pem
```

Rancher用の証明書キーファイルとして `convertkey.pem` を使用できるようになりました。

### 私が中級者を追加したい場合の証明書の順序は何ですか？

証明書を追加する順序は次のとおりです。

```
-----BEGIN CERTIFICATE-----
%YOUR_CERTIFICATE%
-----END CERTIFICATE-----
-----BEGIN CERTIFICATE-----
%YOUR_INTERMEDIATE_CERTIFICATE%
-----END CERTIFICATE-----
```

### 証明書チェーンを検証する方法

`openssl` バイナリを使用して証明書チェーンを検証できます。
コマンドの出力（以下のコマンド例を参照）が `Verify return code: 0 (ok)` で終わっていれば、証明書チェーンは有効です。
`ca.pem` ファイルは、`rancher/rancher` のコンテナーに追加したものと同じでなければなりません。
認識された認証局によって署名された証明書を使用するときは、`-CAfile` パラメーターを省略することができます。

Command:
```
openssl s_client -CAfile ca.pem -connect rancher.yourdomain.com:443
...
    Verify return code: 0 (ok)
```

## Advanced Options

### Enable API Audit Log

Enable API Audit Log には、Rancherサーバーを介して行われたすべてのユーザーとシステムのトランザクションが記録されます。

Enable API Audit Log は、デフォルトではRancherコンテナ内の `/var/log/auditlog` に書き込まれます。
そのディレクトリをボリュームとして共有し、`AUDIT_LEVEL` を設定してログを有効にします。

詳細およびオプションについては、Enable API Audit Log を参照してください。

```
docker run -d --restart=unless-stopped \
  -p 80:80 -p 443:443 \
  -v /var/log/rancher/auditlog:/var/log/auditlog \
  -e AUDIT_LEVEL=1 \
  rancher/rancher:latest
```

### Air Gap

エアギャップインストールを完了するためにこのページにアクセスしている場合は、選択したオプションでインストールコマンドを実行するときに、プライベートレジストリURLをサーバタグの先頭に追加する必要があります。
`<REGISTRY.DOMAIN.COM：PORT>` をあなたの個人用レジストリURLと一緒に `rancher/rancher:latest` の前に追加してください。

例：
```
<REGISTRY.DOMAIN.COM:PORT>/rancher/rancher:latest
```

### 永続データ

Rancherはデータストアとしてetcdを使用します。
シングルノードインストールを使用している場合は、組み込みetcdが使用されています。
永続データは、コンテナー内の次のパスにあります： `/var/lib/rancher`。
ホストボリュームをこの場所にバインドマウントして、それが実行されているホスト上のデータを保持することができます。
RancherOSを使用する場合は、データを保存するために使用できる[永続的な保存ディレクトリ](https://rancher.com/docs/os/v1.x/en/installation/system-services/system-docker-volumes/#user-volumes)を確認してください。

Command:
```
docker run -d --restart=unless-stopped \
  -p 80:80 -p 443:443 \
  -v /opt/rancher:/var/lib/rancher \
  rancher/rancher:latest
```

### 同一ノード上で `rancher/rancher` と `rancher/rancher-agent` を実行中

単一のノードを使用してRancherを実行し、同じノードをクラスターに追加できるようにする場合は、`rancher/rancher` コンテナーにマップされているホストポートを調整する必要があります。

ノードがクラスタに追加されると、ポート80と443を使用するnginx入力コントローラがデプロイされます。
これは、`rancher/rancher` コンテナに公開することをお勧めするデフォルトのポートと競合します。

この設定は本番用にはお勧めできませんが、開発/デモ目的には便利な場合があります。

ホストポートマッピングを変更するには、次の部分 `-p 80:80 -p 443:443` を `-p 8080:80 -p 8443:443` に置き換えます。

```
docker run -d --restart=unless-stopped \
  -p 8080:80 -p 8443:443 \
  rancher/rancher:latest
```

