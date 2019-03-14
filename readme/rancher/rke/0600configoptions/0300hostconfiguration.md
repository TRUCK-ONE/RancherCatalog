# Bastion/Jump Host Configuration

RKEは `ssh` を使用して[ノード](0100nodes.md)に接続するので、要塞ホストを使用するように設定できます。
RKEノードの[ポート要件](https://rancher.com/docs/rke/v0.1.x/en/os/#ports)は設定された要塞ホストに移動することに注意してください。

```
bastion_host:
    address: x.x.x.x
    user: ubuntu
    port: 22
    ssh_key_path: /home/user/.ssh/bastion_rsa
    # or
    # ssh_key: |-
    #   -----BEGIN RSA PRIVATE KEY-----
    #
    #   -----END RSA PRIVATE KEY-----
```

## Bastion Host Options

### Address

`address` ディレクティブは要塞ホストのホスト名またはIPアドレスを設定するために使用されます。
RKEはこのアドレスに接続できなければなりません。

### SSH Port

要塞ホストに接続するときに使用する `port` を指定します。
デフォルトのポートは `22` です。

### SSH Users

このノードに接続するときに使用される `user` を指定します。

### SSH Key Path

要塞ホストに接続するときに使用するSSH秘密鍵のパス、つまり `ssh_key_path` を指定します。

### SSH Key

SSHキーへのパスを設定する代わりに、要塞ホストへの接続に使用される実際のキー、つまり `ssh_key` を指定できます。


