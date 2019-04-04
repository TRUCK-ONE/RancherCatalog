# Setting up Docker TLS

`ros tls generate` は、Docker用のクライアントとサーバーの両方のTLS証明書を生成するために使用されます。

覚えておいて、すべての `ros` コマンドは `sudo` と一緒に使うか、または `root` ユーザーとして使う必要がある。

## End to end example

### Enable TLS for Docker and Generate Server Certificate(Dockerに対してTLSを有効にしてサーバー証明書を生成する)

dockerをTLSで保護するには、`rancher.docker.tls` を `true` に設定し、サーバーとクライアントの鍵と証明書のセットを生成する必要があります。

```
$ sudo ros config set rancher.docker.tls true
$ sudo ros tls gen --server -H localhost -H <hostname1> -H <hostname2> ... -H <hostnameN>
$ sudo system-docker restart docker
```

ここで、`<hostname *>`は、dockerホスト名として使用できるホスト名です。
`<hostname *>`はワイルドカードパターンにすることができます。
例："`*。*。*。*。*`"。
docker TLS接続をローカルでテストできるように、
`localhost` をホスト名の1つとして使用することをお勧めします。

これで、必要なサーバー証明書とキーファイルはすべて `/etc/docker/tls` ディレクトリに保存され、`docker` サービスは `--tlsverify` オプションで起動されました。

### Generate Client Certificates(クライアント証明書を生成する)

TCPソケット経由でDockerにアクセスするには、クライアント証明書とキーも必要です。

```
$ sudo ros tls gen
  INFO[0000] Out directory (-d, --dir) not specified, using default: /home/rancher/.docker
```

dockerクライアントのTLSファイルはすべて `~/.docker` ディレクトリにあります。

### Test docker TLS connection(docker TLS接続のテスト)

クライアント証明書を使用して、TCPを介してDockerにアクセスできるかどうかを確認できます。

```
$ docker --tlsverify version
```

必要なファイルはすべて `~/.docker` ディレクトリにあるため、`--tlscacert` `--tlscert` および `--tlskey` オプションを使用してそれらを指定する必要はありません。
localhostでDockerにアクセスするのに `-H` も必要ありません。

そこからRancherOSホストのDockerにアクセスする必要がある場合は、ファイルを　`/home/rancher/.docker`からクライアントマシンの `$HOME/.docker` にコピーします。

クライアントマシンで、Dockerホストを設定し、Dockerコマンドが機能するかどうかをテストします。

```
$ export DOCKER_HOST=tcp://<hostname>:2376 DOCKER_TLS_VERIFY=1
$ docker ps
```

