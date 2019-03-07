## Scope, Dependencies, and Values

Values ファイルは、トップレベルのチャート、およびそのチャートのディレクトリ(charts/) に含まれているすべてのチャートの値を宣言できます。
別の言い方をすると、値ファイルはチャートとその依存関係のいずれかに値を提供できます。
例えば、上のデモ用WordPressチャートはmysqlとapacheの両方を依存関係として持っています。
値ファイルは、これらすべてのコンポーネントに値を提供できます。

```
title: "My WordPress Site" # Sent to the WordPress template

mysql:
  max_connections: 100 # Sent to MySQL
  password: "secret"

apache:
  port: 8080 # Passed to Apache
```

上位レベルのチャートは、その下に定義されているすべての変数にアクセスできます。
そのため、WordPressチャートはMySQLのパスワードに.Values.mysql.passwordとしてアクセスできます。
しかし、下位レベルのチャートは親チャート内のものにアクセスできないため、MySQLはtitleプロパティにアクセスできません。
それでも、それはapache.portにアクセスできません。

値は名前空間ですが、名前空間は整理されます。
そのため、WordPressチャートの場合、MySQLのパスワードフィールドに.Values.mysql.passwordとしてアクセスできます。
しかし、MySQLチャートでは、値の範囲が縮小され、名前空間の接頭辞が削除されたため、パスワードフィールドは単に.Values.passwordとして表示されます。

### Global Values

2.0.0-Alpha.2以降、Helmは特別な "グローバル"値をサポートします。
例の修正版を考えてみましょう。

```
title: "My WordPress Site" # Sent to the WordPress template

global:
  app: MyWordPress

mysql:
  max_connections: 100 # Sent to MySQL
  password: "secret"

apache:
  port: 8080 # Passed to Apache
```

上記はapp：MyWordPressという値のグローバルセクションを追加します。
この値は、すべてのチャートで.Values.global.appとして使用できます。

例えば、mysqlテンプレートは{{.Values.global.app}}としてappにアクセスすることができ、そしてapacheチャートもそうすることができます。
事実上、上記のvaluesファイルはこのように再生成されます。

```
title: "My WordPress Site" # Sent to the WordPress template

global:
  app: MyWordPress

mysql:
  global:
    app: MyWordPress
  max_connections: 100 # Sent to MySQL
  password: "secret"

apache:
  global:
    app: MyWordPress
  port: 8080 # Passed to Apache
```

これは、1つの最上位変数をすべてのサブチャートと共有する方法を提供します。
これは、ラベルなどのメタデータプロパティの設定などに役立ちます。

サブチャートがグローバル変数を宣言した場合、そのグローバルは（サブチャートのサブチャートに）下に渡されますが、親チャートには渡されません。
サブチャートが親チャートの値に影響を与える方法はありません。

また、親チャートのグローバル変数はサブチャートのグローバル変数よりも優先されます。

### References

テンプレートと値ファイルの作成に関しては、役に立つ参考文献がいくつかあります。

* [Go templates](https://godoc.org/text/template)
* [Extra template functions](https://godoc.org/github.com/Masterminds/sprig)
* [The YAML format](https://yaml.org/spec/)
