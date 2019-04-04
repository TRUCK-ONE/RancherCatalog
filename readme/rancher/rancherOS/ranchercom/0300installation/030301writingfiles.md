# Writing Files

`write_files` cloud-configディレクティブを使用してファイルのディスクへの書き込みを自動化できます。

```
#cloud-config
write_files:
  - path: /etc/rc.local
    permissions: "0755"
    owner: root
    content: |
      #!/bin/bash
      echo "I'm doing things on start"
```

## Writing Files in Specific System Services(特定のシステムサービスにファイルを書き込む)

デフォルトでは、`write_files` ディレクティブはコンソールコンテナにファイルを作成します。
他のシステムサービスにファイルを書き込むには、`コンテナ`キーを使用できます。
たとえば、`コンテナ`キーを使用して、NTPシステムサービスの `/etc/ntp.conf` に書き込むことができます。

```
#cloud-config
write_files:
  - container: ntp
    path: /etc/ntp.conf
    permissions: "0644"
    owner: root
    content: |
      server 0.pool.ntp.org iburst
      server 1.pool.ntp.org iburst
      server 2.pool.ntp.org iburst
      server 3.pool.ntp.org iburst

      # Allow only time queries, at a limited rate, sending KoD when in excess.
      # Allow all local queries (IPv4, IPv6)
      restrict default nomodify nopeer noquery limited kod
      restrict 127.0.0.1
      restrict [::1]
```

> **Note：**
> 現在、特定のシステムサービスへのファイルの書き込みは、RancherOSの組み込みサービスでのみサポートされています。
> カスタムシステムサービスにファイルを書き込めません。


