# 4. Install Rancher

RancherのインストールはKubernetes用のHelmパッケージマネージャを使って管理されます。
前提条件をインストールするには `helm` を使用し、Rancherをインストールするにはチャートを使用してください。

> **Note：**  
> インターネットに直接アクセスできないシステムについては、[Air Gap：High Availability Install](https://rancher.com/docs/rancher/v2.x/en/installation/air-gap-high-availability/install-rancher/)を参照してください。

## Add the Helm Chart Repository

Rancherをインストールするためのチャートを含むHelmチャートリポジトリを追加するには、`helm repo add` コマンドを使用します。
リポジトリの選択とユースケースに最適な選択の詳細については、[Rancherのバージョンの選択](https://rancher.com/docs/rancher/v2.x/en/installation/server-tags/#helm-chart-repositories)を参照してください。

- Latest：最新の機能を試すのにおすすめ
- Stable：実稼働環境にお勧め
- Alpha：今後のリリースの実験的プレビュー  
    ######  Note：アップグレードはAlphaへのアップグレード、Alphaからのアップグレード、Alpha間でのアップグレードはサポートされていません。

```
helm repo add rancher-stable https://releases.rancher.com/server-charts/stable
```

## Choose your SSL Configuration

Rancher Serverはデフォルトで安全になるように設計されており、SSL/TLS 設定が必要です。

証明書の出所には3つの推奨オプションがあります。

> **Note：**  
> SSL/TLS を外部で終了したい場合は、外部ロードバランサーでのTLS終了を参照してください。

| Configuration | Chart option | Description | Requires cert-manager |
| --- | --- | --- | --- |
| [Rancher Generated Certificates](https://rancher.com/docs/rancher/v2.x/en/installation/ha/helm-rancher/#rancher-generated-certificates) | `ingress.tls.source=rancher` | Rancherの生成したCAによって発行された証明書を使用する（自己署名）<br>これが**デフォルトです**。 | [yes](https://rancher.com/docs/rancher/v2.x/en/installation/ha/helm-rancher/#optional-install-cert-manager) |
| [Let’s Encrypt](https://rancher.com/docs/rancher/v2.x/en/installation/ha/helm-rancher/#let-s-encrypt) | `ingress.tls.source=letsEncrypt` | 証明書を発行するには、[暗号化しましょう](https://letsencrypt.org/) | [yes](https://rancher.com/docs/rancher/v2.x/en/installation/ha/helm-rancher/#optional-install-cert-manager) |
| [Certificates from Files](https://rancher.com/docs/rancher/v2.x/en/installation/ha/helm-rancher/#certificates-from-files) | `ingress.tls.source=secret` | Kubernetes Secretを作成してあなた自身の証明書ファイルを使用してください | no |

## Optional: Install cert-manager

> **Note：**  
> cert-managerは、Rancherが生成したCAによって発行された証明書（`ingress.tls.source = rancher`）および発行した証明書を暗号化しましょう（`ingress.tls.source = letsEncrypt`）にのみ必要です。
>独自の証明書ファイルを使用している場合（オプション `ingress.tls.source = secret`）、または[外部ロードバランサでTLS終了を使用している場合](https://rancher.com/docs/rancher/v2.x/en/installation/ha/helm-rancher/chart-options/#external-tls-termination)は、この手順をスキップしてください。
>
> **重要：**  
> Helm v2.12.0およびcert-managerの問題のため、Helm v2.12.1以降を使用してください。

Rancherは、公式のKubernetes Helmチャートリポジトリの [cert-manager](https://github.com/helm/charts/tree/master/stable/cert-manager) バージョンv0.5.2を使用して、Rancher自身が生成したCAから証明書を発行するか、Let's Encrypt証明書を要求します。

Kubernetes Helmチャートリポジトリから `cert-manager` をインストールします。

```
helm install stable/cert-manager \
  --name cert-manager \
  --namespace kube-system \
  --version v0.5.2
```

`cert-manager` がロールアウトされるのを待ちます。

```
kubectl -n kube-system rollout status deploy/cert-manager
Waiting for deployment "cert-manager" rollout to finish: 0 of 1 updated replicas are available...
deployment "cert-manager" successfully rolled out
```

### Rancher Generated Certificates

> **Note：**  
> 続行する前にcert-managerをインストールしておく必要があります。

デフォルトでは、RancherはCAを生成し、 `cert-manager` を使用してRancherサーバーインターフェイスにアクセスするための証明書を発行します。
`rancher` は `ingress.tls.source` のデフォルトのオプションなので、`helm install` コマンドを実行するときに `ingress.tls.source` を指定していません。

- `hostname` をロードバランサーに指定したDNS名に設定します。

```
helm install rancher-latest/rancher \
  --name rancher \
  --namespace cattle-system \
  --set hostname=rancher.my.org
```

Rancherがロールアウトされるのを待ちます。

```
kubectl -n cattle-system rollout status deploy/rancher
Waiting for deployment "rancher" rollout to finish: 0 of 3 updated replicas are available...
deployment "rancher" successfully rolled out
```

### Let’s Encrypt

> **Note：**  
> 続行する前に [cert-manager](https://rancher.com/docs/rancher/v2.x/en/installation/ha/helm-rancher/#optional-install-cert-manager) をインストールしておく必要があります。

このオプションでは、`cert-manager` を使用して、[Let's Encrypt](https://letsencrypt.org/)証明書を自動的に要求および更新します。
Let's Encryptは信頼できるCAなので、これは有効な証明書を提供する無料のサービスです。
この設定ではHTTP検証（`HTTP-01`）を使用するため、ロードバランサはパブリックDNSレコードを持ち、インターネットからアクセスできる必要があります。

- `hostname`をパブリックDNSレコードに設定し、 `ingress.tls.source` を `letsEncrypt` に設定し、`letsEncrypt.email` を証明書に関する通信に使用される電子メールアドレスに設定します（たとえば、有効期限の通知）。

```
helm install rancher-latest/rancher \
  --name rancher \
  --namespace cattle-system \
  --set hostname=rancher.my.org \
  --set ingress.tls.source=letsEncrypt \
  --set letsEncrypt.email=me@example.org
```

Rancherがロールアウトされるのを待ちます。

```
kubectl -n cattle-system rollout status deploy/rancher
Waiting for deployment "rancher" rollout to finish: 0 of 3 updated replicas are available...
deployment "rancher" successfully rolled out
```

### Certificates from Files

Rancherが使用する独自の証明書からKubernetesの秘密を作成します。

> **Note：**  
> サーバ証明書の `Common Name` または `Subject Alternative Names` エントリは、`hostname` オプションと一致する必要があります。
そうしないと、入力コントローラが正しく設定できません。
`Subject Alternative Names` の入力は技術的には必要ですが、一致する `Common Name` を持つことで、古いブラウザ/アプリケーションとの互換性が最大になります。
証明書が正しいかどうかを確認したい場合は、[サーバー証明書の共通名とサブジェクト代替名を確認する方法](https://rancher.com/docs/rancher/v2.x/en/faq/technical/#how-do-i-check-common-name-and-subject-alternative-names-in-my-server-certificate)を参照してください。

- `hostname` を設定し、`ingress.tls.source` を`secret` に設定します。
- プライベートCA署名付き証明書を使用している場合は、以下に示すコマンドに `--set privateCA=true` を追加します。

```
helm install rancher-latest/rancher \
  --name rancher \
  --namespace cattle-system \
  --set hostname=rancher.my.org \
  --set ingress.tls.source=secret
```

Rancherがデプロイされたので、証明書ファイルを公開してRancherと入力コントローラがそれらを使用できるようにするための[TLS秘密の追加](https://rancher.com/docs/rancher/v2.x/en/installation/ha/helm-rancher/tls-secrets/)を参照してください。

秘密を追加したら、Rancherが正常にロールアウトされたかどうかを確認します。

```
kubectl -n cattle-system rollout status deploy/rancher
Waiting for deployment "rancher" rollout to finish: 0 of 3 updated replicas are available...
deployment "rancher" successfully rolled out
```

次のエラーが表示された場合：`error: deployment "rancher" exceeded its progress deadline` は、次のコマンドを実行してデプロイメントのステータスを確認できます。

```
kubectl -n cattle-system get deploy rancher
NAME      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
rancher   3         3         3            3           3m
```

`DESIRED` と `AVAILABLE` に同じ数が表示されるはずです。

## Advanced Configurations

Rancherチャート構成には、特定の環境に合わせてインストールをカスタマイズするための多くのオプションがあります。
これが一般的な高度なシナリオです。

- [HTTP Proxy](https://rancher.com/docs/rancher/v2.x/en/installation/ha/helm-rancher/chart-options/#http-proxy)
- [Private Docker Image Registry](https://rancher.com/docs/rancher/v2.x/en/installation/ha/helm-rancher/chart-options/#private-registry-and-air-gap-installs)
- [TLS Termination on an External Load Balancer](https://rancher.com/docs/rancher/v2.x/en/installation/ha/helm-rancher/chart-options/#external-tls-termination)

オプションの全リストについては、[チャートオプション](https://rancher.com/docs/rancher/v2.x/en/installation/ha/helm-rancher/chart-options/)を参照してください。

## Save your options

使用した `--set` オプションを必ず保存してください。
RancherをHelmで新しいバージョンにアップグレードするときにも、同じオプションを使用する必要があります。

## Finishing Up

それであなたは機能するRancherサーバーを持っているべきです。
選択したホスト名にブラウザを向けます。
カラフルなログインページが表示されます。

うまくいかない？ [トラブルシューティング](https://rancher.com/docs/rancher/v2.x/en/installation/ha/helm-rancher/troubleshooting/)ページを見てください。

