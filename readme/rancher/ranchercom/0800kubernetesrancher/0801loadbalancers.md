# Load Balancers

Kubernetesは2つの方法でロードバランシングをサポートします：レイヤ4ロードバランシングとレイヤ7ロードバランシング。

## Layer-4 Load Balancer

Layer-4 load balancer（または外部ロードバランサ）は、トラフィックをノードポートに転送します。
Layer-4 load balancer を使用すると、HTTPトラフィックとTCPトラフィックの両方を転送できます。
Layer-4 load balancer は、基盤となるクラウドプロバイダによってサポートされています。
その結果、ベアメタルサーバーとvSphereクラスターにRKEクラスターを展開すると、Layer-4 load balancerはサポートされません。

### Support for Layer-4 Load Balancing

Layer-4 load balancer のサポートは、基盤となるクラウドプロバイダによって異なります。

| Cluster Deployment | Layer-4 Load Balancer Support |
| --- | --- |
| Amazon EKS | Supported by AWS cloud provide |
| Google GKE | Supported by GCE cloud provider |
| Azure AKS | Supported by Azure cloud provider |
| RKE on EC2 | Supported by AWS cloud provider |
| RKE on DigitalOcean | Not Supported |
| RKE on vSphere | Not Supported |
| RKE on Custom Hosts (e.g. bare-metal servers) | Not Supported |

## Layer-7 Load Balancer

Layer-7 load balancer （または入力コントローラ）は、ホストおよびパスベースのロードバランシングとSSL終了をサポートします。
Layer-7 load balancer はHTTPおよびHTTPSトラフィックのみを転送するため、ポート80および443でのみ待機します。
AmazonやGoogleなどのクラウドプロバイダーは、Layer-7 load balancer をサポートしています。
さらに、RKEクラスタはNginx Ingress Controllerを展開します。

### Support for Layer-7 Load Balancing

Layer-7 load balancer のサポートは、基盤となるクラウドプロバイダによって異なります。

| Cluster Deployment | Layer-7 Load Balancer Support |
| --- | --- |
| Amazon EKS | Supported by AWS cloud provider |
| Google GKE | Supported by GKE cloud provider |
| Azure AKS | Not Supported |
| RKE on EC2 | Nginx Ingress Controller |
| RKE on DigitalOcean | Nginx Ingress Controller |
| RKE on vSphere | Nginx Ingress Controller |
| RKE on Custom Hosts (e.g. bare-metal servers) | Nginx Ingress Controller |

### Host Names in Layer-7 Load Balancer

一部のクラウド管理 Layer-7 Load Balancer（AWSのALB入力コントローラなど）は、入力ルール用にDNSアドレスを公開しています。
ドメイン名を Layer-7 Load Balancer によって生成されたDNSアドレスに（CNAME経由で）マッピングする必要があります。

Google Load Balancer や Nginx Ingress Controllerなどの他の Layer-7 Load Balancer は、1つ以上のIPアドレスを直接公開します。
Google Load Balancer は単一のルーティング可能なIPアドレスを提供します。
Nginx Ingress Controllerは、Nginx Ingress Controller を実行しているすべてのノードの外部IPを公開します。
次のどちらかを実行できます。

1. ドメイン名を（Aレコードを介して）Layer-7 Load Balancer によって公開されるIPアドレスにマッピングするように独自のDNSを設定します。
1. あなたのイングレスルールのためにxip.ioホスト名を生成するようにRancherに依頼してください。
Rancherは公開されているIPの1つを受け取り、a.b.c.dと言って、ホスト名..a.b.c.d.xip.ioを生成します。

xip.ioを使用する利点は、入力規則を作成した直後に作業中のエントリポイントURLを取得できることです。
一方、独自のドメイン名を設定するには、DNSサーバーを設定し、DNSが伝播するのを待つ必要があります。

## Related Links(参照リンク)
- [Create an External Load Balancer](https://kubernetes.io/docs/tasks/access-application-cluster/create-external-load-balancer/)

### Tutorials
- High Availability Installation with External Load Balancer (HTTPS/Layer 7)
- High Availability Installation with External Load Balancer (TCP/Layer 4)
- Single Node Installation with External Load Balancer



