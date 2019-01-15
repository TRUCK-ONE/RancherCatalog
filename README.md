# Rancher Catalog

## Rancher の カタログ 構成

参考URL  
[Helmの概要とChart(チャート)の作り方](https://qiita.com/thinksphere/items/5f3e918015cf4e63a0bc)  
[Rancherでコンテナ化に挫折しました](https://texta.pixta.jp/entry/2018/10/31/120000)

Helmチャートの構造 ＋ α(追加項目)

Helm チャート から Rancher 2.0 カタログに追加される項目  
* app-readme.md
* questions.yml

``` ディレクトリ構造
(カタログの構成)
    チャート名ディレクトリ/
        バージョン/
            Chart.yaml              チャートの概要が記述されたファイル (ファイル名は予約語)
            values.yaml             デフォルト値が定義されたファイル (ファイル名は予約語)
            app-readme.md           Rancher 2.0 UI用 Readmeファイル
            questions.yml           Rancher 2.0 UI用 質問ファイル
            README.md               (option)Helm用 Readmeファイル
            requirements.yaml       (option)依存するチャートの一覧 (ファイル名は予約語)
            LICENSE                 (option)チャートのライセンスを含むテキスト
            charts/                 依存関係を含むディレクトリ (ディレクトリ名は予約語)
            templates/              values.yaml と組合わせて使用する K8SのYMAL を格納 (ディレクトリ名は予約語)
            templates/NOTES.txt     (option)使用メモを含むテキスト
            tests/                  (option)helm test で実行されるテスト用YAMLを配置するディレクトリ (ディレクトリ名は予約語)
```

## [Rancher2.0 カタログ]

[Rancher Catalog](https://github.com/rancher/charts)  
→ Private の Rancher2.0 でも、参照は可能

* MySQL

* rocketchat

* wordpress  

* その他参考URL  
[Kubernetes:パッケージマネージャHelm](https://qiita.com/tkusumi/items/12857780d8c8463f9b9c)

---
# 参考

Ranchar の カタログを作成するに参考となるアイテム

[Helm Charts](readme/helmchart/helmchart.md)

