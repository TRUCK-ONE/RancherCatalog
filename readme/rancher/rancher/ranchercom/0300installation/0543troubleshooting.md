# Troubleshooting

## Where is everything

トラブルシューティングのほとんどは、これら3つのネームスペース内のオブジェクトに対して行われます。

- `cattle-system` - `rancher` の展開とポッド。
- `ingress-nginx` - 入力コントローラポッドとサービス。
- `kube-system` - `tiller` および `cert-manager` ポッド。

## “default backend - 404”

いくつかのことで、イングレスコントローラがあなたのrancherのインスタンスにトラフィックを転送しないことがあります。
ほとんどの場合それは悪いssl設定によるものです。

確認すること

- [Rancher が実行中か](https://rancher.com/docs/rancher/v2.x/en/installation/ha/helm-rancher/troubleshooting/#is-rancher-running)
- [Cert CNは「Kubernetes Ingress Controllerの偽の証明書」です。](https://rancher.com/docs/rancher/v2.x/en/installation/ha/helm-rancher/troubleshooting/#cert-cn-is-kubernetes-ingress-controller-fake-certificate)

## Is Rancher Running(Rancher が実行中か)

`kubectl` を使用して、`cattle-system` システムの名前空間を確認し、ランチャーポッドが実行状態にあるかどうかを確認します。

```
kubectl -n cattle-system get pods

NAME                           READY     STATUS    RESTARTS   AGE
pod/rancher-784d94f59b-vgqzh   1/1       Running   0          10m
```

状態が`実行中`ではない場合は、ポッドで `describe` を実行して[イベント]を確認します。

```
kubectl -n cattle-system describe pod

...
Events:
  Type     Reason                 Age   From                Message
  ----     ------                 ----  ----                -------
  Normal   Scheduled              11m   default-scheduler   Successfully assigned rancher-784d94f59b-vgqzh to localhost
  Normal   SuccessfulMountVolume  11m   kubelet, localhost  MountVolume.SetUp succeeded for volume "rancher-token-dj4mt"
  Normal   Pulling                11m   kubelet, localhost  pulling image "rancher/rancher:v2.0.4"
  Normal   Pulled                 11m   kubelet, localhost  Successfully pulled image "rancher/rancher:v2.0.4"
  Normal   Created                11m   kubelet, localhost  Created container
  Normal   Started                11m   kubelet, localhost  Started container
```

## Checking the rancher logs

ポッドを一覧表示するには、`kubectl` を使用してください。

```
kubectl -n cattle-system get pods

NAME                           READY     STATUS    RESTARTS   AGE
pod/rancher-784d94f59b-vgqzh   1/1       Running   0          10m
```

ポッドからのログを一覧表示するには、`kubectl` とポッド名を使用します。

```
kubectl -n cattle-system logs -f rancher-784d94f59b-vgqzh
```
## Cert CN is “Kubernetes Ingress Controller Fake Certificate”

ブラウザを使用して証明書の詳細を確認してください。
一般名が「Kubernetes Ingress Controllerの偽の証明書」と表示されている場合は、SSL証明書の読み取りまたは発行に問題がある可能性があります。

> **Note：**  
> 証明書の発行にLetsEncryptを使用している場合は、証明書の発行に数分かかることがあります。

### cert-manager issued certs (Rancher Generated or LetsEncrypt)

`cert-manager`には3つの部分があります。

- `kube-system` 名前空間の `cert-manager` ポッド
- `cattle-system` 名前空間内の `Issuer` オブジェクト
- `cattle-system` ネームスペース内の `Certificate` オブジェクト

逆方向に作業し、各オブジェクトについて `kubectl describe` を実行してイベントを確認します。
何が足りないのか突き止めることができます。

たとえば、発行者に問題があります。

```
kubectl -n cattle-system describe certificate
...
Events:
  Type     Reason          Age                 From          Message
  ----     ------          ----                ----          -------
  Warning  IssuerNotReady  18s (x23 over 19m)  cert-manager  Issuer rancher not ready
```
```
kubectl -n cattle-system describe issuer
...
Events:
  Type     Reason         Age                 From          Message
  ----     ------         ----                ----          -------
  Warning  ErrInitIssuer  19m (x12 over 19m)  cert-manager  Error initializing issuer: secret "tls-rancher" not found
  Warning  ErrGetKeyPair  9m (x16 over 19m)   cert-manager  Error getting keypair for CA issuer: secret "tls-rancher" not found
```

### Bring Your Own SSL Certs

あなたの証明書は、`cattle-system` ネームスペースのIngressオブジェクトに直接適用されます。

Ingressオブジェクトのステータスを確認して、準備ができているかどうかを確認してください。

```
kubectl -n cattle-system describe ingress
```

準備ができてもSSLがまだ機能していない場合は、不正な証明書または秘密がある可能性があります。

nginx-ingress-controllerログを確認してください。
nginx-ingress-controllerはそのポッドに複数のコンテナーを持っているので、コンテナーの名前を指定する必要があります。

```
kubectl -n ingress-nginx logs -f nginx-ingress-controller-rfjrq nginx-ingress-controller
...
W0705 23:04:58.240571       7 backend_ssl.go:49] error obtaining PEM from secret cattle-system/tls-rancher-ingress: error retrieving secret cattle-system/tls-rancher-ingress: secret cattle-system/tls-rancher-ingress was not found
```

## no matches for kind “Issuer”

選択した[SSL設定](https://rancher.com/docs/rancher/v2.x/en/installation/ha/helm-rancher/#choose-your-ssl-configuration)オプションでは、Rancherをインストールする前に[cert-manager](https://rancher.com/docs/rancher/v2.x/en/installation/ha/helm-rancher/#optional-install-cert-manager)をインストールする必要があります。
そうしないと、次のエラーが表示されます。

```
Error: validation failed: unable to recognize "": no matches for kind "Issuer" in version "certmanager.k8s.io/v1alpha1"
```

[cert-manager](https://rancher.com/docs/rancher/v2.x/en/installation/ha/helm-rancher/#optional-install-cert-manager)をインストールして、もう一度Rancherをインストールしてみてください。


