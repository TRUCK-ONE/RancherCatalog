# Choosing a Version of Rancher(Rancherのバージョンを選ぶ)

## Single Node Installs

シングルノードインストール、アップグレード、またはロールバックを実行するときは、タグを使用してRancherの特定のバージョンをインストールできます。

### Server Tags

Rancher ServerはDockerイメージとして配布されており、タグが付いています。
Rancherをデプロイするためのコマンドを入力するときにこのタグを指定できます。
明示的なバージョン（最新または安定）のないタグを使用する場合は、そのイメージタグの新しいバージョンを明示的にプルする必要があります。
それ以外の場合は、ホストにキャッシュされているイメージが使用されます。

| Tag | Description |
| --- | --- |
| `rancher/rancher:latest` | 最新の開発版です。 これらのビルドは、CI自動化フレームワークを通じて検証されています。 これらのリリースは実稼働環境にはお勧めできません。|
| `rancher/rancher:stable` | 最新の安定版リリース このタグはプロダクションにお勧めです。 |
| `rancher/rancher:<v2.X.X>` | 以前のリリースのタグを使用して、Rancherの特定のバージョンをインストールできます。 DockerHubで何が利用可能かを確認してください。 |

> **Notes:**
> - `master` タグ、または `-rc` またはその他のサフィックスが付いたタグは、Rancherテストチームが検証するためのものです。 これらのビルドは正式にはサポートされていないため、これらのタグを使用しないでください。
> - プレビュー用にアルファレビューをインストールしますか？ [お知らせページ](https://forums.rancher.com/c/announcements)に記載されているアルファタグのいずれかを使用してインストールします（例：`v2.2.0-alpha1`）。

## High Availability Installs

高可用性構成でRancher Serverをインストール、アップグレード、またはロールバックする場合、RancherサーバーはKubernetesクラスタのヘルムチャートを使用してインストールされます。
したがって、高可用性Rancher構成をインストールまたはアップグレードする準備をするときには、Rancherをインストールするためのチャートを含むHelmチャートリポジトリを追加する必要があります。

### Helm Chart Repositories

Rancherは、選択可能ないくつかの異なるHelmチャートリポジトリを提供しています。
最新の安定したHelmチャートリポジトリを、シングルノードインストールに使用されるDockerタグと整合させます。
したがって、 `rancher-latest` レポジトリには、 `rancher/rancher:latest` としてタグ付けされているすべてのRancherバージョンのチャートが含まれます。
Rancherバージョンが `rancher/rancher:stable` に昇格すると、 `rancher-stable` リポジトリに追加されます。

| Type | Command to Add the Repo | Description of the Repo |
| --- | --- | --- |
| rancher-latest | `helm repo add rancher-latest https://releases.rancher.com/server-charts/latest` | 最新バージョンのRancher用のHelmチャートのリポジトリを追加します。新しいRancherビルドをテストするためにこのリポジトリを使用することをお勧めします。 |
| rancher-stable | `helm repo add rancher-stable https://releases.rancher.com/server-charts/stable` | Rancherの古い安定版に対するHelmチャートのリポジトリを追加します。 本番環境ではこのリポジトリを使用することをお勧めします。 |
| rancher-alpha | `helm repo add rancher-alpha https://releases.rancher.com/server-charts/alpha` | 今後のリリースをプレビューするためのRancherのアルファバージョン用のHelmチャートのリポジトリを追加します。 これらのリリースは運用環境では推奨されません。 リポジトリに関係なく、Rancher-alphaリポジトリのチャートへのアップグレード、または他のチャートへのアップグレードはサポートされていません。 |

これらのレポを選択するタイミングについては、以下の[異なるヘルムチャートレポジトリへの切り替え](https://rancher.com/docs/rancher/v2.x/en/installation/server-tags/#switching-to-a-different-helm-chart-repository)を参照してください。

> **Note:**
> `rancher-latest` および `rancher-stable` のHelm Chartリポジトリの導入はRancher v2.1.0以降に導入されたので、 `rancher-stable` リポジトリには `rancher/rancher:stable` として安定とマークされたことのないいくつかのRancherバージョンが含まれます。
> rancher/rancher:v2.1.0より前の安定版としてタグ付けされたRancherのバージョンはv2.0.4、v2.0.6、v2.0.8です。
> v2.1.0以降では、Rancher-stableリポジトリの全てのチャートは安定版としてタグ付けされた全てのRancherバージョンと一致するでしょう。

### Helm Chart Versions

Rancher v2.1.0用のHelmチャートの初期リリースまでは、HelmチャートのバージョンはRancherのバージョン（つまり `appVersion` ）と一致していました。

Rancherのバージョンに変更を加えずにHelmチャートの変更が必要になる場合があるため、Helmチャートに `yyyy.mm. <build-number>` を使用したバージョン管理方式に移行しました。

どのRancherバージョンがあなたのHelmチャートのために起動されるかを見るために `helm search rancher` を実行してください。

```
NAME                      CHART VERSION    APP VERSION    DESCRIPTION
rancher-latest/rancher    2018.10.1            v2.1.0      Install Rancher Server to manage Kubernetes clusters acro...
```

### Switching to a Different Helm Chart Repository(別の Helm Chart Repository への切り替え)

Rancherをインストールした後、Rancherのインストール元のHelmチャートリポジトリを変更したい場合は、次の手順に従う必要があります。

> **Note:**
> rancher-alphaリポジトリにはアルファチャートしか含まれていないため、アップグレードのためのrancher-alphaリポジトリとrancher-stableまたはrancher-latestリポジトリとの間の切り替えはサポートされていません。

Latest：最新の機能を試すのにおすすめ
Stable：本番環境にお勧め
Alpha：今後のリリースの実験的プレビュー
###### 注意：アップグレードは、Alphaへのアップグレード、Alphaからのアップグレード、Alpha間のアップグレードはサポートされていません。

1. 現在の Helm chart repositories を一覧表示します。

```
helm repo list

NAME                  URL
stable                https://kubernetes-charts.storage.googleapis.com
rancher-stable        https://releases.rancher.com/server-charts/stable
```

2. Rancherをインストールするためのチャートを含む既存のHelm Chartリポジトリを削除します。
これはあなたが持っていたものに応じて `rancher-stable` か `rancher-latest` のどちらかになります。
最初に追加されます。

```
helm repo remove rancher-stable
```

3. Rancherのインストールを開始したい Helm chart repository を追加します。

```
helm repo add rancher-stable https://releases.rancher.com/server-charts/stable
```

4. 手順に従って、新しい Helm chart repository からRancherをアップグレードします。

