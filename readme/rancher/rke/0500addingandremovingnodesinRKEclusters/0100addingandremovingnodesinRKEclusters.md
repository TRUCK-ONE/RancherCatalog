# Adding and Removing Nodes in RKE Clusters(RKEクラスタでのノードの追加と削除)

## Adding/Removing Nodes(ノードの追加/削除)

RKEは、ワーカーホストとコントロールプレーンホストの[ノード](https://rancher.com/docs/rke/v0.1.x/en/config-options/nodes/)の追加/削除をサポートします。

追加のノードを追加するには、元の `cluster.yml` ファイルを追加のノードで更新し、Kubernetesクラスタでそれらの役割を指定します。

ノードを削除するには、元の `cluster.yml` のノードリストからノード情報を削除します。

ノードを追加/削除するための変更を加えたら、更新された `cluster.yml` を使って `rke up` を実行してください。

## Adding/Removing Worker Nodes(ワーカーノードの追加/削除)

`rke up --update-only` を実行することで、ワーカーノードのみを追加/削除できます。
これは全てのワーカーノードを除いて `cluster.yml` の他の全てを無視します。

## Removing Kubernetes Components from Nodes(ノードからKubernetesコンポーネントを削除する)

ノードから Kubernetesコンポーネント を削除するには、`rke remove` コマンドを使用します。

> **Note：**  
> このコマンドは元に戻すことができず、Kubernetesクラスタを破壊します。

このコマンドは `cluster.yml` の各ノードに対して次のことを行います：

- デプロイされているKubernetesコンポーネントを削除します。
    - `etcd`
    - `kube-apiserver`
    - `kube-controller-manager`
    - `kubelet`
    - `kube-proxy`
    - `nginx-proxy`

> **Note：**  
> ポッドはノードから削除されません。 ノードを再利用すると、新しいKubernetesクラスタが作成されたときにポッドが自動的に削除されます。

- サービスによって残されたディレクトリから各ホストを削除します。
    - `/etc/kubernetes/ssl`
    - `/var/lib/etcd`
    - `/etc/cni`
    - `/opt/cni`
    - `/var/run/calico`

