# Custom Catalogs and Charts

Rancherのカタログサービスでは、カタログサービスがRancherで活用できるようにするために、カスタムカタログを特定のフォーマットで構造化する必要があります。 どのカスタムカタログも public Git repository でなければなりません。 URLは git clone が[扱うこと](https://git-scm.com/docs/git-clone#_git_urls_a_id_urls_a)ができ、.gitで終わらなければならないものである必要があります。

## Chart Types

Rancherは2種類のチャートをサポートしています。

* Helm Charts

    ネイティブヘルムチャートはそれを実行するために必要な他のソフトウェアと一緒にアプリケーションが含まれています。 ネイティブのHelmチャートをデプロイするときは、チャートのパラメータを学習し、Answersを使用してそれらを設定します。これらはキーと値のペアのセットです。  
    Helm StableとHelm Incubatorには、ネイティブのHelmチャートが表示されます。 ただし、カスタムカタログでネイティブのHelmチャートを使用することもできます（ただし、Rancherチャートをお勧めします）。
* Rancher Charts

    Rancherチャートはネイティブのhelmチャートを反映していますが、ユーザーエクスペリエンスを向上させる2つのファイル、app-readme.mdとquestions.yamlが追加されています。 Rancher Chart追加ファイルでそれらについての詳細を読んでください。

    Rancher Charts の利点は次のとおりです。

    * Enhanced Revision Tracking(改訂トラッキングの強化)
        
        Helmはバージョン管理されたデプロイメントをサポートしますが、Rancherは追跡と改訂履歴を追加してチャートの異なるバージョン間の変更を表示します。

    * Streamlined Application Launch(合理化されたアプリケーション起動)
        
        Rancher Charts は、カタログアプリケーションの展開を容易にするために、簡単なチャートの説明と設定フォームを追加します。 Rancherユーザーは、アプリケーションの起動方法を理解するためにHelm変数のリスト全体を読む必要はありません。
    
    * Application Resource Management(アプリケーションリソース管理)
    
        Rancherは特定のアプリケーションによって作成されたすべてのリソースを追跡します。 ユーザーは、アプリケーションの起動に使用されるすべてのワークロードオブジェクトを一覧表示したページに簡単に移動してトラブルシューティングできます。

## Chart の ディレクトリ構造

次の表は、チャートディレクトリにあるチャートのディレクトリ構造を示しています。charts/<APPLICATION>/<APP_VERSION>/。 この情報は、カスタムカタログのチャートをカスタマイズするときに役立ちます。 Rancher Specificと表示されているファイルはRancherチャートに固有のものですが、チャートのカスタマイズではオプションです。

```
charts/<APPLICATION>/<APP_VERSION>/
|--charts/           # 依存関係チャートを含むディレクトリ
|--templates/        # values.ymlと組み合わせるとKubernetes YAMLを生成するテンプレートを含むディレクトリ
|--app-readme.md     # Rancher UIのチャートヘッダーに表示されるテキスト*
|--Chart.yml         # (必須)ヘルムチャート情報ファイル
|--questions.yml     # Rancher UI内に表示されるフォームの質問。質問は設定オプションに表示されます。*
|--README.md         # (オプション)Rancher UI内に表示されるHelm Readmeファイル。 このテキストは詳細説明に表示されます。
|--requirements.yml  # (オプション)チャートの依存関係をリストしているYAMLファイル
|--values.yml        # charts のデフォルト設定値。
```

## Rancher Chart 追加ファイル

独自のカスタムカタログを作成する前に、Rancher Chart がネイティブの Helm Chart とどのように異なるのかについて基本的な理解が必要です。 Rancher Chart は、そのディレクトリ構造がHelm Chartとわずかに異なります。  Rancher Chart には、Helm Chart にはない2つのファイルが含まれています。

* `app-readme.md`
   
    チャートのUIヘッダーに説明テキストを提供するファイル。 次の図は、Rancher Chart（app-readme.mdを含む）とネイティブ Helm Chart（含まない）の違いを示しています。
    
    app-readme.md の ある　Rancher Chart (左)と なしの Helm Chart (右)

* `questions.yml`

    フォームに対する質問を含むファイル。 これらの質問形式は Chart の展開を簡単にします。 それがなければ、キーと値のペアを使用してデプロイメントを設定する必要がありますが、これはより困難です。 以下の画像は Rancher Chart（questions.ymlを含む）とネイティブ Helm Chart（含まない）の違いを示しています。
    
    questions.yml（左）のある Rancher Chart と（右）なしの Helm Chart

## Question Variable Reference(質問変数リファレンス)

このリファレンスはquestions.ymlで使用できる変数を含みます。

| Variable  | Type | Required  | 説明 |
| --------- | ---- | --------- | ---- |
| variable | string | true | ネストしたオブジェクトにはfoo.barを使ってファイル(values.yml) で指定された変数名を定義してください。 |
| label | string | true | UIラベルを定義します。 |
| description | string | false | 変数の説明を指定してください。 |
| type | string | false |　指定されていない場合、デフォルトはstringです（現在サポートされているタイプは、string、boolean、int、enum、password、storageclass、およびhostnameです） |
| required | bool | false | 変数が必須かどうかを定義します（true/false）|
| default | string | false | デフォルト値を指定してください。 |
| group | string | false | 入力値によって質問をグループ化します。 |
| min_length | int | false | 最小文字長 |
| max_length | int | false | 最大文字長 |
| min | int | false | 最小整数長 |
| max | int | false | 最大整数長 |
| options | []string | false | 変数タイプがenumの場合にオプションを指定します。|
| valid_chars | string | false | 入力文字検証のための正規表現 |
| invalid_chars | string | false | 無効な入力文字検証のための正規表現 |
| subquestions | []subquestion | false | 副問い合わせの配列を追加します。 |
| show_if | string | false | 条件変数が真の場合、現在の変数を表示します。 例えば、show_if： "serviceType = Nodeport"です。 |
| show_subquestion_if | string | false | trueであるか、オプションの1つと等しい場合は、サブ質問を表示します。 例えばshow_subquestion_if： "true"です。|

**注意:** subquestions []にsubquestionsまたはshow_subquestions_ifキーを含めることはできませんが、上記の表の他のすべてのキーはサポートされています。

## Example Custom Chart Creation( カスタムチャート作成の例)

カスタムカタログには Helm Charts と Rancher Charts のどちらでも入力できますが、ユーザーエクスペリエンスが向上しているため、Rancher Charts をお勧めします。

**注意:** チャート開発の完全なチュートリアルについては、上流の [Helm Chart 開発者リファレンス](https://docs.helm.sh/developing_charts/)を参照してください。

**1:** カスタムカタログとして使用しているGitHubリポジトリ内に、Chart Directory Structureにリストされている構造を反映したディレクトリ構造を作成します。

Rancher には、このディレクトリ構造が必要ですが、app-readme.md と questions.yml はオプションです。

**Tip:**
    
* chartのカスタマイズを開始するには、Rancher Library または Helm Stable のいずれかからchart をコピーします。
* チャート開発の詳細については、上流の [Helm Chart 開発者リファレンス](https://docs.helm.sh/developing_charts/)を参照してください。

**2:** (推奨) ファイル(app-readme.md) を作成してください。

このファイルを使用して、Rancher UIのチャートのヘッダーにカスタムテキストを作成します。 このテキストを使用して、チャートがご使用の環境用にカスタマイズされていることをユーザーに通知したり、それを使用する方法について特別な指示を与えることができます。

**3:** (推奨) ファイル(questions.yml) を作成してください。

このファイルは、ユーザーがカスタムチャートを展開するときに展開パラメーターを指定するためのフォームを作成します。 このファイルがないと、ユーザーはキーと値のペアを使用して手動でパラメータを指定する必要がありますが、これは使い勝手がよくありません。

以下の例では、ユーザーに固定ボリュームサイズとストレージクラスの入力を求めるフォームを作成します。

ファイル(questions.yml) を作成するときに使用できる変数のリストは[Question Variable Reference](https://rancher.com/docs/rancher/v2.x/en/catalog/custom/#question-variable-reference)を参照してください。

```
   categories:
    - Blog
    - CMS
    questions:
    - variable: persistence.enabled
    default: "false"
    description: "Enable persistent volume for WordPress"
    type: boolean
    required: true
    label: WordPress Persistent Volume Enabled
    show_subquestion_if: true
    group: "WordPress Settings"
    subquestions:
    - variable: persistence.size
        default: "10Gi"
        description: "WordPress Persistent Volume Size"
        type: string
        label: WordPress Volume Size
    - variable: persistence.storageClass
        default: ""
        description: "If undefined or null, uses the default StorageClass. Default to null"
        type: storageclass
        label: Default StorageClass for WordPress
```

**4:** カスタマイズしたチャートをGitHubリポジトリにチェックインします。

**結果:** カスタムチャートがリポジトリに追加されます。 あなたのRancherサーバーは数分以内にチャートを複製します。