# Images prefix

v1.3以降で利用可能

独自のDockerレジストリを構築し、`rancher/os` および他の `os-services` イメージをキャッシュした場合は、通常の `docker pull rancher/os` のようなものを` docker pull dockerhub.mycompanyname.com/docker.io/rancher/os `としてキャッシュすることができます。

ただし、インストールやサービスのためにRancherOSにプレフィックスを挿入する方法が必要です。
RancherOSはROSが常にあなたのミラーを使用するように強制するために追加できるグローバルプレフィックスをサポートします。

グローバルイメージプレフィックスを設定できます。

```
ros config set rancher.environment.REGISTRY_DOMAIN xxxx.yyy
```

それからあなたはosリストをチェックします：

```
$ ros os list
xxxx.yyy/rancher/os:v1.3.0 remote latest running
xxxx.yyy/rancher/os:v1.2.0 remote available
...
...
```

それからあなたはosリストをチェックします：

```
$ ros console switch ubuntu
Switching consoles will
1. destroy the current console container
2. log you out
3. restart Docker
Continue [y/N]: y
Pulling console (xxxx.yyy/rancher/os-ubuntuconsole:v1.3.0)...
...
```

この設定をリセットしたい場合は、

```
ros config set rancher.environment.REGISTRY_DOMAIN docker.io
```

