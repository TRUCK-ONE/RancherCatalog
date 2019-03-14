# OpenEBS

[参照](https://github.com/openebs/openebs#contributing)

https://www.openebs.io/

**OpenEBS** は、ミッションクリティカルで持続的なワークロードや、ロギングやプロメテウスなどの他のステートフルワークロードにコンテナを使用できます。
OpenEBSはコンテナ化されたストレージと関連するストレージサービスです。

OpenEBSでは、他のコンテナと同じように、コンテナ上のDBなど、永続的なワークロードコンテナを扱うことができます。 OpenEBS自体はホスト上の単なる別のコンテナとしてデプロイされ、ポッド、アプリケーション、クラスタ、またはコンテナごとのレベルで指定できるストレージサービスを可能にします。

- ノード間でのデータの永続性。
    たとえばCassandraリングの再構築にかかる時間を劇的に短縮します。
- 可用性ゾーンとクラウドプロバイダ間でデータを同期することで、可用性を向上させたり、アタッチ/デタッチ時間を短縮したりできます。
- AKS、ベアメタル、GKE、AWSのいずれで実行しているかにかかわらず、共通の層 - ストレージサービスの配線と開発者のエクスペリエンスは可能な限り似ています。
- Kubernetesとの統合。
    開発者とアプリケーションの意図が自動的にOpenEBS構成に流れ込みます。
- S3と他のターゲットとの間の階層化の管理。

私たちのビジョンは単純です。
持続的なワークロード用のストレージとストレージサービスを完全に環境に統合して、各チームとワークロードが制御の細分性とKubernetesのネイティブ動作から恩恵を受けるようにします。

## Scalability

OpenEBSは、任意の数のコンテナ化されたストレージコントローラを含めるように拡張できます。
Kubernetesは、在庫にetcdを使用するなどの基本的な部分を提供するために使用されます。
OpenEBSはあなたのKubernetesが拡大縮小する範囲で拡大縮小します。

## Installation and Getting Started

OpenEBSはいくつかの簡単な手順で設定できます。
Kubernetesノードにopen-iscsiをインストールし、kubectlを使ってopenebs-operatorを実行することで、Kubernetesクラスタを選択していくことができます。

### Start the OpenEBS Services using operator

```
# apply this yaml
kubectl apply -f https://openebs.github.io/charts/openebs-operator.yaml
```

### Apply OpenEBS StorageClasses

```
# apply this yaml
kubectl apply -f https://openebs.github.io/charts/openebs-storageclasses.yaml
```

また、[クイックスタートガイド](https://docs.openebs.io/docs/overview.html)に従うこともできます。

OpenEBSは、クラウド、オンプレミス、または開発者用ラップトップ（ミニキューブ）のいずれかのKubernetesクラスタにデプロイできます。
OpenEBSがユーザー空間で動作するために必要となる基礎となるカーネルへの変更はありません。
OpenEBSセットアップのドキュメントに従ってください。
また、サンプルのKubernetesデプロイメントとOpenEBSのパフォーマンスをシミュレートするために使用できる合成負荷を含むVagrant環境もあります。
Kubernetesでのステートフルなワークロードのためのカオスエンジニアリングを手助けするLitmus（https://www.openebs.io/litmus） と呼ばれる関連プロジェクトも興味深いでしょう。

## Status

我々は積極的な開発が進行中でベータ段階に近づいています。
詳しくはプロジェクトトラッカーをご覧ください。
多くのユーザーが本番環境でOpenEBSを実行しており、早期アクセスの商用ソリューションは2018年9月に当社のメインスポンサーであるMayaData（https://www.mayadata.io） によって提供されました。

## Contributing

OpenEBSはあなたの意見や貢献をあらゆる形で歓迎します。

- Slackに参加してください
    - すでにサインアップしていますか？#openebs-usersでの議論に向かいます
- 問題を提起したいですか。
    - それが一般的なものである（または本当によくわからない）場合でも、問題でそれを提起することができます。
    - プロジェクト（リポジトリ）固有の問題もissueで発生し、repo/maya のような個々のリポジトリラベルでタグ付けすることができます。
- 修正や機能を手助けしたいですか。
    - 未解決の問題を参照してください。
    - 投稿ガイドを見る。
    - 私たちのコミュニティに参加したい、これをチェックしてください。

## Show me the Code

これはOpenEBS用のメタリポジトリです。
ソースコードは次の場所にあります。
- 初期ストレージエンジンのソースコードは [openebs/jiva](https://github.com/openebs/jiva) にあります。
- ストレージオーケストレーションのソースコードが[openebs/maya](https://github.com/openebs/maya) の下にある。
- jivaとmayaには大量のソースコードが含まれていますが、オーケストレーションと自動化のコードの一部はOpenEBS組織の下の他のリポジトリにも配布されています。

ピン留めされたリポジトリか [OpenEBS Architecture](https://github.com/openebs/openebs/blob/master/contribute/design/README.md) ドキュメントから始めてください。

## License

OpenEBSはプロジェクトレベルでApache 2.0ライセンスの下で開発されています。
プロジェクトの一部のコンポーネントは他のオープンソースプロジェクトから派生したものであり、それぞれのライセンスの下で配布されています。

