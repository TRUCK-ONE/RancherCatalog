# SSH Connectivity Errors

## Failed to set up SSH tunneling for host [xxx.xxx.xxx.xxx]: Can’t retrieve Docker Info

### Failed to dial to /var/run/docker.sock: ssh: rejected: administratively prohibited (open failed)

- 接続するように指定されたユーザーにはDockerソケットにアクセスする権限がありません。
これは、ホストにログインしてコマンド `docker ps` を実行することで確認できます。
    ```
    $ ssh -i ssh_privatekey_file user@server
    user@server$ docker ps
    CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
    ```

これを適切に設定する方法については、[Dockerをroot以外のユーザーとして管理する](https://docs.docker.com/install/linux/linux-postinstall/#manage-docker-as-a-non-root-user)を参照してください。

- オペレーティングシステムとして RedHat/CentOS を使用している場合、[Bugzilla＃1527565](https://bugzilla.redhat.com/show_bug.cgi?id=1527565) のため、 `root` ユーザーを使用してノードに接続することはできません。
別のユーザーを追加してDockerソケットにアクセスするように設定する必要があります。
これを適切に設定する方法については、[Dockerをroot以外のユーザーとして管理する](https://docs.docker.com/install/linux/linux-postinstall/#manage-docker-as-a-non-root-user)を参照してください。
- SSHサーバーのバージョンがバージョン6.7以降ではありません。
これは、ソケット転送が機能するために必要です。これは、SSHを介してDockerソケットに接続するために使用されます。
これは、接続しているホストの `sshd -V`、またはnetcatを使って確認できます。
    ```
    $ nc xxx.xxx.xxx.xxx 22
    SSH-2.0-OpenSSH_6.6.1p1 Ubuntu-2ubuntu2.10
    ```

### Failed to dial ssh using address [xxx.xxx.xxx.xxx:xx]: Error configuring SSH: ssh: no key found

- `ssh_key_path` として指定された鍵ファイルにアクセスできません。
秘密鍵ファイル（公開鍵 `.pub` ではなく）を指定したこと、および `rke` コマンドを実行しているユーザーが秘密鍵ファイルにアクセスできることを確認してください。
- `ssh_key_path` として指定されたキーファイルが不正です。
`ssh-keygen -y -e -f private_key_file` を実行して、鍵が有効かどうかを確認してください。
これは秘密鍵の公開鍵を表示します。
秘密鍵ファイルが無効な場合は失敗します。

### Failed to dial ssh using address [xxx.xxx.xxx.xxx:xx]: ssh: handshake failed: ssh: unable to authenticate, attempted methods [none publickey], no supported methods remain

- `ssh_key_path` として指定された鍵ファイルは、ノードへのアクセスには正しくありません。
ノードに正しい `ssh_key_path` を指定したかどうか、および接続先の正しいユーザーを指定したかどうかを再確認してください。

### Failed to dial ssh using address [xxx.xxx.xxx.xxx:xx]: Error configuring SSH: ssh: cannot decode encrypted private keys

- 暗号化された秘密鍵を使いたい場合は、`ssh-agent` を使って自分の鍵をパスフレーズと一緒に読み込む必要があります。
コマンドラインで `--ssh-agent-auth` を指定することで、そのエージェントを使用するようにRKEを設定できます。
`rke` コマンドが実行される環境では、 `SSH_AUTH_SOCK` 環境変数が使用されます。

### Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?

- ノードは設定された `アドレス` と `ポート` に到達できません。


