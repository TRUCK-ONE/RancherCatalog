## Chart.yaml

Chart.yaml は Charts に必要です。
以下のフィールドがあります。

``` ファイル構造
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

Helm Classic の Chart.yaml 形式に慣れている場合は、依存関係を指定するフィールドが削除されていることに気付くでしょう。
それは、新しい Chartフォーマット が ディレクトリ(charts/) を使用して依存関係を表現するからです。  
他のフィールドは黙って無視されます。

## Charts と バージョン管理

各 Chart にはバージョン番号が必要です。バージョンは [SemVer2](https://semver.org/) に従う必要があります。
Helm Classic とは異なり、Kubernetes Helm は リリースマーカーとしてバージョン番号を使用します。
リポジトリ内のパッケージは名前とバージョンで識別されます。

例えば、version が ```version：1.2.3``` に設定されている ```nginx``` chart は、次のようになります。

```
nginx-1.2.3.tgz
```

```version:1.2.3-alpha.1 + ef365``` など、より複雑な SemVer2 の名前もサポートされています。
しかし、 SemVer 以外の名前はシステムによって明示的に許可されていません。

**メモ:** Helm Classic と Deployment Manager はどちらも GitHub を指向していましたが、Kubernetes Helm は GitHub や Git に頼ることも必要とすることもありません。
そのため、バージョン管理に Git SHA を使用することはまったくありません。

Chart.yaml 内 の versionフィールド は、 CLI や Tiller サーバーなど、多くの Helm ツールで使用されています。
パッケージを生成するとき、コマンド(helm package) は、 Chart.yaml で見つかったバージョンをパッケージ名のトークンとして使用します。
システムは、チャートパッケージ名のバージョン番号が Chart.yaml のバージョン番号と一致すると見なします。
この仮定を満たさないとエラーが発生します。

## The appVersion field

appVersionフィールド は versionフィールド とは無関係です。
アプリケーションのバージョンを指定する方法です。
たとえば、drupalチャートの appVersion：8.2.1 は、チャートに含まれているDrupalのバージョン（デフォルト）が8.2.1であることを示しています。
このフィールドは情報を提供するもので、チャートバージョンの計算には影響しません。

## Deprecating Charts

チャートリポジトリでチャートを管理する場合、チャートを非推奨にする必要がある場合があります。
Chart.yaml のオプションの非推奨フィールドを使用して、チャートに非推奨のマークを付けることができます。
リポジトリ内の最新バージョンのチャートが廃止予定としてマークされている場合は、チャート全体として廃止予定と見なされます。
チャート名は、廃止予定としてマークされていない新しいバージョンを公開することによって後で再利用できます。
helm/chart プロジェクトが後に続く、チャートを非推奨にするためのワークフローは次のとおりです。

* チャートの Chart.yaml を更新してチャートを非推奨としてマークし、バージョンを上げます。
* チャートリポジトリで新しいチャートバージョンをリリースする
* ソースリポジトリからチャートを削除する（例：git）

