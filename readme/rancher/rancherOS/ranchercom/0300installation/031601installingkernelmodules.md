# Installing Kernel Modules that require Kernel Headers

カーネルモジュールをコンパイルするには、カーネルヘッダをダウンロードする必要があります。
カーネルヘッダはシステムサービスの形で利用できます。
カーネルヘッダはシステムサービスなので、`ros service` コマンドを使って有効にする必要があります。

## Installing Kernel Headers

以下のコマンドは、DockerまたはSystem Dockerのコンテナーで使用するためのカーネルヘッダーをインストールするために使用できます。

### Docker

```
$ sudo ros service enable kernel-headers
$ sudo ros service up kernel-headers
```

### System Docker

```
$ sudo ros service enable kernel-headers-system-docker
$ sudo ros service up kernel-headers-system-docker
```

ros serviceコマンドは/ lib / modules / $（uname -r）/ buildにカーネルヘッダをインストールします。 どのサービスをインストールするかに基づいて、DockerまたはSystem Dockerのカーネルヘッダーは、特定のボリュームをバインドマウントすることによって、コンテナーで使用可能になります。 カーネルモジュールをコンパイルするコンテナでは、Dockerコマンドは/ usr / srcと/ lib / modulesにmountをバインドする必要があります。

`ros service` コマンドは `/lib/modules/$(uname -r)/build` にカーネルヘッダをインストールします。
どのサービスをインストールするかに基づいて、DockerまたはSystem Dockerのカーネルヘッダーは、特定のボリュームをバインドマウントすることによって、コンテナーで使用可能になります。
カーネルモジュールをコンパイルするコンテナでは、Dockerコマンドは `/usr/src` と `/lib/modules` にmountをバインドする必要があります。

> **Note：**
> どちらのコマンドもカーネルヘッダーを同じ場所にインストールするため、サービスが異なる理由は、System DockerとDockerの格納場所が異なるためです。
> 一方または両方のカーネルヘッダーを同じRancherOSサービスにインストールできます。

## Example of Launching Containers to use Kernel Headers

```
# Run a container in Docker and bind mount specific directories
$ docker run -it -v /usr/src:/usr/src -v /lib/modules:/lib/modules ubuntu:15.10
# Run a container in System Docker and bind mount specific directories
$ sudo system-docker run -it -v /usr/src:/usr/src -v /lib/modules:/lib/modules ubuntu:15.10
```

