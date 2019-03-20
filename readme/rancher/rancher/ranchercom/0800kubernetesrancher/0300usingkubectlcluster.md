# Using kubectl to Access a Cluster

2つの方法でkubectlを使用してKubernetesクラスタにアクセスし管理することができます。

- [kubectlシェルを使用したクラスターへのアクセス](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/kubectl/#accessing-clusters-with-kubectl-shell)
- [kubectl CLIとkubeconfigファイルを使用したクラスタへのアクセス](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/kubeconfig/)

## Resources created using kubectl

Rancherは `kubectl` によって作成されたリソースを発見して表示します。
ただし、これらのリソースには、発見に必要なすべての注釈が含まれているとは限りません。
Rancher UI/APIを使用してリソースに対して操作（ワークロードのスケーリングなど）が行われると、アノテーションが欠落しているためにリソースの再作成が引き起こされる可能性があります。
これは、ディスカバーされたリソースに対して初めて操作が行われたときにのみ発生します。

## Accessing Clusters with kubectl Shell

Rancherにログインしてkubectlシェルを開くことで、クラスターにアクセスして管理することができます。
それ以上の設定は必要ありません。

1. **グローバル**ビューから、kubectlでアクセスしたいクラスターを開きます。

1. kubectlを起動をクリックします。
Kubernetesクラスタと対話するために開くウィンドウを使用してください。

kubectlの使用方法の詳細については、[Kubernetes Documentation：kubectlの概要](https://kubernetes.io/docs/reference/kubectl/overview/)を参照してください。

## Accessing Clusters with kubectl and a kubeconfig File

あるいは、ワークステーションにkubectlをインストールしてから、それをRancherによって自動的に生成されたkubeconfigファイルに転送することによって、クラスターにアクセスすることもできます。
インストールと設定が完了したら、Rancherにログインしなくてもクラスタにアクセスできます。

1. ワークステーションにkubectlをインストールしてください。
詳細については、[Kubernetes Documentation：kubectlのインストールと設定](https://kubernetes.io/docs/tasks/tools/install-kubectl/)を参照してください。

1. Rancherにログインしてください。
**グローバル**ビューから、kubectlでアクセスしたいクラスターを開きます。

1. クラスタのkubeconfigファイルをワークステーションにコピーします。

    1. **Kubeconfigファイル**をクリックします。
    1. 表示されている内容をクリップボードにコピーします。
    1. ローカルコンピュータの新しいファイルに内容を貼り付けます。
    ファイルを `~/.kube/config` に移動します。
        > **Note：**
        > kubectlがkubeconfigファイルに使用するデフォルトの場所は `~/.kube/config` ですが、以下のサンプルのように、任意のディレクトリを使用して `--kubeconfig`フラグを使用して指定することができます。
        > ```
        > kubectl --kubeconfig /custom/path/kube.config get pods
        > ```

1. ワークステーションからkubectlを起動します。
あなたのkubernetesクラスタと対話するためにそれを使ってください。


kubectlの使用方法の詳細については、[Kubernetes Documentation：kubectlの概要](https://kubernetes.io/docs/reference/kubectl/overview/)を参照してください。

