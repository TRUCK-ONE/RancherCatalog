# Kubernetes in Rancher

Rancherでクラスターをプロビジョニングした後は、強力なKubernetes機能を使用して、開発環境、テスト環境、または実稼働環境でコンテナー化アプリケーションをデプロイおよび拡張することができます。

## Interacting with Clusters
- Rancher UI  
    Rancherはあなたのクラスタとやり取りするための直感的なユーザーインターフェースを提供します。UIで利用可能なすべてのオプションはRancher APIを使用します。
    したがって、UIで可能なアクションはすべてRancher CLIまたはRancher APIでも可能です。
- kubectl  
    Kubernetesコマンドラインツールのkubectlを使ってクラスタを管理できます。
    kubectlを使用する方法は2つあります。
    - Rancher kubectl shell  
    Rancher UIで利用可能なkubectlシェルを起動して、クラスターと対話します。
    このオプションでは、ユーザー側で設定作業は不要です。
    詳しくは、kubectlシェルを使用したクラスターへのアクセスを参照してください。
    - Terminal remote connection  
    ローカルデスクトップにkubectlをインストールしてから、クラスタのkubeconfigファイルをローカルの `〜/.kube/config` ディレクトリにコピーすることで、クラスタと対話することもできます。

    詳しくは、kubectlおよびkubeconfigファイルを使用したクラスターへのアクセスを参照してください。
- Rancher CLI  
    Rancher独自のコマンドラインインターフェイスRancher CLIをダウンロードすることで、クラスタを制御できます。
    このCLIツールは、さまざまなクラスターやプロジェクトと直接対話したり、それらにkubectlコマンドを渡したりすることができます。
- Rancher API  
    最後に、Rancher APIを介してクラスターと対話することができます。
    APIを使用する前に、APIキーを入手する必要があります。
    APIオブジェクトのさまざまなリソースフィールドとアクションを表示するには、API UIを開きます。
    これには、任意のRancher UIオブジェクトの **[View in API]** をクリックしてアクセスできます。

## Editing Clusters

クラスタを起動した後は、最初の起動時に設定した設定の多くを編集できます。
すべてのクラスタで、クラスタメンバーシップを編集できます。
これは、クラスタにアクセスできるユーザのプールであり、クラスタ内の役割も一緒です。
詳しくは、[メンバー](https://rancher.com/docs/rancher/v2.x/en/admin-settings/rbac/cluster-project-roles/#membership-and-role-assignment)と[役割](https://rancher.com/docs/rancher/v2.x/en/admin-settings/rbac/cluster-project-roles/#project-roles)を参照してください。

クラスターのプロビジョニング方法に応じて、サイズとオプションを編集するためのさまざまな設定が利用可能です。

- ホステッドkubernetesプロバイダーにプロビジョニングされたクラスターの場合は、クラスタープロビジョニング中に選択したオプションを編集できます。
これらのオプションは、[ホストされているKubernetesプロバイダ](https://rancher.com/docs/rancher/v2.x/en/cluster-provisioning/hosted-kubernetes-clusters/)によって異なります。
- [Rancher Launched Kubernetes](https://rancher.com/docs/rancher/v2.x/en/cluster-provisioning/rke-clusters/) クラスターの場合は、以下のオプションを編集できます。
    - 以下を含む [Kubernetesのオプション](https://rancher.com/docs/rancher/v2.x/en/cluster-provisioning/rke-clusters/options/)：
        - インストールされているKubernetesのバージョン。
        - サポートされていないバージョンのDockerの実行をクラスターが許可するかどうか。
        - クラスタがクラウドプロバイダを使用しているかどうか。
        - クラスタがポッドセキュリティポリシーを適用するかどうか。

        > **Note：**  
        > 初回起動後は、クラスタのネットワークプロバイダを編集できません。

    - [**ノードプール**](https://rancher.com/docs/rancher/v2.x/en/cluster-provisioning/rke-clusters/node-pools/#node-pools)または[**カスタムノード**](https://rancher.com/docs/rancher/v2.x/en/cluster-provisioning/rke-clusters/custom-nodes/)クラスタのみ：ノードの規模と[役割](https://rancher.com/docs/rancher/v2.x/en/cluster-provisioning/#kubernetes-cluster-node-components)。
        - ノードプールの場合、クラスタノードプールを追加/削除/編集できます。
        ノードプールは特定のスケールに設定されているため、プールのスケールを変更しない限り、ノードを個別に削除してもクラスタのサイズは変わりません。
        - カスタムノードの場合、ノードを追加するためのDockerコマンドが提供されていますが、このコマンドを使用して既存のノードの役割を編集することもできます。
        ノードを削除するには、クラスタの**Nodes**ページを参照して不要なノードを削除します。

クラスタで利用可能な設定を表示するには、[クラスタの編集](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/editing-clusters/)を参照してください。

## Projects and Namespaces



---

以下略