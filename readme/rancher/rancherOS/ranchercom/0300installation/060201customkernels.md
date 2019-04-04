# Custom Kernels

## Kernel version in RancherOS

RancherOSは基本的に標準のLinuxカーネルを使用しますが、私たちは自分自身でカーネル設定を管理します。
さまざまな機能サポートとセキュリティ修正により、カーネルのバージョンを常に更新しています。

| RancherOS | Kernel |
| --- | --- |
| <=v0.7.1 | 4.4.x |
| <=v1.3.0 | 4.9.x |
| >=v1.4.0 | 4.14.x |

# Building and Packaging a Kernel to be used in RancherOS

RancherOS用のカーネルは [os-kernelリポジトリ](https://github.com/rancher/os-kernel) で構築します。
このリポジトリを使用して、RancherOSで使用するための独自のカスタムカーネルをパッケージ化することができます。

`git clone` を使用して、ローカルマシンに [os-kernel](https://github.com/rancher/os-kernel) リポジトリのクローンを作成します。

```
$ git clone https://github.com/rancher/os-kernel.git
```

カーネルv4.14.53を構築したい場合は、次のコマンドを参照することができます。
構築が完了した後、新しく構築されたカーネルのtarballとヘッダを使って `./dist/kernel` ディレクトリが作成されます。

```
$ git tag v4.14.53-rancher
$ KERNEL_TAG=4.14.53 make release
...snip...
./dist/kernel/extra-linux-4.14.53-rancher-x86.tar.gz
./dist/kernel/build-linux-4.14.53-rancher-x86.tar.gz
./dist/kernel/linux-4.14.53-rancher-x86.tar.gz
./dist/kernel/config
...snip...
Images ready to push:
rancher/os-extras:4.14.53-rancher
rancher/os-headers:4.14.53-rancher
```

カスタムカーネルが必要なユーザーにとっては、次の情報が非常に役立ちます。

1. `modules.list` で定義されたモジュールは組み込みモジュールにパッケージされます。

1. `modules-extra.list` に定義されているモジュールは、追加のモジュールにまとめられます。

1. 必要なカーネルモジュールを構築するために `config/kernel-config` を修正することができます。

1. パッチディレクトリに `パッチ` を追加することができます、そして `os-kernel` はカーネルソースをダウンロードした後にこれらのパッチを更新します。

今、あなたはどこかに `./dist/kernel/linux-4.14.53-rancher-x86.tar.gz` ファイルをアップロードするか、または `assets/kernel.tar.gz` として `rancher/os` リポジトリのあなたのクローンにそのファイルをコピーする必要があります。

`build-<name>.tar.gz` および `extra-<name>.tar.gz` ファイルは、RancherOSリリース用の `rancher/os-extras` および rancher/os-headers` イメージをビルドするために使用されます - それらにタグを付ける必要があります。
別の組織名で、それらをレジストリにプッシュして、カスタムservice.ymlファイルを作成してください。

あなたのカーネルは次のフォーマットのファイルのセットとしてパッケージ化され公開されるべきです。


---

以下略


