## 依存関係を使用する運用上の側面

上記のセクションでチャートの依存関係を指定する方法について説明しましたが、これは helm install および helm upgrade を使用したチャートのインストールにどのように影響しますか？

"A"という名前のチャートが次のKubernetesオブジェクトを作成するとします。

* namespace "A-Namespace"
* statefulset "A-StatefulSet"
* service "A-Service"

さらに、Aはオブジェクトを作成するチャートBに依存しています

* namespace "B-Namespace"
* replicaset "B-ReplicaSet"
* service "B-Service"

チャートAのインストール/アップグレード後に、単一のHelmリリースが作成/変更されます。
このリリースでは、上記のKubernetesオブジェクトすべてを次の順序で作成または更新します。

* A-Namespace
* B-Namespace
* A-StatefulSet
* B-ReplicaSet
* A-Service
* B-Service

これは、Helmがチャートをインストール/アップグレードすると、チャートからのKubernetesオブジェクトとそのすべての依存関係が失われるためです。

* 単一のセットにまとめた。 それから
* 名前の後にタイプ別にソート。 その後
* この順序で作成/更新された。

そのため、チャートのすべてのオブジェクトとその依存関係を使用して単一のリリースが作成されます。

Kubernetes型のインストール順序は、kind_sorter.goの列挙InstallOrderによって与えられます（参照：[Helmソースファイル](https://github.com/helm/helm/blob/master/pkg/tiller/kind_sorter.go#L26)）。
