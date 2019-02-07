# Vagrant Quick Start

次の手順では、シングルノードクラスタを接続した状態でRancher Serverをすばやく展開します。

## 前提条件
- [Vagrant](https://www.vagrantup.com/)：Vagrantファイルに基づいてマシンをプロビジョニングするために使用されるため、Vagrantが必要です。
- [Virtualbox](https://www.virtualbox.org/)：Vagrantがプロビジョニングする仮想マシンはVirtualBoxにプロビジョニングする必要があります。
- 4GB以上の空きRAM

## 入門

1. `git clone` を使ってRancher Quickstartをフォルダにクローンする。
    ```
    https://github.com/rancher/quickstart.
    ```
1. `cd quickstart/vagrant` を実行して、Vagrantfile を含むフォルダに移動します。
1. **オプション：** config.yamlを次のように編集します。
    - 必要に応じて、ノード数とメモリ割り当てを変更します。（node.count、node.cpus、node.memory）
    - Rancherにログインするための管理ユーザーのパスワードを変更します。（default_password）
1. `vagrant up` で環境実行の作成を開始する。
1. プロビジョニングが完了したら、ブラウザで`https://172.22.101.101` にアクセスしてください。 デフォルトのユーザー/パスワードは `admin/admin`です。

**結果：** Rancher ServerとKubernetesクラスタがVirtualBoxにインストールされます。

### What’s Next?

展開を作成するには、Rancherを使用してください。 詳細については、配置の作成を参照してください。

## Destroying the Environment

1. フォルダ `quickstart/vagrant` から `vagrant destroy -f` を実行してください。
1. すべてのリソースが破壊されたという確認を待ちます。
