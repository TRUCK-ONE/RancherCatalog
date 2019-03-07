## Chart の依存関係

Helm では、1つの Chart が他の Chart に依存することがあります。 これらの依存関係は、 ファイル(requirements.yaml) を介して動的にリンクするか、または ディレクトリー(charts/) に取り込んで手動で管理することができます。

依存関係を手動で管理することは、いくつかの利点がありますが、依存関係を宣言する推奨される方法は、チャート内のファイル(requirements.yaml) を使用することです。  

**注意:** Helm Classic の Chart.yaml のセクション(dependencies:) は完全に削除されました。

## 依存関係の管理 (requirements.yaml)

ファイル(requirements.yaml) は、あなたの依存関係をリストアップするためのシンプルなファイルです。

```
dependencies:
  - name: apache
    version: 1.2.3
    repository: http://example.com/charts
  - name: mysql
    version: 3.2.1
    repository: http://another.example.com/charts
```

* フィールド(name:) は、必要な Chart の名前です。
* フィールド(version:) は、必要な Chart のバージョンです。
* フィールド(repository:) は、チャートリポジトリへの完全なURLです。 そのレポをローカルに追加するには、```helm repo add``` も使用する必要があります。

依存関係ファイルを入手したら、```helm dependency update``` を実行すると、依存関係ファイルを使用して、指定したすべてのチャートが自分のディレクトリ(charts/) にダウンロードされます。
```
$ helm dep up foochart
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "local" chart repository
...Successfully got an update from the "stable" chart repository
...Successfully got an update from the "example" chart repository
...Successfully got an update from the "another" chart repository
Update Complete. Happy Helming!
Saving 2 charts
Downloading apache from repo http://example.com/charts
Downloading mysql from repo http://another.example.com/charts
```
```helm dependency update``` で、Chart を取得すると、チャートアーカイブとしてディレクトリ(charts/)に保存されます。そのため、上の例では、ディレクトリ(charts/)に次のファイルが表示されるはずです。
```
charts/
  apache-1.2.3.tgz
  mysql-3.2.1.tgz
```
requirements.yaml を使用して Chart を管理することは、 Chart を簡単に最新の状態に保ち、またチーム全体で要件情報を共有するのに良い方法です。

## requirements.yaml のエイリアスフィールド

上記のフィールドに加えて、各要件エントリにはオプションのフィールドエイリアスを含めることができます。

依存関係チャートにエイリアスを追加すると、エイリアスを新しい依存関係の名前として使用して、チャートを依存関係に配置します。

他の名前のチャートにアクセスする必要がある場合は、エイリアスを使用できます。

```
# parentchart/requirements.yaml
dependencies:
  - name: subchart
    repository: http://localhost:10191
    version: 0.1.0
    alias: new-subchart-1
  - name: subchart
    repository: http://localhost:10191
    version: 0.1.0
    alias: new-subchart-2
  - name: subchart
    repository: http://localhost:10191
    version: 0.1.0
```
上の例では、parentchart に対して全部で3つの依存関係を得ます。
```
subchart
new-subchart-1
new-subchart-2
```
これを達成するための手動の方法は、ディレクトリ(charts/) 内の同じ chart を異なる名前で複数回コピー/貼り付けすることです。

## requirements.yamlのタグと条件フィールド

上記のフィールドに加えて、各要求項目にはオプションのフィールドタグと条件を含めることができます。

すべてのチャートはデフォルトでロードされています。タグまたは条件フィールドが存在する場合は、それらが評価され、適用されるチャートの読み込みを制御するために使用されます。

condition - condition フィールドは1つ以上のYAMLパス（コンマで区切られています）を保持します。 このパスが上位の親の値に存在し、ブール値に解決される場合、チャートはそのブール値に基づいて有効または無効になります。 リスト内で最初に見つかった有効なパスだけが評価され、パスが存在しない場合は条件に影響はありません。

tags - tags フィールドは、このチャートに関連付けるラベルのYAMLリストです。 上の親の値では、タグとブール値を指定して、タグ付きのすべてのチャートを有効または無効にすることができます。
```
# parentchart/requirements.yaml
dependencies:
  - name: subchart1
    repository: http://localhost:10191
    version: 0.1.0
    condition: subchart1.enabled,global.subchart1.enabled
    tags:
      - front-end
      - subchart1

  - name: subchart2
    repository: http://localhost:10191
    version: 0.1.0
    condition: subchart2.enabled,global.subchart2.enabled
    tags:
      - back-end
      - subchart2
```
```
# parentchart/values.yaml

subchart1:
  enabled: true
tags:
  front-end: false
  back-end: true
```

上記の例では、タグ(front-end) を持つすべてのチャートは無効になりますが、パス(subchart1.enabled) は親の値で 'true'と評価されるため、条件はフロントエンドタグをオーバーライドし、subchart1 が有効になります。

subchart2 はバックエンドでタグ付けされ、そのタグはtrueと評価されるので、subchart2 は有効になります。 また、subchart2 には requirements.yaml で指定された条件がありますが、親の値に対応するパスと値がないため、この条件は効果がありません。

### タグと条件でのCLIの使用

--setパラメータは、通常どおりタグと条件の値を変更するために使用できます。

```
helm install --set tags.front-end=true --set subchart2.enabled=false
```

### タグと条件の解決

* 条件（値に設定されている場合）は常にタグをオーバーライドします。
* そのチャートで最初に存在する条件パスとそれ以降のパスが無視されます。
* タグは「チャートのタグのいずれかが真の場合はチャートを有効にする」と評価されます。
* タグと条件の値は、上位の親の値に設定する必要があります。
* tags：キー入力値はトップレベルキーでなければなりません。 グローバルとネストタグ：テーブルは現在サポートされていません。

## requirements.yamlによる子値のインポート

場合によっては、子チャートの値を親チャートに伝達し、共通のデフォルトとして共有できるようにすることが望ましい場合があります。 エクスポート形式を使用することのもう1つの利点は、将来設定するツールがユーザー設定可能な値をイントロスペクトできるようになることです。

インポートする値を含むキーは、YAMLリストを使用して親チャートのrequirements.yamlファイルで指定できます。 リストの各項目は子チャートのエクスポートフィールドからインポートされるキーです。

エクスポートキーに含まれていない値をインポートするには、child-parent形式を使用します。 両方の形式の例を以下に説明します。

### エクスポートフォーマットを使用する

子チャートのvalues.yamlファイルのルートにエクスポートフィールドが含まれている場合、その内容は、以下の例のようにインポートするキーを指定することによって、親の値に直接インポートされます。

```
# parent's requirements.yaml file
    ...
    import-values:
      - data
```
```
# child's values.yaml file
...
exports:
  data:
    myint: 99
```
インポートリストでキーデータを指定しているので、Helmは子チャートのexportsフィールドでデータキーを探し、その内容をインポートします。

最終的な親の値はエクスポートされたフィールドを含みます。:
```
# parent's values file
...
myint: 99
```
親キーデータは、親の最終値には含まれていません。 親キーを指定する必要がある場合は、 'child-parent'形式を使用してください。

### Using the child-parent format

子チャートの値のエクスポートキーに含まれていない値にアクセスするには、インポートする値のソースキー（子）と、親チャートの値の宛先パス（親）を指定する必要があります。

以下の例のimport-valuesは、child：pathで見つかったすべての値を取り、それらをparentで指定されたパスの親の値にコピーするようにHelmに指示します。
```
# parent's requirements.yaml file
dependencies:
  - name: subchart1
    repository: http://localhost:10191
    version: 0.1.0
    ...
    import-values:
      - child: default.data
        parent: myimports
```
上記の例では、subchart1の値のdefault.dataにある値は、以下に詳しく説明するように、親チャートの値のmyimportsキーにインポートされます。
```
# parent's values.yaml file

myimports:
  myint: 0
  mybool: false
  mystring: "helm rocks!"
```
```
# subchart1's values.yaml file

default:
  data:
    myint: 999
    mybool: true
```
親チャートの結果の値は次のようになります。
```
# parent's final values

myimports:
  myint: 999
  mybool: true
  mystring: "helm rocks!"
```
親の最終値に、subchart1からインポートされたmyintフィールドとmyboolフィールドが含まれるようになりました。
