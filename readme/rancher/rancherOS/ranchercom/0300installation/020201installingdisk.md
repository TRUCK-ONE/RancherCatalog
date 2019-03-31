# Installing to Disk

RancherOSには、特定のターゲットディスクにRancherOSをインストールする簡単なインストーラが付属しています。
新しいディスクにRancherOSをインストールするには、`ros install` コマンドを使用できます。
インストールする前に、[RancherOSをISOから起動](https://rancher.com/docs/os/v1.x/en/installation/running-rancheros/workstation/boot-from-iso/)しておく必要があります。
必ずリリース[ページ](https://github.com/rancher/os/releases)から `rancheros.iso` を選択してください。

## Using `ros install` to Install RancherOS

`ros install` コマンドは、`rancher/os` コンテナからのインストールを調整します。
あなたはすでにcloud-configファイルを作成してターゲットディスクを見つける必要があるでしょう。

### Cloud-Config

最も簡単なログイン方法はあなたの公開SSHキーを含む `cloud-config.yml` ファイルを渡すことです。
cloud-configでサポートされている機能の詳細については、当社の[ドキュメント](https://rancher.com/docs/os/v1.x/en/installation/configuration/#cloud-config)をご覧ください。

`ros install` コマンドは、`-c` フラグで指定された `cloud-config.yml` ファイルを処理します。
このファイルもディスクに置かれ、`/var/lib/rancher/conf/` にインストールされます。
起動ごとに評価されます。

SSHキーを使用してcloud-configファイルを作成します。
これにより、rancherユーザーとしてボックスにSSHでアクセスできます。
ymlファイルは次のようになります。

```
#cloud-config
ssh_authorized_keys:
  - ssh-rsa AAA...
```

この記事＜[オリジナル](https://help.github.com/en/articles/connecting-to-github-with-ssh)／[翻訳](../../../github/articles/GithubwithSSH/010000GitHubwithSSH.md)＞に従って、`cloud-config.yml` ファイル用の新しいSSHキーを生成できます。

ディスクにインストールする前に、公開SSH鍵をRancherOSにコピーしてください。

これで `cloud-config.yml` にSSHの公開鍵が含まれたので、RancherOSのディスクへのインストールに移ります

```
$ sudo ros install -c cloud-config.yml -d /dev/sda
INFO[0000] No install type specified...defaulting to generic
Installing from rancher/os:v0.5.0
Continue [y/N]:
```

`cloud-config.yml` ファイルには、リモートURLも指定できますが、確実に取得できるようにする必要があります：

```
$ sudo ros install -c https://link/to/cloud-config.yml
```

続行するかどうかを確認するように求められます。
**y**を入力してください。

```
Unable to find image 'rancher/os:v0.5.0' locally
v0.5.0: Pulling from rancher/os
...
...
...
Status: Downloaded newer image for rancher/os:v0.5.0
+ DEVICE=/dev/sda
...
...
...
+ umount /mnt/new_img
Continue with reboot [y/N]:
```

RancherOSをディスクにインストールした後、あなたは `rancher` ユーザーとして自動的にログインすることはもうありません。
あなたの[cloud-configファイル](https://rancher.com/docs/os/v1.x/en/installation/configuration/#cloud-config)内にSSHキーを追加しておく必要があります。

### Installing a Different Version(異なるバージョンのインストール)

デフォルトでは、`ros install` は、実行元のISOと同じインストーライメージバージョンを使用します。
`-i` オプションは、インストール元の特定のイメージを指定します。
ISOをできるだけ小さくするために、インストーライメージはDockerHubからダウンロードされてSystem Dockerで使用されます。
たとえば、RancherOS v0.5.0の場合、デフォルトのインストーライメージは `rancher/os:v0.5.0` になります。

利用可能なRancherOSイメージ/バージョンのリストを見つけるために `ros os list` コマンドを使用することができます。

```
$ sudo ros os list
rancher/os:v0.4.0 remote
rancher/os:v0.4.1 remote
rancher/os:v0.4.2 remote
rancher/os:v0.4.3 remote
rancher/os:v0.4.4 remote
rancher/os:v0.4.5 remote
rancher/os:v0.5.0 remote
```

または、インストーラーイメージをSystem Docker内の任意のイメージに設定してRancherOSをインストールすることもできます。
これは、インターネットに直接アクセスできないマシンにとって特に便利です。

### SSH into RancherOS(RancherOSへのSSH)

RancherOSをインストールした後、あなたはあなたの秘密鍵と **rancher** ユーザーを使ってRancherOSにsshすることができます。

```
$ ssh -i /path/to/private/key rancher@<ip-address>
```

### Installing with no Internet Access(インターネットにアクセスできないインストール)

インターネットにアクセスできないマシンにRancherOSをインストールする場合は、自分の個人用レジストリ、または他の手段でマシンのSystem DockerにDockerイメージを配布することを想定しています。
プライベートレジストリの作成について支援が必要な場合は、[プライベートレジストリについてDockerのドキュメント](https://docs.docker.com/registry/)を参照してください。

インストールコマンド（すなわち `sudo ros install`）には、インストールする特定のイメージを渡すオプションがあります。
このイメージがSystem Dockerで利用可能である限り、RancherOSはそのイメージを使用してRancherOSをインストールします。

```
$ sudo ros install -c cloud-config.yml -d /dev/sda -i <Image_Name_in_System_Docker>
INFO[0000] No install type specified...defaulting to generic
Installing from <Image_Name_in_System_Docker>
Continue [y/N]:
```


