# System Logging

## System services

RancherOSはシステムサービスにコンテナを使用します。
これは、`syslog`、`acipd`、`system-cron`、`udev`、`network`、`ntp`、`console`、およびユーザDockerのログが、`sudo ros service logs <service-name>` を使用して利用できることを意味します。

## Boot logging

v1.1.0以降、initプロセスのログは、ユーザースペースファイルシステムが利用可能になった後に `/var/log/boot` にコピーされます。
これらは、初期化、ネットワーク、およびクラウド初期化の問題を診断するために使用できます。

## Remote Syslog logging

Linuxカーネルには、カーネルレベルのログをリモートのSyslogサーバーに送信するための `netconsole` ロギング機能があります。
RancherOS v1.1.0以降でこのカーネルブートパラメーターを設定すると、RancherOSのデバッグログも送信されます。

LinuxカーネルとRancherOSのリモートSyslogロギングを設定するには、ローカルとリモートの両方のホストIPアドレスを設定する必要があります。
このアドレスがシステムの最終IPアドレスではない場合でも。
カーネル設定は以下のようになります。

```
 netconsole=[+][src-port]@[src-ip]/[<dev>],[tgt-port]@<tgt-ip>/[tgt-macaddr]

   where
        +             if present, enable extended console support
        src-port      source for UDP packets (defaults to 6665)
        src-ip        source IP to use (interface address)
        dev           network interface (eth0)
        tgt-port      port for logging agent (6666)
        tgt-ip        IP address for logging agent
        tgt-macaddr   ethernet MAC address for logging agent (broadcast)
```

たとえば、現在のテストシステムでは、カーネルのブート行を次のように設定しています。

```
printk.devkmsg=on console=tty1 rancher.autologin=tty1 console=ttyS0 rancher.autologin=ttyS0    rancher.state.dev=LABEL=RANCHER_STATE rancher.state.autoformat=[/dev/sda,/dev/vda] rancher.rm_usr loglevel=8 netconsole=+9999@10.0.2.14/,514@192.168.42.223/
```

カーネルブートパラメータは、`sudo ros install --append "...."` を使用してインストール中に、またはインストールされたRancherOSシステム上で、`sudo ros config syslinx`（コンテナ内でviを起動し、ブート設定ファイル `global.cfg` を編集する）を実行することで設定できます。


