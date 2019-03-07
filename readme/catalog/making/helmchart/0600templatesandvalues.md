## テンプレートと値

Helm Chart テンプレート は [Go言語テンプレート](https://golang.org/pkg/text/template/) で書かれており、[Sprigライブラリ](https://github.com/Masterminds/sprig)の50個ほどの追加テンプレート関数とその他の[特殊な関数](https://github.com/helm/helm/blob/master/docs/charts_tips_and_tricks.md)がいくつか追加されています。

すべてのテンプレートファイルはチャートのフォルダ(templates/) に保存されています。 Helmがチャートをレンダリングすると、そのディレクトリ内のすべてのファイルがテンプレートエンジンを通過します。

テンプレートの値は2つの方法で提供されます。

* チャート開発者は、チャートの内部に values.yaml というファイルを提供することができます。 このファイルにはデフォルト値を含めることができます。

* チャートユーザーは値を含むYAMLファイルを提供できます。 これは、コマンドラインで helm install を使用して指定できます。

ユーザーがカスタム値を指定すると、これらの値はチャートのファイル(values.yaml) の値をオーバーライドします。