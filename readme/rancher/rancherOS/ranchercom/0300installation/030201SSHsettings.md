# SSH Settings

RancherOSは [cloud-config](https://rancher.com/docs/os/v1.x/en/installation/configuration/#cloud-config)ファイルを通してSSHキーを追加することをサポートします。
cloud-configファイル内で、`ssh_authorized_keys`キー内にsshキーを追加するだけです。

```
#cloud-config
ssh_authorized_keys:
  - ssh-rsa AAA...ZZZ example1@rancher
  - ssh-rsa BBB...ZZZ example2@rancher
```

`ros install` コマンドの実行中にcloud-configファイルを渡すと、これらのsshキーを **rancher**ユーザーに関連付けることができます。
キーを使ってRancherOSにSSHでログインできます。

```
$ ssh -i /path/to/private/key rancher@<ip-address>
```

OpenSSH 7.0以降も同様にssh-dss（DSA）公開鍵アルゴリズムを無効にします。
あまりにも弱いので、その使用はお勧めしません。

## SSHD Port and IP

v1.3以降で利用可能

RancherOSはsshdポートとIPの変更をサポートしています、あなたはcloud-configファイルでこれらを使うことができます：

```
rancher:
  ssh:
    port: 10022
    listen_address: 172.22.100.100
```

これらの設定はデフォルトコンソール専用です。
sshd-configを変更した場合、ホストを再起動するとデフォルトが復元されるため、新しい設定は有効になりません。

他のコンソールでは、すべてのファイルは永続的です、あなたは自分でsshd-configを修正することができます。

