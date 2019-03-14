# Catalogs and Charts

Rancherは、あらゆるアプリケーションを繰り返し簡単にデプロイできるようにするためのチャートのカタログを提供しています。

カタログとはGitHubのリポジトリで、すぐに使えるアプリケーションが用意されています。 アプリケーションはチャートと呼ばれるオブジェクトにまとめられています。

チャートは、Helmによって普及しているパッケージ形式です。 それらを展開用のテンプレートと考えてください。 Helm 、charts は次のとおりです。
```
Kubernetesリソースの関連セットを記述するファイルの集まり。 単一のチャートを使用して、memcachedポッドなどの単純なもの、またはHTTPサーバー、データベース、キャッシュなどを含む完全なWebアプリケーションスタックなどの複雑なものをデプロイできます。
```
RancherはHelmのカタログとチャートを改良しました。 ネイティブのHelmチャートはすべてRancher内で機能しますが、Rancherではユーザーエクスペリエンスを向上させるためにいくつかの機能強化が追加されています。

## [Enabling Built-in Catalogs](0110enablingcatalogs.md)
## [Custom Catalogs and Charts](0120customcatalogscharts.md)
