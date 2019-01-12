# Charts
参考URL： [helm](https://docs.helm.sh/developing_charts/)
／
[github](https://github.com/helm/helm/blob/master/docs/charts.md)

Helm は、Charts と 呼ばれるパッケージ形式を使用します。Chart は、関連するKubernetesリソースのセットを記述するファイルの集まりです。単一の Chart を使用して、memcachedポッドなどの単純なもの、またはHTTPサーバー、データベース、キャッシュなどを含む完全なWebアプリケーションスタックなどの複雑なものをデプロイできます。

Charts は特定のディレクトリツリーにレイアウトされたファイルとして作成され、展開するためにバージョン管理されたアーカイブにパッケージ化することができます。

このドキュメントは Chart フォーマット を説明し、Helm で Charts を構築するための基本的な手引きを提供します。

## [Chart の構成](helmchart/0000filestructure.md)
## [Chart.yamal](helmchart/0100chartyaml.md)
## [LICENSE, README and NOTES](helmchart/0200file.md)
## [Chart の依存関係](helmchart/0300dependencies.md)
