# System Images

RKEがKubernetesを展開しているとき、引っ張られるいくつかのイメージがあります。
これらのイメージはKubernetesのシステムコンポーネントとして使用され、これらのシステムコンポーネントの展開にも役立ちます。

`v0.1.6` 以降、いくつかのシステムイメージの機能は、展開プロセスを簡素化しスピードアップするために単一の `rancher/rke-tools` イメージに統合されました。

[ネットワークプラグイン](https://rancher.com/docs/rke/v0.1.x/en/config-options/add-ons/network-plugins/)と[入力コントローラ](https://rancher.com/docs/rke/v0.1.x/en/config-options/add-ons/ingress-controllers/)、およびこれらのアドオンのオプションを別々に設定できます。

これは、RKEを介してKubernetesを展開するために使用されるシステムイメージの完全なリストの例です。 画像タグは、使用されている [Kubernetes image/version](https://github.com/rancher/types/blob/master/apis/management.cattle.io/v3/k8s_defaults.go) によって異なります。

> **Note：**  
> RKEのバージョンがリリースされると、これらの画像のタグは最新になりません。 このリストは `v1.10.3-rancher2` に固有のものです。

```
system_images:
    kubernetes: rancher/hyperkube:v1.10.3-rancher2
    etcd: rancher/coreos-etcd:v3.1.12
    alpine: rancher/rke-tools:v0.1.9
    nginx_proxy: rancher/rke-tools:v0.1.9
    cert_downloader: rancher/rke-tools:v0.1.9
    kubernetes_services_sidecar: rancher/rke-tools:v0.1.9
    kubedns: rancher/k8s-dns-kube-dns-amd64:1.14.8
    dnsmasq: rancher/k8s-dns-dnsmasq-nanny-amd64:1.14.8
    kubedns_sidecar: rancher/k8s-dns-sidecar-amd64:1.14.8
    kubedns_autoscaler: rancher/cluster-proportional-autoscaler-amd64:1.0.0
    pod_infra_container: rancher/pause-amd64:3.1

    # Flannel Networking Options
    flannel: rancher/coreos-flannel:v0.9.1
    flannel_cni: rancher/coreos-flannel-cni:v0.2.0

    # Calico Networking Options
    calico_node: rancher/calico-node:v3.1.1
    calico_cni: rancher/calico-cni:v3.1.1
    calico_ctl: rancher/calico-ctl:v2.0.0

    # Canal Networking Options
    canal_node: rancher/calico-node:v3.1.1
    canal_cni: rancher/calico-cni:v3.1.1
    canal_flannel: rancher/coreos-flannel:v0.9.1

    # Weave Networking Options
    weave_node: weaveworks/weave-kube:2.1.2
    weave_cni: weaveworks/weave-npc:2.1.2

    # Ingress Options
    ingress: rancher/nginx-ingress-controller:0.10.2-rancher3
    ingress_backend: rancher/nginx-ingress-controller-defaultbackend:1.4
```

`v0.1.6` より前では、`rancher/rke-tools` イメージを使用する代わりに、次のイメージを使用しました。

```
system_images:
    alpine: alpine:latest
    nginx_proxy: rancher/rke-nginx-proxy:v0.1.1
    cert_downloader: rancher/rke-cert-deployer:v0.1.1
    kubernetes_services_sidecar: rancher/rke-service-sidekick:v0.1.0
```

## Air-gapped Setups

エアギャップの設定があり、`docker.io` にアクセスできない場合は、クラスタ設定ファイルで[プライベートレジストリ](0200privateregistries.md)を設定する必要があります。
プライベートレジストリを設定した後、あなたはあなたのプライベートレジストリから引き出すためにこれらのimages を更新する必要があるでしょう。

