## Rancher Catalog

# [Rancher2.0 カタログ]

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


* その他参考URL  
<https://qiita.com/tkusumi/items/12857780d8c8463f9b9c>

# Rancher の カタログ 構成

参考URL  
[Helmの概要とChart(チャート)の作り方]<https://qiita.com/thinksphere/items/5f3e918015cf4e63a0bc>  
[Rancherでコンテナ化に挫折しました]<https://texta.pixta.jp/entry/2018/10/31/120000>

Helmチャートの構造 ＋ α(追加項目)

Helm チャート から Rancher 2.0 カタログに追加される項目  
* app-readme.md  
* questions.yml

``` ディレクトリ構造
(カタログの構成)
    チャート名ディレクトリ/
        バージョン/
            Chart.yaml              チャートの概要が記述されたファイル
            values.yaml             デフォルト値が定義されたファイル
            app-readme.md           Rancher 2.0 UI用 Readmeファイル
            questions.yml           Rancher 2.0 UI用 質問ファイル
            README.md               (option)Helm用 Readmeファイル
            requirements.yaml       (option)依存するチャートの一覧
            LICENSE                 (option)チャートのライセンスを含むテキスト
            charts/                 依存関係を含むディレクトリ
            templates/              values.yaml と組合わせて使用する K8SのYMAL を格納
            templates/NOTES.txt     (option)使用メモを含むテキスト
```
