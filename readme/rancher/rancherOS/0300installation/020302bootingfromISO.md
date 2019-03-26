# Booting from ISO

RancherOS ISOファイルは、KVM、VMware、VirtualBox、またはベアメタルサーバーに新しいRancherOSインストールを作成するために使用できます。
`rancheros.iso` ファイルは[リリースページ](https://github.com/rancher/os/releases/)からダウンロードできます。

最低**1280MB**のメモリで起動する必要があります。
ISOで起動すると、自動的に `rancher` のユーザーとしてログインします。
ISOのみがデフォルトで自動ログインを使用するように設定されています。
クラウドから実行する場合、またはディスクにインストールする場合は、SSHキーまたは選択したパスワードが使用されると予想されます。

## Install to Disk

ISOからRancherOSを起動した後、[ここ](https://rancher.com/docs/os/v1.x/en/installation/running-rancheros/server/install-to-disk/)にある指示に従ってRancherOSをハードディスクにインストールしてください。


