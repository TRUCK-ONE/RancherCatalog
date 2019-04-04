# Configuration

RancherOSを構成する方法は2つあります。

1. 最初にRancherOSを起動するときにcloud-configファイルを使用して設定を提供できます。
1. `ros config` コマンドを使用して手動で設定を変更します。

通常、最初にサーバーを起動するときに、cloud-configファイルを渡してサーバーの初期化を設定します。
初回起動後、設定に変更がある場合は、`ros config` を使用して必要な設定プロパティを設定することをお勧めします。
変更はすべてディスクに保存され、変更を適用するには再起動が必要になります。

## Cloud-Config

Cloud-configは、多くのLinuxディストリビューションでサポートされている宣言型の設定ファイルフォーマットで、RancherOSの主要な設定メカニズムです。

cloud-configをサポートするLinux OSは、起動中にcloud-initプロセスを呼び出してcloud-configファイルを解析し、オペレーティングシステムを設定します。
RancherOSは、システムコンテナ内で独自のcloud-initプロセスを実行します。
cloud-initプロセスは、さまざまなデータソースからcloud-configファイルを取得しようとします。
cloud-initがcloud-configファイルを取得すると、cloud-configファイルの内容に従ってLinux OSを設定します。

たとえば、AWSでRancherOSインスタンスを作成するときに、オプションで `user-data` フィールドに渡されるcloud-configを指定できます。
RancherOSインスタンスの内部では、cloud-initプロセスがAWSのcloud-configデータソースを介してcloud-configコンテンツを取得します。
これは単にVMインスタンスが受信したユーザーデータのコンテンツを抽出するためです。 ファイルが"`#cloud-config`"で始まる場合、cloud-initはそのファイルをcloud-configファイルとして解釈します。
ファイルが `#!<interpreter>` で始まる場合（たとえば `#!/bin/sh`）、cloud-initは単にそのファイルを実行します。
スクリプトにファイル内の任意の設定コマンドを配置できます。

cloud-configファイルはYAMLフォーマットを使います。
YAMLは理解しやすく解析しやすいです。
YAMLの詳細については、[YAMLのサイト](https://yaml.org/start.html)でもっと読んでください。
最も重要なフォーマットの原則は、インデントまたは空白です。
このインデントは、アイテム間の関係を示します。
前の行よりもインデントが大きくなっている場合、インデントが少なくなっているのは一番上のアイテムのサブアイテムです。

例：両方が `ssh_authorized_keys` の下にインデントされている様子に注目してください。

```
#cloud-config
ssh_authorized_keys:
  - ssh-rsa AAA...ZZZ example1@rancher
  - ssh-rsa BBB...ZZZ example2@rancher
```

上記の例では、cloud-configファイルであることを示すために `#cloud-config` 行があります。
最上位のプロパティとして `ssh_authorized_keys` が1つあります。
その値は、`ssh_authorized_keys:` の下の破線リストとして表されている公開鍵のリストです。

## Manually Changing Configuration(手動による設定変更)

起動後にRancherOS設定を更新するには、`ros config set <key> <value>` コマンドを使用できます。
[sysctl設定](https://rancher.com/docs/os/v1.x/en/installation/configuration/sysctl/)のようなもっと複雑な設定のために、小さなYAMLファイルを作成してから `sudo ros config merge -i <your yaml file>`を実行することもできます。

### Getting Values(値の取得)

`/var/lib/rancher/conf/cloud-config.yml` ファイルに設定されている任意の値を簡単に取得できます。
システムのDNS設定を取得するのがどれほど簡単かを見てみましょう。

```
$ sudo ros config get rancher.network.dns.nameservers
- 8.8.8.8
- 8.8.4.4
```

### Setting Values(値の設定)

`/var/lib/rancher/conf/cloud-config.yml` ファイルで値を設定できます。

`/var/lib/rancher/conf/cloud-config.yml` に簡単な値を設定する

```
$ sudo ros config set rancher.docker.tls true
```

`/var/lib/rancher/conf/cloud-config.yml` にリストを設定する

```
$ sudo ros config set rancher.network.dns.nameservers "['8.8.8.8','8.8.4.4']"
```

### Exporting the Current Configuration(設定のエクスポート)

現在の設定状態を出力して確認するには、`ros config export` コマンドを使用できます。

```
$ sudo ros config export
rancher:
  docker:
    tls: true
  network:
    dns:
      nameservers:
      - 8.8.8.8
      - 8.8.4.4
```

### Validating a Configuration File(設定ファイルの検証)

構成ファイルを検証するには、`ros config validate` コマンドを使用できます。

```
$ sudo ros config validate -i cloud-config.yml
```


