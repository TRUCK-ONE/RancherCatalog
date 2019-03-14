# 1. Create Nodes and Load Balancer(ノードとロードバランサを作成する)

ご使用のプロバイダーを使用して、RKEインストール用に3つのノードとLoad Balancerエンドポイントをプロビジョニングしてください。

> **Note:**  
> これらのノードは同じ region/datacenter 内になければなりません。
> これらのサーバーを別々のアベイラビリティーゾーンに配置することができます。

## Node Requirements(Node 要件)

[Node Requirements](https://rancher.com/docs/rancher/v2.x/en/installation/requirements/) で Rancher を実行しているノードでサポートされているオペレーティングシステムおよびハードウェア/ソフトウェア/ネットワークの要件を表示します。

[RKE Requirements](https://rancher.com/docs/rke/v0.1.x/en/os/) でRKEのOS要件を見る

## Load Balancer(ロードバランサ)

RKEは各ノードでIngressコントローラポッドを設定します。
入力コントローラポッドは、ホストネットワーク上のポートTCP/80およびTCP/443にバインドされており、RancherサーバーへのHTTPSトラフィックの入り口です。

ロードバランサを基本的なレイヤ4 TCPフォワーダとして設定します。
正確な構成は環境によって異なります。

> **重要：**  
> インストール後にRancher以外のアプリケーションをロードバランスするために、このロードバランサ（つまり、ローカルクラスタのIngress）を使用しないでください。
> このIngressを他のアプリケーションと共有すると、他のアプリケーションのIngress設定のリロード後にRancherにwebsocketエラーが発生する可能性があります。
> ローカルクラスタをRancher専用にし、他のアプリケーション専用にしないことをお勧めします。

## Examples(例)

- [Nginx](https://rancher.com/docs/rancher/v2.x/en/installation/ha/create-nodes-lb/nginx/)
- [Amazon NLB](https://rancher.com/docs/rancher/v2.x/en/installation/ha/create-nodes-lb/nlb/)

## [Next: Install Kubernetes with RKE](https://rancher.com/docs/rancher/v2.x/en/installation/ha/kubernetes-rke/)


