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


[Rancher の カタログ 構成]

参考URL<https://qiita.com/thinksphere/items/5f3e918015cf4e63a0bc>

Helmチャートの構造 ＋ α

チャート名ディレクトリ\

    Chart.yaml              チャートの概要が記述されたファイル
    values.yaml             デフォルト値が定義されたファイル
    README.md               (option)チャートの説明
    requirements.yaml       (option)依存する他のチャート一覧
    templates\


