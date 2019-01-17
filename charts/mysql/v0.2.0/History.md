# MySQL 履歴

## ver0.2.0

- TIME/ZONE の デフォルト設定を Tokyo(+9:00) に
- ログの保存ができるように・・・なったらいいな  
ログの保存先のディレクトが、新規作成のため、ディレクトリが作成できていない。

## ver0.1.0

[Rancherカタログ](https://github.com/rancher/charts/blob/master/charts/mysql/v0.3.7/)の内容をアレンジ  
- MySQL を 5.0系 から 8.0系に  
- 注釈等の日本語化  
- ログの取得はできないが、rancherにて起動可能

# Chart の構成

mysql Chart の最新構成を以下に記す。

``` ディレクトリ構造
mysql/
    v0.2.0/
        templates/
            _helpers.tpl
            configmap.yaml
            deployment.yaml
            NOTES.txt
            pvc.yaml
            secrets.yaml            パスワードなどの機密データを取扱っている。
            svc.yaml
        app-readme.md
        Chart.yaml
        history.md                  この履歴ファイル
        questions.yml
        README.md
        values.yaml
```