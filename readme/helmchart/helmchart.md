# [Charts](https://docs.helm.sh/developing_charts/)

Helm は、Charts と 呼ばれるパッケージ形式を使用します。Chart は、関連するKubernetesリソースのセットを記述するファイルの集まりです。単一の Chart を使用して、memcachedポッドなどの単純なもの、またはHTTPサーバー、データベース、キャッシュなどを含む完全なWebアプリケーションスタックなどの複雑なものをデプロイできます。

Charts は特定のディレクトリツリーにレイアウトされたファイルとして作成され、展開するためにバージョン管理されたアーカイブにパッケージ化することができます。

このドキュメントは Chart フォーマット を説明し、Helm で Charts を構築するための基本的な手引きを提供します。

## Chart.yaml

Chart.yaml は Charts に必要です。以下のフィールドがあります。

``` ディレクトリ構造
apiVersion: Chart API の バージョン。常に"v1"(必須)
name: Chart の名前(必須)
version: SemVer 2 の バージョン(必須)
kubeVersion: 互換性のある Kubernetes バージョン のSemVer範囲(オプション)
keywords: プロジェクトに関するキーワードのリスト(オプション)
home: プロジェクトのホームページのURL(オプション)
sources: このプロジェクトのソースコードへのURL(オプション)
maintainers: (オプション)
    name: メンテナーの名前
    email: メンテナーのメールアドレス
    url: メンテナーのURL
engine: gotpl テンプレートエンジンの名前(オプション／デフォルト:gotpl)
icon: アイコンとして使用するSVGまたはPNG画像へのURL(オプション)
appVersion: アプリのバージョン(オプション) SemVer である必要はない
deprecated: chart が非推奨かどうか（オプション／boolean型）
tillerVersion: chart が必要とする Tiller のバージョン。これはSemVerの範囲として表現する必要があります。
```
Helm Classic の Chart.yaml 形式に慣れている場合は、依存関係を指定するフィールドが削除されていることに気付くでしょう。それは、新しい Chartフォーマット が chart/ ディレクトリを使用して依存関係を表現するからです。  
他のフィールドは黙って無視されます。

## Charts と バージョン管理

各 chart にはバージョン番号が必要です。バージョンは [SemVer2](https://semver.org/) に従う必要があります。Helm Classic とは異なり、Kubernetes Helm は リリースマーカーとしてバージョン番号を使用します。リポジトリ内のパッケージは名前とバージョンで識別されます。

例えば、version が ```version：1.2.3``` に設定されている ```nginx``` chart は、次のようになります。
```
nginx-1.2.3.tgz
```
```version:1.2.3-alpha.1 + ef365``` など、より複雑な SemVer2 の名前もサポートされています。 しかし、 SemVer 以外の名前はシステムによって明示的に許可されていません。

**メモ:** チャートに関しては、 Helm Classic と Deployment Manager はどちらも非常に GitHub を指向していましたが、Kubernetes Helm は GitHub や Git に頼ることも必要とすることもありません。そのため、バージョン管理に Git SHA を使用することはまったくありません。

Chart.yaml 内 の versionフィールド は、 CLI や Tiller サーバーなど、多くの Helm ツールで使用されています。 パッケージを生成するとき、 helm package コマンドは、 Chart.yaml で見つかったバージョンをパッケージ名のトークンとして使用します。 システムは、チャートパッケージ名のバージョン番号が Chart.yaml のバージョン番号と一致すると見なします。 この仮定を満たさないとエラーが発生します。

## The appVersion field

appVersionフィールド は versionフィールド とは無関係です。 アプリケーションのバージョンを指定する方法です。 たとえば、drupalチャートの appVersion：8.2.1 は、チャートに含まれているDrupalのバージョン（デフォルト）が8.2.1であることを示しています。 このフィールドは情報を提供するもので、チャートバージョンの計算には影響しません。

## Deprecating Charts

チャートリポジトリでチャートを管理する場合、チャートを非推奨にする必要がある場合があります。 Chart.yaml のオプションの非推奨フィールドを使用して、チャートに非推奨のマークを付けることができます。 リポジトリ内の最新バージョンのチャートが廃止予定としてマークされている場合は、チャート全体として廃止予定と見なされます。 チャート名は、廃止予定としてマークされていない新しいバージョンを公開することによって後で再利用できます。 helm / chart プロジェクトが後に続く、チャートを非推奨にするためのワークフローは次のとおりです。

* チャートの Chart.yaml を更新してチャートを非推奨としてマークし、バージョンを上げます。
* チャートリポジトリで新しいチャートバージョンをリリースする
* ソースリポジトリからチャートを削除する（例：git）

## Chart LICENSE, README and NOTES

Chart には、Chart のインストール、設定、使用方法、およびライセンスを説明するファイルも含めることができます。

ライセンスは、Chart のライセンスを含むプレーンテキストファイルです。 Chart にはテンプレート内にプログラミングロジックがある可能性があるためライセンスを含めることができ、したがって構成のみではありません。 必要に応じて、 Chart によってインストールされたアプリケーションに対して個別のライセンスを設定することもできます。

Chart の README は Markdown（README.md）でフォーマットされていなければならず、一般に次のものを含むべきです。
* Chart が提供するアプリケーションまたはサービスの説明
* Chart を実行するための前提条件または要件
* values.yaml 内のオプションの説明とデフォルト値
* Chart のインストールまたは設定に関連する可能性があるその他の情報

Chart には、インストール後やリリースのステータスを表示するときに印刷される短いプレーンテキストのファイル(templates/NOTES.txt) も含めることができます。 このファイルはテンプレートとして評価され、使用上の注意、次のステップ、または Chart のリリースに関連するその他の情報を表示するために使用できます。 例えば、データベースに接続するため、またはウェブUIにアクセスするための指示を提供することができる。 このファイルは、helm install またはhelm status の実行時に STDOUT に出力されるので、内容を簡潔にして、詳細については README を指すことをお勧めします。

## Chart の依存関係

Helm では、1つの Chart が他の Chart のいくつにも依存することがあります。 これらの依存関係は、 ファイル(requirements.yaml) を介して動的にリンクするか、または ディレクトリー(charts/) に取り込んで手動で管理することができます。

依存関係を手動で管理することは、いくつかの利点がありますが、依存関係を宣言する推奨される方法は、チャート内のファイル(requirements.yaml) を使用することです。  

**注意:** Helm Classic の Chart.yaml のセクション(dependencies:) は完全に削除されました。

## 依存関係の管理 (requirements.yaml)

ファイル(requirements.yaml) は、あなたの依存関係をリストアップするためのシンプルなファイルです。

```
dependencies:
  - name: apache
    version: 1.2.3
    repository: http://example.com/charts
  - name: mysql
    version: 3.2.1
    repository: http://another.example.com/charts
```

* フィールド(name:) は、必要な Chart の名前です。
* フィールド(version:) は、必要な Chart のバージョンです。
* フィールド(repository:) は、チャートリポジトリへの完全なURLです。 そのレポをローカルに追加するには、```helm repo add``` も使用する必要があります。

依存関係ファイルを入手したら、```helm dependency update``` を実行すると、依存関係ファイルを使用して、指定したすべてのチャートが自分のディレクトリ(chart/) にダウンロードされます。
```
$ helm dep up foochart
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "local" chart repository
...Successfully got an update from the "stable" chart repository
...Successfully got an update from the "example" chart repository
...Successfully got an update from the "another" chart repository
Update Complete. Happy Helming!
Saving 2 charts
Downloading apache from repo http://example.com/charts
Downloading mysql from repo http://another.example.com/charts
```
```helm dependency update``` で、Chart を取得すると、チャートアーカイブとしてディレクトリ(charts/)に保存されます。そのため、上の例では、ディレクトリ(charts/)に次のファイルが表示されるはずです。
```
charts/
  apache-1.2.3.tgz
  mysql-3.2.1.tgz
```
requirements.yaml を使用して Chart を管理することは、 Chart を簡単に最新の状態に保ち、またチーム全体で要件情報を共有するのに良い方法です。

## requirements.yaml のエイリアスフィールド

