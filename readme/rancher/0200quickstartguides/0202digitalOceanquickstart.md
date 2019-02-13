# DigitalOcean Quick Start

次の手順では、シングルノードクラスタを接続した状態でRancher Serverをすばやく展開します。

## 前提条件

> **注意：**
DigitalOceanにデプロイすると料金が発生します。

- [DigitalOcean Account](https://www.digitalocean.com/)：サーバーとクラスタが実行される場所であるため、DigitalOceanのアカウントが必要になります。

- [DigitalOcean Access Key](https://www.digitalocean.com/community/tutorials/how-to-create-a-digitalocean-space-and-api-key)：まだ持っていない場合は、このリンクを使用してDigitalOceanアクセスキーを作成してください。

- [Terraform](https://www.terraform.io/downloads.html)：サーバーとクラスターをDigitalOceanにプロビジョニングするために使用されます。

## 入門

1. `git clone` を使ってRancher Quickstartをフォルダにクローンする。

    ```
    https://github.com/rancher/quickstart
    ```

1. `cd quickstart/do` を実行して、Terraformファイルを含むDigitalOceanフォルダーに移動します。

1. ファイル `terraform.tfvars.example` の名前を `terraform.tfvars` に変更します。

1. DigitalOcean Access Key を含めるために `terraform.tfvars` を編集してください。

1. **Option：** `terraform.tfvars` を次のように編集します。
    - ノード数を変更する。（count_agent_all_nodes）
    - Rancherにログインするための管理ユーザーのパスワードを変更します。（admin_password）

1. `terraform init` を実行してください。

1. 環境の構築を開始するには、`terraform apply` を実行してください。
その後、以下の出力を待ちます。

    ```
    Apply complete! Resources: 2 added, 0 changed, 0 destroyed. 
        Outputs: 
        rancher-url = [ 
          https://xxx.xxx.xxx.xxx 
        ]
    ```

1. 上記の出力からrancher-urlをブラウザに貼り付けます。 
プロンプトが表示されたらログインします（デフォルトのパスワードはadminです）。

**結果：** Rancher ServerとKubernetesクラスタがDigitalOceanにインストールされました。

### What’s Next?

展開を作成するには、Rancherを使用してください。 詳細については、配置の作成を参照してください。

## Destroying the Environment

1. フォルダ `quickstart/do` から `terraform destroy --force` を実行します。

1. すべてのリソースが破壊されたことの確認を待ちます。

