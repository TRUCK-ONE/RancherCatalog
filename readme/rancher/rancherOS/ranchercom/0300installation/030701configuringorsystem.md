# Configuring Docker or System Docker

RancherOSでは、[cloud-config](https://rancher.com/docs/os/v1.x/en/installation/configuration/#cloud-config)を使用してSystem DockerデーモンとDockerデーモンを構成できます。

## Configuring Docker

cloud-configでは、Dockerの設定は `rancher.docker` キーの下にあります。

```
#cloud-config
rancher:
  docker:
    tls: true
    tls_args:
      - "--tlsverify"
      - "--tlscacert=/etc/docker/tls/ca.pem"
      - "--tlscert=/etc/docker/tls/server-cert.pem"
      - "--tlskey=/etc/docker/tls/server-key.pem"
      - "-H=0.0.0.0:2376"
    storage_driver: overlay
```

`ros config` を使って起動した後でDockerをカスタマイズすることもできます。

```
$ sudo ros config set rancher.docker.storage_driver overlay
```

### User Docker settings

標準のDockerデーモン引数の多くは、`rancher.docker` キーの下に置くことができます。
Dockerデーモンを起動するために必要なコマンドは、これらの引数に基づいて生成されます。
以下の引数が現在サポートされています。

| Key | Value |
| --- | --- |
| `bridge` | String |
| `config_file` | String |
| `containerd` | String |
| `debug` | Boolean |
| `exec_root` | String |
| `group` | String |
| `graph` | String |
| `host` | List |
| `insecure_registry` | List |
| `live_restore` | Boolean |
| `log_driver` | String |
| `log_opts` | Map where keys and values are strings |
| `pid_file` | String |
| `registry_mirror` | String |
| `restart` | Boolean |
| `selinux_enabled` | Boolean |
| `storage_driver` | String |
| `userland_proxy` | Boolean |

標準のデーモン引数に加えて、RancherOSに固有のいくつかのフィールドがあります。

| Key | Value | Default | Description |
| --- | --- | --- | --- |
| `extra_args` | List of Strings | `[]` | 生成されたコマンドに付加される任意のデーモン引数 |
| `environment` | List of Strings | `[]` |  |
| `tls` | Boolean | `false` | [TLSを設定する](https://rancher.com/docs/os/v1.x/en/installation/configuration/setting-up-docker-tls/)ときは、このキーをtrueに設定する必要があります。 |
| `tls_args` | List of Strings (`tls: true` の場合のみ) | `[]` | |
| `server_key` | Strings (`tls: true` の場合のみ) | `""` | PEMエンコードサーバーのTLSキー |
| `server_cert` | Strings (`tls: true` の場合のみ) | `""` | PEMエンコードされたCA TLSキー |
| `storage_context` | String | `console` | Dockerデーモンプロセスを実行するコンテキスト内のシステムコンテナの名前を指定します。 |

### Example using extra_args for setting MTU(MTUを設定するためのextra_argsの使用例)

次の例は、DockerデーモンにMTUを設定するために使用できます。

```
#cloud-config
rancher:
  docker:
    extra_args: [--mtu, 1460]
```

## Configuring System Docker

cloud-configでは、System Dockerの設定は `rancher.system_docker` キーの下にあります。

```
#cloud-config
rancher:
  system_docker:
    storage_driver: overlay
```

### System Docker settings

最初の表に示されているすべてのデーモン引数はSystem Dockerでも使用できます。
以下もサポートされています。

| Key | Value | Default | Description |
| --- | --- | --- | --- |
| `extra_args` | List of Strings | `[]` | 生成されたコマンドに付加される任意のデーモン引数 |
| `environment` | 	List of Strings (optional) | `[]` | |
v1.4.xから利用可能

docker-sysブリッジはsystem-docker argsで設定することができ、再起動後に有効になります。

```
$ ros config set rancher.system_docker.bip 172.18.43.1/16
```
v1.4.xから利用可能

system-dockerログのデフォルトパスは `/var/log/system-docker.log` です。
system-dockerログを別のパーティションに書き込みたい場合は、[RANCHE_OEMパーティション](https://rancher.com/docs/os/v1.x/en/about/custom-partition-layout/#use-rancher-oem-partition)は、`rancher.defaults.system_docker_logs` を試すことができます。

```
#cloud-config
rancher:
  defaults:
    system_docker_logs: /usr/share/ros/oem/system-docker.log
```

## Using a pull through registry mirror

プルスルーDocker Hubレジストリミラーキャッシュを使用するように構成できる3つのDockerエンジンがあります。

```
#cloud-config
rancher:
  bootstrap_docker:
    registry_mirror: "http://10.10.10.23:5555"
  docker:
    registry_mirror: "http://10.10.10.23:5555"
  system_docker:
    registry_mirror: "http://10.10.10.23:5555"
```

`bootstrap_docker` はネットワークの準備と初期化に使用され、最終的なネットワーク設定とSystem-dockerを設定するために使用できるcloud-configオプションを引き出す - これはイメージを引き出すことはほとんどありません。

System-dockerによるミラーキャッシュへのプルスルー要求に成功すると、次のようになります。

```
[root@rancher-dev rancher]# system-docker pull alpine
Using default tag: latest
DEBU[0201] Calling GET /v1.23/info
> WARN[0201] Could not get operating system name: Error opening /usr/lib/os-release: open /usr/lib/os-release: no such file or directory
WARN[0201] Could not get operating system name: Error opening /usr/lib/os-release: open /usr/lib/os-release: no such file or directory
DEBU[0201] Calling POST /v1.23/images/create?fromImage=alpine%3Alatest
DEBU[0201] hostDir: /etc/docker/certs.d/10.10.10.23:5555
DEBU[0201] Trying to pull alpine from http://10.10.10.23:5555/ v2
DEBU[0204] Pulling ref from V2 registry: alpine:latest
DEBU[0204] pulling blob "sha256:2aecc7e1714b6fad58d13aedb0639011b37b86f743ba7b6a52d82bd03014b78e" latest: Pulling from library/alpine
DEBU[0204] Downloaded 2aecc7e1714b to tempfile /var/lib/system-docker/tmp/GetImageBlob281102233 2aecc7e1714b: Extracting  1.99 MB/1.99 MB
DEBU[0204] Untar time: 0.161064213s
DEBU[0204] Applied tar sha256:3fb66f713c9fa9debcdaa58bb9858bd04c17350d9614b7a250ec0ee527319e59 to 841c99a5995007d7a66b922be9bafdd38f8090af17295b4a44436ef433a2aecc7e1714b: Pull complete
Digest: sha256:0b94d1d1b5eb130dd0253374552445b39470653fb1a1ec2d81490948876e462c
Status: Downloaded newer image for alpine:latest
```

## Using Multiple User Docker Daemons
v1.5.0から利用可能

RancherOSが起動すると、System Dockerで実行されている User Dockerサービスから始めます。
v1.5.0では、RancherOSは同時に実行できる追加のUser Dockerサービスを作成することができます。

### Terminology(用語)

このドキュメントの残りの部分では、Dockerを説明するときにこれらの用語を簡単に使用することができます。

| Terminology | Definition |
| --- | --- |
| DinD | Docker in docker |
| User Docker | The user-docker on RancherOS |
| Other User Docker | 作成した他のユーザードッカーデーモン、これらのユーザードッカーデーモンは自動的にDocker in Dockerと見なされます。 |

### Pre-Requisites(前提条件)

User DockerはDocker 17.12.1以前に設定する必要があります。
それがそれ以降のバージョンのDockerである場合、それはSystem Dockerでユーザー定義ネットワークを作成するときにエラーを生成します。

```
$ ros engine switch docker-17.12.1-ce
```

他のユーザードッカーを作成するときに使用されるユーザー定義ネットワークを作成する必要があります。

```
$ system-docker network create --subnet=172.20.0.0/16 dind
```

### Create the Other User Docker(他のユーザーDockerを作成する)

別のUser Dockerを作成するには、`ros engine create` を使用します。
現在、RancherOSはOther User Dockerイメージに対してDocker `17.12.1` および `18.03.1` のみをサポートしています。

```
$ ros engine create otheruserdockername --network=dind --fixed-ip=172.20.0.2
```

Other User Dockerサービスが作成された後、ユーザーは他のサービスと同様にこのサービスを照会できます。

```
$ ros service list
...
...
disabled volume-efs
disabled volume-nfs
enabled  otheruserdockername
```

`ros service up`を使ってOther User Dockerサービスを開始できます。

```
$ ros service up otheruserdockername
```

Other User Dockerサービスが実行された後は、組み込みのUser Dockerを使用するのと同じように対話できます。
`docker` に - `<SERVICE_NAME>` を追加する必要があります。

```
$ docker-otheruserdockername ps -a
```

### SSH into the Other User Docker container(Other User DockerコンテナーへのSSH接続)

Other User Dockerを作成するときは、System DockerのOther User DockerコンテナにSSHで接続できるように外部SSHポートを設定できます。
`--ssh-port` を使用して `--authorized-keys` にsshキーを追加することで、このオプションのSSHポートを設定できます。

```
$ ros engine create  --help
...
...
OPTIONS:
    --ssh-port value
    --authorized-keys value
```

`--authorized-keys` を使用するときは、キーファイルを次のディレクトリのいずれかに置く必要があります。

```
/var/lib/rancher/
/opt/
/home/
```

RancherOSはOther User Dockerコンテナごとにランダムなパスワードを生成します。
これはコンテナで確認できます。SSHキーを設定していない場合は、パスワードを使用します。

```
$ system-docker logs otheruserdockername

======================================
chpasswd: password for 'root' changed
password: xCrw6fEG
======================================
```

System Dockerでは、`ssh` を使って他のUesr Dockerコンテナに SSH で接続できます。

```
$ system-docker ps
CONTAINER ID        IMAGE                              COMMAND                  CREATED             STATUS              PORTS                             NAMES
2ca07a25799b        rancher/os-dind:17.12.1          "docker-entrypoint..."   5 seconds ago       Up 3 seconds        2375/tcp, 0.0.0.0:34791->22/tcp   otheruserdockername

$ ssh -p 34791 root@<HOST_EXTERNAL_IP>

$ ssh root@<OTHERUSERDOCKER_CONTAINER_IP>
```

### Removing any Other User Docker Service(他のUser Dockerサービスを削除する)

Other User Dockerサービスを削除するには、`ros engine rm` を使用することをお勧めします。

```
$ ros engine rm otheruserdockername
```


