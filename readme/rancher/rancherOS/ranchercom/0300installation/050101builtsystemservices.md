# Built-in System Services

RancherOSを起動するために、システムサービスが組み込まれています。
それらは[Docker Compose](https://docs.docker.com/compose/compose-file/) フォーマットで定義されており、デフォルトのシステム設定ファイル `/usr/share/ros/os-config.yml` にあります。
[独自のシステムサービスを追加](https://rancher.com/docs/os/v1.x/en/installation/system-services/adding-system-services/)したり、cloud-configでサービスを上書きしたりできます。

## preload-user-images

[イメージのプリロード](https://rancher.com/docs/os/v1.x/en/installation/boot-process/image-preloading/)についてもっと読んでください。

## network

このサービス中に、ネットワーキングが設定されます。
ホスト名、インターフェイス、およびDNS。

それは[cloud-config](https://rancher.com/docs/os/v1.x/en/installation/configuration/#cloud-config)の `hostname` と `rancher.networksettings` によって設定されます。

## ntp

System Dockerコンテナー内で `ntpd` を実行します。

## console

このサービスは `sshd` と `getty` を実行することによってRancherOSユーザーインターフェースを提供します。
起動時にRancherOSの設定は完了です。

1. `rancher.password=<password>` カーネルパラメータが存在する場合は、`<password>` を `rancher` ユーザーのパスワードとして設定します。

1. ホストSSHキーがない場合は、ホストSSHキーを生成し、それらを [cloud-config](https://rancher.com/docs/os/v1.x/en/installation/configuration/#cloud-config)の `rancher.ssh.keys` に保存します。

1. `cloud-init -execute`を実行します。
これにより、次の処理が行われます。
    - `/home/rancher` および `/home/docker` 内の `.ssh/authorized_keys` を [cloud-config](https://rancher.com/docs/os/v1.x/en/installation/configuration/ssh-keys/)およびメタデータから更新します。
    - `write_files` [cloud-config](https://rancher.com/docs/os/v1.x/en/installation/configuration/resizing-device-partition/) 設定で指定されたファイルを書き込みます。
    - `rancher.resize_device` [cloud-config](https://rancher.com/docs/os/v1.x/en/installation/configuration/resizing-device-partition/)設定で指定されたデバイスのサイズを変更します。
    - `mounts` [cloud-config](https://rancher.com/docs/os/v1.x/en/installation/storage/additional-mounts/) 設定で指定されたデバイスをマウントします。
    - `rancher.sysctl` [cloud-config](https://rancher.com/docs/os/v1.x/en/installation/configuration/sysctl/) 設定で指定されたsysctlパラメータを設定します。

1. user-dataに `#!` で始まるファイルが含まれている場合、そのファイルはcloud-init中に `/var/lib/rancher/conf/cloud-config-script` に保存されてから実行されます。
エラーは無視されます。

1. `/opt/rancher/bin/start.sh` が存在し、実行可能であれば実行します。
エラーは無視されます。

1. `/etc/rc.local` が存在し実行可能な場合は実行します。
エラーは無視されます。

## docker

このシステムサービスはユーザーdockerデーモンを実行します。
通常これは、コンソールシステムコンテナ内で`docker-init` スクリプトを実行して実行されます。
これは、`/opt/bin`、`/usr/local/bin`、および`/usr/bin` でdockerバイナリを探し、最初に見つかったディレクトリをdockerバイナリで追加します。
PATHを実行し、`dockerlaunch docker daemon` を実行して、渡された引数を追加します。

Dockerデーモンの引数は、`rancher.docker.args`  cloud-configプロパティー（続いて `rancher.docker.extra_args`）から読み取られます。



