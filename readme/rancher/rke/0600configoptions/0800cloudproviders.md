# Cloud Providers

RKEはあなたのKubernetesクラスタにあなたの特定の[クラウドプロバイダ](https://kubernetes.io/docs/concepts/cluster-administration/cloud-providers/)を設定する機能をサポートします。
これらのクラウドプロバイダーには特定のクラウド構成があります。
クラウドプロバイダを有効にするには、その名前と必要な設定オプションを、クラスタYMLの `cloud_provider` ディレクティブの下に指定する必要があります。

- AWS
- Azure
- OpenStack
- vPhere

このリスト以外では、RKEは[カスタムクラウドプロバイダ](https://rancher.com/docs/rke/v0.1.x/en/config-options/cloud-providers/custom/)を処理する機能もサポートします。
