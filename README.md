# RancherCatalog

[Rancher2.0 カタログ]

Rancher Catalog

<https://github.com/rancher/charts>

→ Private の Rancher2.0 でも、参照は可能

* alpinelinux

v1.0コピー元

<https://github.com/helm/helm/tree/master/docs/examples/alpine>

* testhelloworld

v1.0コピー元

<https://github.com/open-toolchain/hello-helm/tree/master/chart/hello>


* wordpress 


その他参考URL

<https://qiita.com/tkusumi/items/12857780d8c8463f9b9c>


# Rancher の カタログ 構成

参考URL<https://qiita.com/thinksphere/items/5f3e918015cf4e63a0bc>

Helmチャートの構造 ＋ α

Helm チャートから追加された項目

* arr-readme.md

* questions.yaml


カタログの構成
    チャート名ディレクトリ\
        バージョン\
            Chart.yaml              チャートの概要が記述されたファイル
            values.yaml             デフォルト値が定義されたファイル
            arr-readme.md           Rancher 2.0 UI用 Readmeファイル
            questions.yaml          Rancher 2.0 UI用 質問ファイル
            README.md               (option)Helm用 Readmeファイル
            requirements.yaml       (option)依存するチャートの一覧
            templates\              values.yaml と組合わせて使用する K8SのYMAL を格納
            charts\                 依存関係を含むディレクトリ

