# Amazon AWS Quick Start

次の手順では、シングルノードクラスタを接続した状態でRancher Serverをすばやく展開します。

## 前提条件

>**注意：**
Amazon AWSにデプロイすると料金が発生します。

- [Amazon AWS Account](https://aws.amazon.com/jp/account/)：RancherとKubernetesをデプロイするためのリソースを作成するには、Amazon AWSアカウントが必要です。
- [Amazon AWS Access Key](https://docs.aws.amazon.com/ja_jp/general/latest/gr/managing-aws-access-keys.html)：まだ作成していない場合は、このリンクを使用してチュートリアルに従ってAmazon AWSアクセスキーを作成します。
- [Amazon AWS Key Pair](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/ec2-key-pairs.html)：このリンクを使用して指示に従ってキーペアを作成します。
- [Terraform](https://www.terraform.io/downloads.html)：Amazon AWSでサーバーとクラスターをプロビジョニングするために使用されます。

## 入門

1. `git clone` を使ってRancher Quickstartをフォルダにクローンする。
    ```
    https://github.com/rancher/quickstart
    ```
1. `cd quickstart/aws` を実行して、terraformファイルを含むAWSフォルダーに移動します。
1. ファイル `terraform.tfvars.example` の名前を`terraform.tfvars` に変更します。
1. `terraform.tfvars` を編集して、少なくとも以下の変数をカスタマイズします。ノード数とサイズを変更するには、ノードサイズを参照してください。
    - `aws_access_key`：Amazon AWS Access Key
    - `aws_secret_key`：Amazon AWS Secret Key
    - `ssh_key_name`：Amazon AWS Key Pair Name
    - `prefix`：Resource Prefix
1. **オプション：** terraform.tfvars内のさまざまなノードタイプの数を変更します。変数の詳細については、クイックスタートReadmeを参照してください。
1. `terraform init` を実行する
1. 環境の構築を開始するには、`terraform apply` を実行してください。その後、次の出力を待ちます。
    ```
    Apply complete! Resources: 3 added, 0     changed, 0 destroyed.
        Outputs: 
        rancher-url = [
            https://xxx.xxx.xxx.xxx
        ]
    ```
1. 上記の出力からrancher-urlをブラウザに貼り付けます。プロンプトが表示されたらログインします（デフォルトのパスワードはadminです）。

**結果：** Rancher ServerとKubernetesクラスターがAmazon AWSにインストールされます。

### What’s Next?

展開を作成するには、Rancherを使用してください。 詳細については、配置の作成を参照してください。

## Destroying the Environment

1. フォルダ `quickstart/aws` から `terraform destroy --auto-Approve` を実行します。
2. すべてのリソースが破壊されたことの確認を待ちます。
