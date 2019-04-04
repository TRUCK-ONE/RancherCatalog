# Disabling Access to RancherOS
v1.5から利用可能

RancherOSでは、`rancher.password` をカーネルパラメータとして設定し、`自動ログイン`を有効にすることができますが、これら両方のオプションを無効にしたい場合があるかもしれません。
これらのオプションは両方ともcloud-configで、または `ros` コマンドの一部として無効にできます。

## How to Disabling Options

RancherOSがすでに起動されている場合は、`ros config set` を使用して無効にしたい設定を更新できます。

```
# Disabling the `rancher.password` kernel parameter
$ sudo ros config set rancher.disable ["password"]

# Disabling the `autologin` ability
$ sudo ros config set rancher.disable ["autologin"]
```

あるいは、RancherOSを起動したときに自動的に無効になるようにcloud-configで設定することもできます。

```
# cloud-config
rancher:
  disable:
  - password
  - autologin
```
