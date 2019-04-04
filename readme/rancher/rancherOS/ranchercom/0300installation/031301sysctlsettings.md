# Sysctl Settings

`rancher.sysctl` cloud-configキーを使用してsysctlパラメータを制御できます。
これは他のLinuxディストリビューションの `/etc/sysctl.conf` に似た方法で機能します。

```
#cloud-config
rancher:
  sysctl:
    net.ipv4.conf.default.rp_filter: 1
```

これらの設定を `cloud-init.yml` に追加するか、`sudo ros config merge -i somefile.yml` を使用して既存のシステムに設定をマージすることができます。


