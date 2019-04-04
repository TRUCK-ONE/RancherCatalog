# Kernel boot parameters

RancherOSは、Linuxカーネルのブートcmdlineを解析して、理解できるすべてのキーを構成に追加します。
これにより、起動時にどのcloud-initソースを使用するかを変更したり、`rancher.debug` ロギングを有効にしたり、その他のほとんどすべての設定を変更することができます。

永続的カーネルパラメータを設定または変更するには、インプレース（ファイルの編集と再起動）またはディスクへのインストール中の2つの方法があります。

## In-place editing
v1.1から利用可能

すでにインストールされているRancherOSシステムのカーネルブートパラメータを編集するには、新しい `sudo ros config syslinux` 編集コマンドを使用します（`vi`を使用します）。

> この設定を有効にするには、再起動する必要があります。

v1.0の場合

インプレース編集の場合は、カーネルパラメータを含む `/boot/global.cfg` ファイルにアクセスするために、エディタとマウントを使用してコンテナを実行する必要があります。

> この設定を有効にするには、再起動する必要があります。

```
$ sudo system-docker run --rm -it -v /:/host alpine vi /host/boot/global.cfg
```

## During installation

[RancherOSをディスクにインストールするとき](https://rancher.com/docs/os/v1.x/en/installation/running-rancheros/server/install-to-disk/)に追加のカーネルパラメータを設定したい場合は、`--append` パラメータを使用してください。

```
$ sudo ros install -d /dev/sda --append "rancheros.autologin=tty1"
```

## Graphical boot screen
v1.1から利用可能

RancherOS v1.1.0では、Syslinuxブートメニューが追加されました。
これにより、ブートパラメータを一時的に編集したり、"Debug logging"、"Autologin"、"Debug logging＆Autologin"と"Recovery Console"の両方を選択できます。

デスクトップシステムでは、`global.cfg` の新しい行に `UI vesamenu.c32` を追加することでSyslinuxブートメニューをグラフィカルモードに切り替えることができます（ファイルを編集するには `sudo ros config syslinux` を使用してください）。

## Useful RancherOS cloud-init or boot settings

### Recovery console

`rancher.recovery=true` は、ブートプロセスと同じくらい簡単に、ネットワークや永続的なファイルシステムがマウントされていない状態でシングルユーザー`ルート`bashセッションを開始します。
これはディスクの問題を解決したり、システムをデバッグするために使用することができます。

### Enable/Disable sshd

`rancher.ssh.daemon=false`（os-configで有効）を使用すると、sshdデーモンなしでRancherOSを起動できます。
これはあなたのシステムが待機しているポートをさらに減らすために使うことができます。

### Enable debug logging

`rancher.debug=true` はデバッグのためにすべてをコンソールに記録します。

### Autologin console

`rancher.autologin = <tty ...>` は指定されたコンソールに自動的にログインします。
プラットフォームによって異なりますが、一般的な値は `tty1`、`ttyS0`、および `ttyACM0` です。

### Enable/Disable hypervisor service auto-enable

RancherOS v1.1.0はHypervisorの検出を追加し、それから `<hypervisor>-vm-tools` と呼ばれるサービスをダウンロードしようとします。
これは起動速度の問題を引き起こす可能性があるので、`rancher.hypervisor_service=false` を設定することで無効にできます。

### Auto reboot after a kernel panic
v1.3以降で利用可能

`panic=10` はカーネルパニックの後に自動的に再起動します、10は再起動の前に10秒間待つことを意味します。
これは一般的なカーネルパラメータで、デフォルトでこのパラメータを設定しているためだと指摘しています。


