# Kubernetes Default Services

Kubernetesをデプロイするために、RKEはノード上のDockerコンテナーにいくつかのコアコンポーネントまたはサービスをデプロイします。
ノードの役割に基づいて、デプロイされるコンテナーは異なります。

すべてのサービスは、[追加のカスタム引数、Dockerマウントバインド、および追加の環境変数](https://rancher.com/docs/rke/v0.1.x/en/config-options/services/services-extras/)をサポートしています。

## etcd

Kubernetesはクラスタの状態とデータの格納場所として [etcd](https://github.com/etcd-io/etcd/blob/master/Documentation/docs.md) を使用します。
Etcdは、信頼性があり、一貫性があり、分散されたKey-Valueストアです。

RKEは、シングルノードモードまたはHAクラスタモードでのetcdの実行をサポートしています。
クラスタへのetcdノードの追加と削除もサポートします。

デフォルトでは、RKEは新しいetcdサービスをデプロイしますが、[外部のetcdサービス](https://rancher.com/docs/rke/v0.1.x/en/config-options/services/external-etcd/)を使ってKubernetesを実行することもできます。

## Kubernetes API Server

> Rancher 2ユーザーへの注意  
> [Rancher Launched Kubernetes](https://rancher.com/docs/rancher/v2.x/en/cluster-provisioning/rke-clusters/) を作成するときに[設定ファイル](https://rancher.com/docs/rancher/v2.x/en/cluster-provisioning/rke-clusters/options/#config-file)を使用してクラスタオプションを設定する場合は、サービス名にアンダースコアのみを含める必要があります：`kube_api` 。
> これはRancher v2.0.5とv2.0.6にのみ適用されます。

[Kubernetes API](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-apiserver/) RESTサービス、すべてのKubernetesオブジェクトに対する要求とデータを処理し、他のすべてのKubernetesコンポーネントに対して共有状態を提供します。

```
services:
  kube-api:
    # IP range for any services created on Kubernetes
    # This must match the service_cluster_ip_range in kube-controller
    service_cluster_ip_range: 10.43.0.0/16
    # Expose a different port range for NodePort services
    service_node_port_range: 30000-32767    
    pod_security_policy: false
```

### Kubernetes API Server Options

RKEは、`kube-api` サービスに対して次のオプションをサポートしています。

- **サービスクラスタIP範囲**（`service_cluster_ip_range`） - これはKubernetesで作成されたサービスに割り当てられる仮想IPアドレスです。
デフォルトでは、サービスクラスタのIP範囲は`10.43.0.0/16` です。
この値を変更した場合は、Kubernetes Controller Manager（`kube-controller`）でも同じ値に設定する必要があります。
- **ノードポート範囲**（`service_node_port_range`） - [type](https://kubernetes.io/docs/concepts/services-networking/service/#publishing-services-service-types) `NodePort` で作成されたKubernetesサービスに使用されるポート範囲。
デフォルトでは、ポート範囲は `30000〜32767` です。
- **ポッドセキュリティポリシー**（`pod_security_policy`） -  [Kubernetesポッドセキュリティポリシー](https://kubernetes.io/docs/concepts/policy/pod-security-policy/)を有効にするためのオプション。
デフォルトでは、`false` に設定されているため、ポッドセキュリティポリシーを有効にしません。

> **Note：**  
> `pod_security_policy` 値を `true` に設定した場合、RKEはすべてのポッドがクラスター上で動作できるようにオープンポリシーを構成します。
> PSPを最大限に活用するには、独自のポリシーを設定する必要があります。

## Kubernetes Controller Manager

> Rancher 2ユーザーへの注意  
> [Rancher Launched Kubernetes](https://rancher.com/docs/rancher/v2.x/en/cluster-provisioning/rke-clusters/) を作成するときに[設定ファイル](https://rancher.com/docs/rancher/v2.x/en/cluster-provisioning/rke-clusters/options/#config-file)を使用してクラスタオプションを設定する場合は、サービスの名前にアンダースコアのみを含める必要があります：`kube_controller`。
> これはRancher v2.0.5とv2.0.6にのみ適用されます。

[Kubernetes Controller Manager](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-controller-manager/) サービスは、Kubernetesのメイン制御ループを実行するためのコンポーネントです。
コントローラマネージャは、Kubernetes APIサーバーを介してクラスタの望ましい状態を監視し、現在の状態に必要な変更を加えて望ましい状態にします。

```
services:
    kube-controller:
      # CIDR pool used to assign IP addresses to pods in the cluster
      cluster_cidr: 10.42.0.0/16
      # IP range for any services created on Kubernetes
      # This must match the service_cluster_ip_range in kube-api
      service_cluster_ip_range: 10.43.0.0/16
```

### Kubernetes Controller Manager Options

RKEは、kube-controllerサービスに対して以下のオプションをサポートします。

- **クラスタCIDR**（`cluster_cidr`） - クラスタ内のポッドにIPアドレスを割り当てるために使用されるCIDRプール。
デフォルトでは、クラスタ内の各ノードには、Pod IP割り当て用にこのプールから `/24` ネットワークが割り当てられています。
このオプションのデフォルト値は `10.42.0.0/16` です。

- **サービスクラスタIP範囲**（`service_cluster_ip_range`） - これはKubernetesで作成されたサービスに割り当てられる仮想IPアドレスです。
デフォルトでは、サービスクラスタのIP範囲は `10.43.0.0/16` です。
この値を変更した場合は、Kubernetes APIサーバー（`kube-api`）でも同じ値に設定する必要があります。

## Kubelet

[kubelet](https://kubernetes.io/docs/reference/command-line-tools-reference/kubelet/)サービスはKubernetesの「ノードエージェント」として機能します。
これは、RKEによってデプロイされたすべてのノード上で実行され、ノード上のコンテナーランタイムを管理する機能をKubernetesに提供します。

```
services:
    kubelet:
     # Base domain for the cluster
     cluster_domain: cluster.local
     # IP address for the DNS service endpoint
     cluster_dns_server: 10.43.0.10
     # Fail if swap is on
     fail_swap_on: false
```

### Kubelet Options

RKEは、kubeletサービスに対して次のオプションをサポートしています。

- **クラスタドメイン**（`cluster_domain`） - クラスタの[基本ドメイン](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/)。
クラスタ上に作成されたすべてのサービスとDNSレコード。
デフォルトでは、ドメインは `cluster.local` に設定されています。

- **クラスタDNSサーバー**（`cluster_dns_server`） - クラスタ内のDNSサービスエンドポイントに割り当てられているIPアドレス。
DNSクエリは、KubeDNSによって使用されるこのIPアドレスに送信されます。
このオプションのデフォルト値は `10.43.0.10` です。

- **Fail if Swap is On**（`fail_swap_on`） -  Kubernetesでは、ノードでスワップが有効になっていると、kubeletのデフォルトの動作は**失敗**します。
RKEはこのデフォルトに**従わず**、スワップが有効になっているノードへのデプロイメントを許可します。
デフォルトでは、値は `false` です。
デフォルトのkubeletの動作に戻したい場合は、このオプションを `true` に設定してください。

## Kubernetes Scheduler

[Kubernetes Scheduler](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-scheduler/)サービスは、さまざまな構成、メトリックス、リソース要件、およびワークロード固有の要件に基づいて、クラスターワークロードをスケジューリングします。

現時点では、RKEは `scheduler` サービスの特定のオプションをサポートしていません。

## Kubernetes Network Proxy

[Kubernetesネットワークプロキシ](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-proxy/)サービスはすべてのノード上で動作し、TCP/UDPポート用にKubernetesによって作成されたエンドポイントを管理します。

現在、RKEは `kubeproxy` サービスの特定のオプションをサポートしていません。

