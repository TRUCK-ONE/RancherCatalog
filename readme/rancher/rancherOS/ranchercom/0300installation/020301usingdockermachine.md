# Using Docker Machine

始める前に、docker machineがインストールされていることを確認する必要があります。
docker machine [release](https://github.com/docker/machine/releases)から直接ダウンロードしてください。
また、[メモリ要件](https://rancher.com/docs/os/v1.x/en/#hardware-requirements)を知る必要があります。

> **Note：**  
> Docker Machineを使用してRancherOSインスタンスを作成した場合、RancherOSのバージョンをアップグレードすることはできません。

## Downloading RancherOS

RancherOS[リリース](https://github.com/rancher/os)から最新のISO成果物を入手してください。

| Machine Driver | Recommended RancherOS version | ISO File |
| --- | --- | --- |
| VirtualBox | >=v1.0.0 | [rancheros.iso](https://releases.rancher.com/os/latest/rancheros.iso) |
| VMWare VSphere | >=v1.4.0 | [rancheros-autoformat.iso](https://releases.rancher.com/os/latest/vmware/rancheros-autoformat.iso) |
| VMWare Fusion | >=v1.4.0 | [rancheros-autoformat.iso](https://releases.rancher.com/os/latest/vmware/rancheros-autoformat.iso) |
| Hyper-V | >=v1.5.0 | [rancheros.iso](https://releases.rancher.com/os/latest/hyperv/rancheros.iso) |

## Using Docker Machine

Docker Machineを使用して、さまざまなプロバイダ用のVMを起動できます。
現在VirtualBoxとVMWare（VMWare VSphere、VMWare Fusion）とAWSがサポートされています。

### Using Docker Machine with VirtualBox

先に進む前に、VirtualBoxをインストールする必要があります。
VirtualBoxから直接ダウンロードしてください。
VirtualBoxとDocker Machineをインストールしたら、RancherOSを実行するための1つのコマンドになります。

RancherOSの最新リンクの使用例は次のとおりです。

```
$ docker-machine create -d virtualbox \
        --virtualbox-boot2docker-url https://releases.rancher.com/os/latest/rancheros.iso \
        --virtualbox-memory <MEMORY-SIZE> \
        <MACHINE-NAME>
```

> **Note：**  
> ISOをダウンロードする代わりに、`rancheros.iso` のURLを直接使用することができます。

それでおしまい！
これで、VirtualBox上でRancherOSホストが稼働しているはずです。
VirtualBox VMがホスト上で実行されていることを確認できます。

> **Note：**  
> マシンが作成された後、Dockerマシンは作成に関するいくつかのエラーを表示するかもしれません、しかしVirtualBox VMが実行されていれば、あなたはログインすることができるはずです。

```
$ VBoxManage list runningvms | grep <MACHINE-NAME>
```

このコマンドは新しく作成されたマシンを印刷します。
そうでない場合は、プロビジョニング手順で問題が発生しました。

### Using Docker Machine with VMWare VSphere
*v1.4から利用可能*

先に進む前に、VMWare VSphereをインストールする必要があります。
VMWare VSphereとDocker Machineをインストールしたら、RancherOSを実行するための1つのコマンドになります。

RancherOSの最新リンクの使用例は次のとおりです。

```
$ docker-machine create -d vmwarevsphere \
        --vmwarevsphere-username <USERNAME> \
        --vmwarevsphere-password <PASSWORD> \
        --vmwarevsphere-memory-size <MEMORY-SIZE> \
        --vmwarevsphere-boot2docker-url https://releases.rancher.com/os/latest/vmware/rancheros-autoformat.iso \
        --vmwarevsphere-vcenter <IP-ADDRESS> \
        --vmwarevsphere-vcenter-port <PORT> \
        --vmwarevsphere-disk-size <DISK-SIZE> \
        <MACHINE-NAME>
```

それでおしまい！
これで、RancherOSホストがVMWare VSphere上で実行されるはずです。
ホストでVMWare（ESXi）VMが実行されていることを確認できます。

### Using Docker Machine with VMWare Fusion
*v1.4から利用可能*

先に進む前に、VMWare Fusionをインストールする必要があります。
VMWare FusionとDocker Machineをインストールしたら、RancherOSを実行するための1つのコマンドになります。

RancherOSの最新リンクの使用例は次のとおりです。

```
$ docker-machine create -d vmwarefusion \
        --vmwarefusion-no-share \
        --vmwarefusion-memory-size <MEMORY> \
        --vmwarefusion-boot2docker-url https://releases.rancher.com/os/latest/vmware/rancheros-autoformat.iso \
        <MACHINE_NAME>
```

それでおしまい！
これで、VMWare Fusion上でRancherOSホストが実行されるはずです。
ホスト上でVMWare Fusion VMが実行されていることを確認できます。

### Using Docker Machine with Hyper-V
*v1.5から利用可能*

[Hyper-Vドライバ](https://docs.docker.com/machine/drivers/hyper-v/)のドキュメントを参照する必要があります。
これは最新のRancherOS URLの使用例です。
どのバージョンのRancherOSをインストールしているかわかるように、特定のバージョンを使用することをお勧めします。

```
$ docker-machine.exe create -d hyperv \
        --hyperv-memory 2048 \
        --hyperv-boot2docker-url https://releases.rancher.com/os/latest/hyperv/rancheros.iso
        --hyperv-virtual-switch <SWITCH_NAME> \
        <MACHINE_NAME>
```

## Logging into RancherOS

RancherOSへのログインは標準のDocker Machineコマンドに従います。
新しくプロビジョニングしたRancherOS VMにログインします。

```
$ docker-machine ssh <MACHINE-NAME>
```

RancherOSにログインしてOSの探索を開始できます。
これでRancherOS VMにログインします。
その後、システムサービスを追加し、設定をカスタマイズし、コンテナを起動することでOSを探索できます。

RancherOSを終了したい場合は、`Ctrl+D` を押して終了できます。

## Docker Machine Benefits

Docker Machineを使用すると、ホスト上のdockerクライアントからVM内で実行されているdockerデーモンを指すことができます。
これはあなたがあなたのホストにdockerをインストールしたかのようにあなたのdockerコマンドを実行することを可能にします。

DockerクライアントをVM内のDockerデーモンに向けるには、次のコマンドを使用します。

```
$ eval $(docker-machine env <MACHINE-NAME>)
```

これを設定した後、あなたはあなたのホストでどんなdockerコマンドでも実行することができ、そしてそれはあなたのRancherOS VMでコマンドを実行するでしょう。

```
$ docker run -p 80:80 -p 443:443 -d nginx
```

あなたのVMでは、nginxコンテナがあなたのVM上で起動します。
コンテナにアクセスするには、VMのIPアドレスが必要です。

```
$ docker-machine ip <MACHINE-NAME>
```

IPアドレスを取得したら、それをブラウザに貼り付けると、nginxのウェルカムページが表示されます。


