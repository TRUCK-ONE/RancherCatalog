# Running Commands

`runcmd` cloud-configディレクティブを使用して、起動時に実行中のコマンドを自動化できます。
コマンドはリストまたは文字列として指定できます。 後者の場合、コマンドは `sh` で実行されます。

```
#cloud-config
runcmd:
- [ touch, /home/rancher/test1 ]
- echo "test" > /home/rancher/test2
```

`runcmd` を使用して指定されたコマンドは、`コンソール`コンテナのコンテキスト内で実行されます。
`コンソール`コンテナで実行されるコマンドの順序に関する詳細は、[こちら](https://rancher.com/docs/os/v1.x/en/installation/boot-process/built-in-system-services/#console)にあります。

## Running Docker commands(Dockerコマンドを実行)

`runcmd` を使用する場合、RancherOSはDockerを起動する前にのコマンドが完了するのを待ちます。
結果として、`docker run` コマンドは `runcmd` の下に置かないでください。
代わって、`/etc/rc.local` を使用できます。
RancherOSはこのスクリプト内のコマンドが完了するのを待機しません。
そのため、`docker run` コマンドを実行する前に、`wait-for-docker` コマンドを使用してDockerデーモンが実行されていることを確認できます。

```
#cloud-config
rancher:
write_files:
  - path: /etc/rc.local
    permissions: "0755"
    owner: root
    content: |
      #!/bin/bash
      wait-for-docker
      docker run -d nginx
```

この方法でDockerコマンドを実行すると、`docker run` コマンドの一部が動的に生成されるときに便利です。
設定が静的なサービスには、[システムサービスを追加する](https://rancher.com/docs/os/v1.x/en/installation/system-services/adding-system-services/)ことをお勧めします。



