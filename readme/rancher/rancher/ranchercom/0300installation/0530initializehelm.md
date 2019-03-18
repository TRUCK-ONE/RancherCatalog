# 3.Initialize Helm (Install Tiller)

HelmはKubernetesに最適なパッケージ管理ツールです。
Helm charts はKubernetes YAMLマニフェストドキュメントのテンプレート構文を提供します。
Helmを使えば、静的ファイルを使う代わりに設定可能なデプロイメントを作成することができます。
独自の展開カタログの作成に関する詳細は、[https://helm.sh/](https://helm.sh/) にあるドキュメントをご覧ください。
Helmを使用できるようにするには、サーバー側のコンポーネントティラーをクラスタにインストールする必要があります。

> **Note：**  
> インターネットに直接アクセスできないシステムについては、インストールの詳細について [Helm - Air Gap](https://rancher.com/docs/rancher/v2.x/en/installation/air-gap-high-availability/install-rancher/) を参照してください。

## Install Tiller on the Cluster

> **重要：**  
> Helm v2.12.0およびcert-managerの問題のため、Helm v2.12.1以降を使用してください。

Helmはチャートを管理するために `tiller` サービスをクラスタにインストールします。
RKEはデフォルトでRBACを有効にするので、`kubectl` を使用して `serviceaccount` と `clusterrolebinding` を作成する必要があります。
そのため、`tiller`はクラスターにデプロイする権限を持っています。

- `kube-system` ネームスペースに `ServiceAccount` を作成します。
-  `tiller` アカウントにクラスタへのアクセス権を付与するための `ClusterRoleBinding` を作成します。
- 最後に `helm` を使って `tiller` サービスをインストールします。

```
kubectl -n kube-system create serviceaccount tiller

kubectl create clusterrolebinding tiller \
  --clusterrole=cluster-admin \
  --serviceaccount=kube-system:tiller

helm init --service-account tiller

# Users in China: You will need to specify a specific tiller-image in order to initialize tiller. 
# The list of tiller image tags are available here: https://dev.aliyun.com/detail.html?spm=5176.1972343.2.18.ErFNgC&repoId=62085. 
# When initializing tiller, you'll need to pass in --tiller-image

helm init --service-account tiller \
--tiller-image registry.cn-hangzhou.aliyuncs.com/google_containers/tiller:<tag>
```

> **Note：**   
> この `tiller` インストールにはフルクラスタアクセスがあります。
クラスタがRancherサーバ専用の場合は、これを使用できます。
セキュリティ要件に合わせて `tiller` アクセスを制限するための[helmのドキュメント](https://helm.sh/docs/using_helm/#role-based-access-control)をチェックしてください。

## Test your Tiller installation

次のコマンドを実行して、クラスタに `tiller` がインストールされていることを確認します。

```
kubectl -n kube-system  rollout status deploy/tiller-deploy
Waiting for deployment "tiller-deploy" rollout to finish: 0 of 1 updated replicas are available...
deployment "tiller-deploy" successfully rolled out
```

そして、以下のコマンドを実行して Helm が `tiller` サービスと通信できることを確認します。

```
helm version
Client: &version.Version{SemVer:"v2.12.1", GitCommit:"02a47c7249b1fc6d8fd3b94e6b4babf9d818144e", GitTreeState:"clean"}
Server: &version.Version{SemVer:"v2.12.1", GitCommit:"02a47c7249b1fc6d8fd3b94e6b4babf9d818144e", GitTreeState:"clean"}
```

## Issues or errors?

[トラブルシューティングページ](https://rancher.com/docs/rancher/v2.x/en/installation/ha/helm-init/troubleshooting/)を参照してください。

## [Next: Install Rancher](https://rancher.com/docs/rancher/v2.x/en/installation/ha/helm-rancher/)

