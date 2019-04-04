# Custom Console

[ISOから起動する](https://rancher.com/docs/os/v1.x/en/installation/running-rancheros/workstation/boot-from-iso/)と、RancherOSはbusyboxに基づいたデフォルトのコンソールで起動します。

[cloud-config](https://rancher.com/docs/os/v1.x/en/installation/configuration/#cloud-config)を使用して、どのコンソールからRancherOSを起動するかを選択できます。

## Enabling Consoles using Cloud-Config

[cloud-config](https://rancher.com/docs/os/v1.x/en/installation/configuration/#cloud-config)ファイルを使用してRancherOSを起動するときに、使用したいコンソールを選択できます。

現在利用可能なコンソールのリストは次のとおりです。

- default
- alpine
- centos
- debian
- fedora
- ubuntu

以下はdebianコンソールを有効にするために使用できるcloud-configファイルの例です。

```
#cloud-config
rancher:
  console: debian
```

## Listing Available Consoles

あなたは簡単にRancherOSで利用可能なコンソールとそれらのステータスが `sudo ros console list` で何であるかを一覧表示できます。

```
$ sudo ros console list
disabled alpine
disabled centos
disabled debian
current  default
disabled fedora
disabled ubuntu
```

## Changing Consoles after RancherOS has started

System Dockerでどのコンソールコンテナーが実行されているかを確認することで、RancherOSがどのコンソールを使用しているかを確認できます。
コンソールを切り替えたい場合は、単純なコマンドを実行して新しいコンソールを選択するだけです。

この例では、Ubuntuコンソールに切り替えます。

```
$ sudo ros console switch ubuntu
Switching consoles will
1. destroy the current console container
2. log you out
3. restart Docker
Continue [y/N]:y
Pulling console (rancher/os-ubuntuconsole:v0.5.0-3)...
v0.5.0-3: Pulling from rancher/os-ubuntuconsole
6d3a6d998241: Pull complete
606b08bdd0f3: Pull complete
1d99b95ffc1c: Pull complete
a3ed95caeb02: Pull complete
3fc2f42db623: Pull complete
2fb84911e8d2: Pull complete
fff5d987b31c: Pull complete
e7849ae8f782: Pull complete
de375d40ae05: Pull complete
8939c16614d1: Pull complete
Digest: sha256:37224c3964801d633ea8b9629137bc9d4a8db9d37f47901111b119d3e597d15b
Status: Downloaded newer image for rancher/os-ubuntuconsole:v0.5.0-3
switch-console_1 | time="2016-07-02T01:47:14Z" level=info msg="Project [os]: Starting project "
switch-console_1 | time="2016-07-02T01:47:14Z" level=info msg="[0/18] [console]: Starting "
switch-console_1 | time="2016-07-02T01:47:14Z" level=info msg="Recreating console"
Connection to 127.0.0.1 closed by remote host.
```

ログアウトしたら、Ubuntuコンソールに入ります。

```
$ sudo system-docker ps
CONTAINER ID        IMAGE                                 COMMAND                  CREATED              STATUS              PORTS               NAMES
6bf33541b2dc        rancher/os-ubuntuconsole:v0.5.0-rc3   "/usr/sbin/entry.sh /"   About a minute ago   Up About a minute
```

> **Note：**
> コンソールを切り替えると、現在実行中のコンソールコンテナが破壊され、Dockerが再起動されてログアウトします。

## Console persistence

デフォルト（busybox）コンソールを除くすべてのコンソールは永続的です。
永続的コンソールとは、コンソールコンテナが同じままで再起動後にファイルシステムに加えられた変更を保持することを意味します。
コンテナが削除/再構築されると、永続化されたディレクトリにあるものを除いてコンソールの状態は失われます。

```
/home
/opt
/var/lib/docker
/var/lib/rancher
```

> **Note：**
> 永続的なコンソールを使用している場合、および現在のバージョンのコンソールでは、ロールバックはサポートされていません。
> たとえば、v0.5.0の永続的コンソールを使用しているときにv0.4.5にロールバックすることはサポートされていません。

## Enabling Consoles

次回の再起動時に変更されるコンソールを有効にすることもできます。

私たちの例では、Debianコンソールに切り替えます。

```
# Check the console running in System Docker
$ sudo system-docker ps
CONTAINER ID        IMAGE                              COMMAND                  CREATED             STATUS              PORTS               NAMES
95d548689e82        rancher/os-docker:v0.5.0    "/usr/sbin/entry.sh /"   About an hour ago   Up About an hour                        docker
# Enable the Debian console
$ sudo ros console enable debian
Pulling console (rancher/os-debianconsole:v0.5.0-3)...
v0.5.0-3: Pulling from rancher/os-debianconsole
7268d8f794c4: Pull complete
a3ed95caeb02: Pull complete
21cb8a645d75: Pull complete
5ee1d288a088: Pull complete
c09f41c2bd29: Pull complete
02b48ce40553: Pull complete
38a4150e7e9c: Pull complete
Digest: sha256:5dbca5ba6c3b7ba6cd6ac75a1d054145db4b4ea140db732bfcbd06f17059c5d0
Status: Downloaded newer image for rancher/os-debianconsole:v0.5.0-3
```

次回の再起動時に、RancherOSはDebianコンソールを使用します。


