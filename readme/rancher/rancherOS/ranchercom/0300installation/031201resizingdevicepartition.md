# Resizing a Device Partition

`resize_device` クラウド設定オプションを使用すると、最初のパーティションを（`ext4` と仮定して）自動的に拡張して、デバイスのサイズを埋めることができます。

パーティションがデバイスを埋めるようにサイズ変更されると、サイズ変更ツールが再度実行されないように `/var/lib/rancher/resizefs.done`ファイルが書き込まれます。
再度実行する必要がある場合は、そのファイルを削除して再起動してください。

```
#cloud-config
rancher:
  resize_device: /dev/sda
```

この動作は、AWSでRancherOSを起動したときのデフォルトです。
