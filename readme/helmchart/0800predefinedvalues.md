## Predefined Values (定義済みの値)

values.yamlファイル（または--setフラグ）を介して提供される値は、テンプレート内の.Valuesオブジェクトからアクセスできます。 ただし、テンプレート内でアクセスできる定義済みのデータが他にもあります。

以下の値は事前定義されており、すべてのテンプレートで使用可能であり、オーバーライドすることはできません。 すべての値と同様に、名前は大文字と小文字が区別されます。

* Release.Name：リリースの名前（chart ではありません）
* Release.Time：チャートのリリースが最後に更新された時刻。 これは、Releaseオブジェクトの最終リリース時刻と一致します。
* Release.Namespace：チャートがリリースされたネームスペース。
* Release.Service：リリースを行ったサービス。 通常これはTillerです。
* Release.IsUpgrade：現在の操作がアップグレードまたはロールバックの場合、これはtrueに設定されます。
* Release.IsInstall：現在の操作がインストールの場合、これはtrueに設定されています。
* Release.Revision：リビジョン番号。 それは1から始まり、ヘルムアップグレードごとに増加します。
* Chart：Chart.yamlの内容 したがって、チャートバージョンはChart.Versionとして入手でき、メンテナはChart.Maintainersにいます。
* ファイル：チャート内のすべての特別ではないファイルを含むマップのようなオブジェクト。 これによってテンプレートにアクセスすることはできませんが、（.helmignoreを使用して除外されていない限り）存在する追加のファイルにアクセスすることができます。 ファイルには、{{index .Files "file.name"}}を使用するか、{{.Files.Get name}}または{{.Files.GetString name}}関数を使用してアクセスできます。 {{.Files.GetBytes}}を使ってファイルの内容に[] byteとしてアクセスすることもできます。
* Capabilities：Kubernetesのバージョン（{{.Capabilities.KubeVersion}}、Tiller（{{.Capabilities.TillerVersion}}、およびサポートされているKubernetes APIのバージョン（{{.Capabilities.APIVersions）に関する情報を含むマップのようなオブジェクトです。 "batch / v1"を持っています）

**注：** 不明なChart.yamlフィールドはすべて削除されます。 それらはChartオブジェクトの中からアクセスすることはできません。 したがって、Chart.yaml を使用して任意の構造化データをテンプレートに渡すことはできません。 ただし、そのためには values ファイルを使用できます。
