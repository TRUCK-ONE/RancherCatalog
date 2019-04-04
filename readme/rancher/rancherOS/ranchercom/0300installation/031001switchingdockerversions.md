# Switching Docker Versions

RancherOSで使用されているUser Dockerのバージョンは、[cloud-config](https://rancher.com/docs/os/v1.x/en/installation/configuration/#cloud-config) ファイルまたは `ros engine` コマンドを使用して設定できます。

> **Note：**
> バージョンを切り替えるときのDockerの既知の問題があります。
> プロダクションシステムでは、[cloud-configを使用して](https://rancher.com/docs/os/v1.x/en/installation/configuration/switching-docker-versions/#setting-the-docker-engine-using-cloud-config)Dockerエンジンを一度だけ設定することをお勧めします。

### Available Docker engines(利用可能なDockerエンジン)

`ros engine list` コマンドを使用して、どのDockerエンジンに切り替えることができるかを示すことができます。
このコマンドは、どのDockerエンジンが現在使用されているかについての詳細も提供します。

```
$ sudo ros engine list
disabled docker-1.10.3
disabled docker-1.11.2
current  docker-1.12.1
```

### Setting the Docker engine using cloud-config(cloud-configを使ってDockerエンジンを設定する)

RancherOSはcloud-configファイルを通してどのDockerエンジンを使用するかを定義することをサポートします。
Dockerのバージョンをデフォルトのパッケージバージョンから変更するには、次のcloud-config設定を使用して利用可能なエンジンのいずれかを選択します。
次の例では、cloud-configファイルを使用して、RancherOSがユーザーDockerにDocker 1.10.3を使用するように設定します。

```
#cloud-config
rancher:
  docker:
    engine: docker-1.10.3
```

### Changing Docker engines after RancherOS has started(RancherOSが起動した後にDockerエンジンを変更する)

すでにRancherOSを起動していてDockerエンジンを切り替えたい場合は、`ros engine switch` コマンドを使用してDockerエンジンを変更できます。
この例では、Docker 1.11.2に切り替えます。

```
$ sudo ros engine switch docker-1.11.2
INFO[0000] Project [os]: Starting project
INFO[0000] [0/19] [docker]: Starting
Pulling docker (rancher/os-docker:1.11.2)...
1.11.2: Pulling from rancher/os-docker
2a6bbb293656: Pull complete
Digest: sha256:ec57fb24f6d4856d737e14c81a20f303afbeef11fc896d31b4e498829f5d18b2
Status: Downloaded newer image for rancher/os-docker:1.11.2
INFO[0007] Recreating docker
INFO[0007] [1/19] [docker]: Started
INFO[0007] Project [os]: Project started
$ docker version
Client:
 Version:      1.11.2
 API version:  1.23
 Go version:   go1.5.4
 Git commit:   b9f10c9
 Built:        Wed Jun  1 21:20:08 2016
 OS/Arch:      linux/amd64

Server:
 Version:      1.11.2
 API version:  1.23
 Go version:   go1.5.4
 Git commit:   b9f10c9
 Built:        Wed Jun  1 21:20:08 2016
 OS/Arch:      linux/amd64
```

### Enabling Docker engines(Dockerエンジンを有効にする)

Dockerエンジンを自動的に切り替えたくない場合は、Dockerエンジンを有効にして、次回の再起動後に使用するDockerのバージョンを設定することもできます。

```
$ sudo ros engine enable docker-1.10.3
```

## Using a Custom Version of Docker(カスタムバージョンのDockerを使用する)

デフォルトでは利用できないバージョンのDocker、またはDockerのカスタムビルドを使用している場合は、カスタムDockerイメージとサービスファイルを作成して配布することができます。

Dockerエンジンイメージは、バイナリを `engine`という名前のフォルダーに追加し、次にこのフォルダーを　`FROM scratch` イメージに追加することによって作成されます。
たとえば、次のDockerfileはDockerエンジンイメージを構築します。

```
FROM scratch
COPY engine /engine
```

イメージが構築されたら、[システムサービス](https://rancher.com/docs/os/v1.x/en/installation/system-services/adding-system-services/)設定ファイルを作成する必要があります。
[サンプルファイル](https://github.com/rancher/os-services/blob/master/d/docker-1.12.3.yml)は、rancher/os-services リポジトリにあります。
作成したDockerエンジンのイメージを指すように`イメージ`フィールドを変更します。

Dockerエンジンを切り替える前述のすべての方法が利用可能になりました。
たとえば、サービスファイルが `https://myservicefile` にある場合は、次のcloud-configファイルを使用してカスタムDockerエンジンを使用できます。

```
#cloud-config
rancher:
  docker:
    engine: https://myservicefile
```

