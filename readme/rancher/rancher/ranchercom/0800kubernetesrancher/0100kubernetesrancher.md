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

クラスタでマルチテナンシーをサポートするには、さまざまな[プロジェクト](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/projects-and-namespaces/)を作成してください。
プロジェクトを使用すると、複数の[ネームスペース](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/projects-and-namespaces/#namespaces)を単一のオブジェクトにまとめることができます。
プロジェクトごとにユーザーアクセスおよびPodセキュリティポリシーを設定できます。
これにより、同じクラスターを使用しながら、ユーザーのグループが異なる名前空間のセットにアクセスできるようになります。
プロジェクトはRancherで利用可能な機能ですが、Kubernetesの基本バージョンではありません。

プロジェクトの管理方法について詳しくは、以下を参照してください。

- [Projects and Namespaces](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/projects-and-namespaces/)
- [Project Members](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/projects-and-namespaces/project-members/)

## Workloads

[ワークロード](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/workloads/)を使用してクラスターノードにアプリケーションをデプロイします。
ワークロードは、アプリケーションを実行するポッドと、デプロイの動作に関するルールを設定するメタデータを含むオブジェクトです。
ワークロードは、クラスタ全体の範囲内またはネームスペース内に配置できます。

ワークロードを展開するときは、任意のイメージから展開できます。
アプリケーションの実行方法を決定するためのさまざまな[ワークロードタイプ](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/workloads/#workload-types)があります。

ワークロードの配置後も、作業を続けることができます。 あなたはできる：

- ワークロードを実行中のアプリケーションの新しいバージョンに[アップグレード](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/workloads/upgrade-workloads/)します。
- アップグレード中に問題が発生した場合は、ワークロードを前のバージョンに[ロールバック](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/workloads/rollback-workloads/)します。
- [サイドカー](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/workloads/add-a-sidecar/)を追加します。これは、プライマリワークロードをサポートするワークロードです。

## Load Balancing and Ingress

### Load Balancers

アプリケーションを起動すると、そのアプリケーションはクラスタ内でしか使用できなくなります。
外部からはアクセスできません。

アプリケーションを外部からアクセス可能にする場合は、ロードバランサをクラスタに追加する必要があります。
ロードバランサーは、ユーザーがロードバランサーのIPアドレスとアプリケーションのポート番号を知っていれば、クラスタにアクセスするための外部接続用のゲートウェイを作成します。

Rancherは2種類のロードバランサをサポートしています。

- [Layer-4 Load Balancers](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/load-balancers-and-ingress/load-balancers/#layer-4-load-balancer)
- [Layer-7 Load Balancers](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/load-balancers-and-ingress/load-balancers/#layer-7-load-balancer)

詳しくは、[ロードバランサー](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/load-balancers-and-ingress/load-balancers/)を参照してください。

### Ingress

ロードバランサはサービスごとに1つのIPアドレスしか処理できません。
つまり、クラスタ内で複数のサービスを実行している場合は、サービスごとにロードバランサを用意する必要があります。
複数のロードバランサを実行すると、コストがかかる可能性があります。
あなたはイングレスを使ってこの問題を回避することができます。

入力は、ロードバランサとして機能する一連のルールです。
入力は1つ以上の入力コントローラと連携してサービス要求を動的にルーティングします。
入力が要求を受信すると、クラスタ内の入力コントローラがロードバランサーをプログラムして、設定したサービスサブドメインまたはパスルールに基づいて要求を正しいサービスに転送します。

詳しくは、[Ingress](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/load-balancers-and-ingress/ingress/)を参照してください。

## Service Discovery

ロードバランサーまたは入力、あるいはその両方を使用してクラスターを外部の要求に公開した後は、IPアドレスでしか利用できません。
解決可能なホスト名を作成するには、サービスレコードを作成する必要があります。
これは、IPアドレス、外部ホスト名、DNSレコードエイリアス、ワークロード、またはラベル付きポッドを特定のホスト名にマッピングするレコードです。

詳しくは、[Service Discovery](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/service-discovery/)を参照してください。

## Volumes and Storage

状態を保持する必要があるワークロードの場合は、ワークロード用の外部ストレージを追加する必要があります。
ストレージボリュームは、アプリケーションがデータを保存できる、ポッドの外側の場所です。
ストレージはワークロードの外部にあるため、コンテナーに障害が発生した場合、それを置き換えるコンテナーが外部データを復元でき、リカバリーがシームレスに見えます。

Rancher内では、次の2つの方法のいずれかを使用して永続ストレージを作成できます。

- Persistent Volumes  
    永続的ボリュームは事前にプロビジョニングされたストレージボリュームで、後で永続的ボリューム要求を使用してポッドにバインドできます。
- Storage Classes  
    ストレージクラスは、要求に応じてストレージボリュームをプロビジョニングするオブジェクトです。
    ポッドがストレージクラスに永続的なボリューム要求を送信すると、クラスはポッドのストレージボリュームを作成します。

ワークロードをデプロイした後は、[永続的なボリューム要求](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/volumes-and-storage/persistent-volume-claims/)を使用してストレージを要求します。
これは、クラスタ内で使用可能な記憶領域を要求するために使用される伝票のようなものです。

## Kubernetes Resources

Rancherプロジェクトまたは名前空間のコンテキスト内で、リソースはあなたのポッドの操作をサポートするファイルとデータです。
Rancher内では、証明書、レジストリ、および機密事項はすべてリソースと見なされます。
ただし、Kubernetesはリソースをさまざまな種類の[秘密](https://kubernetes.io/docs/concepts/configuration/secret/)として分類します。
したがって、単一のプロジェクトまたはネームスペース内では、競合を避けるために個々のリソースに一意の名前を付ける必要があります。
リソースは主に機密情報の伝達に使用されますが、他の用途もあります。

リソースが含まれます：
- [Certificates](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/certificates/)：クラスタに出入りするデータの暗号化/復号化に使用されるファイル。
- [ConfigMaps](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/configmaps/)：設定ファイルのグループなど、一般的な設定情報を格納するファイル。
- [Secrets](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/secrets/)：パスワード、トークン、キーなどの機密データを格納するファイル。
- [Registries](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/registries/)：プライベートレジストリによる認証に使用される資格情報を含むファイル。

