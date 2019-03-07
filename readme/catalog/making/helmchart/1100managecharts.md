## Using Helm to Manage Charts

helmツールには、チャートを操作するためのいくつかのコマンドがあります。

それはあなたのために新しいチャートを作成することができます：

```
$ helm create mychart
Created mychart/
```

チャートを編集したら、helmはそれをチャートアーカイブにパッケージ化できます。

```
$ helm package mychart
Archived mychart-0.1.-.tgz
```

チャートのフォーマットや情報に関する問題を見つけるのに役立つようにhelmを使用することもできます。

```
$ helm lint mychart
No issues found
```