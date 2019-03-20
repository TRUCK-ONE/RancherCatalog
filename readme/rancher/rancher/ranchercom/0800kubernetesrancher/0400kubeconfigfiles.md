# Kubeconfig Files

kubeconfigファイルは、kubectlコマンドラインツール（または他のクライアント）と組み合わせて使用したときにKubernetesへのアクセスを設定するために使用されるファイルです。

kubeconfigとkubectlの連携方法の詳細については、[Kubernetesのドキュメント](https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/)を参照してください。

Rancher GUIを使用してクラスターを作成すると、Rancherは自動的にクラスター用のkubeconfigを作成します。

このkubeconfigファイルとその内容は、表示しているクラスターに固有のものです。
Rancherでアクセスできる各クラスタには、別々のkubeconfigファイルが必要です。

詳細については、[kubectlを使用してクラスタにアクセスする](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/kubectl/)を参照してください。

> **Note：**
> デフォルトでは、kubectlはkubeconfigファイルの`~/.kube/config` をチェックしますが、`--kubeconfig` フラグを使用して任意のディレクトリを使用できます。
> 例えば：
> ```
> kubectl --kubeconfig /custom/path/kube.config get pods
> ```

