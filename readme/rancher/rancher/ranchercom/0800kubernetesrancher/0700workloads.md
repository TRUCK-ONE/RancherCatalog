# Workloads

ポッドとワークロードという2つの基本的な構成要素を使用して、Kubernetesで複雑なコンテナ化アプリケーションを構築できます。
アプリケーションを作成したら、3つめの構成要素であるservicesを使用して、同じクラスター内またはインターネット上のアクセス用に公開することができます。

## Pods

[ポッド](https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/)は、ネットワークネームスペースとストレージボリュームを共有する1つ以上のコンテナです。
ほとんどのポッドにはコンテナが1つしかありません。
したがって、ポッドについて説明するとき、この用語はしばしばコンテナと同義です。
サービスを実装する同じポッドの複数のインスタンスを持つことで、コンテナをスケールするのと同じ方法でポッドをスケールします。
通常、ポッドはワークロードによって拡張および管理されます。

## Workloads

ワークロードは、ポッドの配置規則を設定するオブジェクトです。
これらのルールに基づいて、Kubernetesはデプロイメントを実行し、アプリケーションの現在の状態でワークロードを更新します。
ワークロードを使用すると、アプリケーションのスケジューリング、拡張、およびアップグレードに関する規則を定義できます。

### Workload Types

Kubernetesはワークロードをさまざまなタイプに分けます。
Kubernetesでサポートされている最も一般的なタイプは以下のとおりです。

- [Deployments](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/)

    展開は、ステートレスなアプリケーション（つまり、ワークロードの状態を維持する必要がない場合）に最も適しています。
    展開ワークロードによって管理されるポッドは、独立して使い捨てとして扱われます。
    ポッドが混乱した場合、Kubernetesはそれを削除してから再作成します。
    アプリケーションの例としては、Nginx Webサーバーがあります。

- [StatefulSets](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/)

    配備とは対照的に、StatefulSetsは、アプリケーションがそのIDを維持し、データを格納する必要がある場合に最適に使用されます。
    アプリケーションはZookeeperのようなものです - ストレージ用のデータベースを必要とするアプリケーションです。

- [DaemonSets](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/)

    デーモンセットは、クラスタ内のすべてのノードが確実にpodのコピーを実行するようにします。
    ログを収集したりノードのパフォーマンスを監視したりするユースケースでは、このデーモン風のワークロードが最適に機能します。

- [Jobs](https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/)

    ジョブは1つ以上のポッドを起動し、指定された数のポッドが正常に終了するようにします。
    ジョブは、進行中の望ましいアプリケーション状態を管理するのではなく、有限のタスクを完了するまで実行するのに最適です。

- [CronJobs](https://kubernetes.io/docs/concepts/workloads/controllers/cron-jobs/)

    CronJob は Jobs に似ています。
    ただし、CronJobsはcronベースのスケジュールで完了するまで実行されます。

## Services

多くのユースケースでは、ワークロードは次のいずれかになります。

- クラスター内の他のワークロードによってアクセスされます。
- 外の世界にさらされています。

サービスを作成することでこれらの目標を達成できます。
サービスは、[セレクター/ラベルアプローチを使用して、基盤となるワークロードのポッドにマッピングされます（コードサンプルを参照）](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#service-and-replicationcontroller)。
Rancher UIは、選択したサービスポートと種類を使用して、ワークロードと共にサービスを自動的に作成することによって、このマッピングプロセスを簡素化します。

### Service Types

Rancher で利用可能なサービスのいくつかの種類があります。
以下の説明は [Kubernetesのドキュメント](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types)から引用しています。

- **ClusterIP**
    > サービスをクラスタ内部IPに公開します。
    > この値を選択すると、サービスはクラスタ内からのみ到達可能になります。
    > これがデフォルトの`ServiceType`です。

- **NodePort**
    > 静的ポート（`NodePort`）で各ノードのIP上のサービスを公開します。
    > `NodePort` サービスのルーティング先となる`ClusterIP` サービスが自動的に作成されます。
    > `<NodeIP>:<NodePort>` をリクエストして、クラスタの外から `NodePort`サービスに連絡することができます。

- **LoadBalancer**
    > クラウドプロバイダーのロードバランサーを使用してサービスを外部に公開します。
    > 外部ロードバランサがルーティングする `NodePort` および `ClusterIP` サービスは自動的に作成されます。

## Workload Options

資料のこのセクションには、ワークロードのデプロイおよびワークロード・オプションの使用に関する説明が含まれています。

- [Deploy Workloads](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/workloads/deploy-workloads/)
- [Upgrade Workloads](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/workloads/upgrade-workloads/)
- [Rollback Workloads](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/workloads/rollback-workloads/)

## Related Links(関連リンク)

### External Links(外部リンク)

- [Services](https://kubernetes.io/docs/concepts/services-networking/service/)


