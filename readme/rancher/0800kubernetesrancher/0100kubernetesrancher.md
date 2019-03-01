# Kubernetes in Rancher

Rancherでクラスターをプロビジョニングした後は、強力なKubernetes機能を使用して、開発環境、テスト環境、または実稼働環境でコンテナー化アプリケーションをデプロイおよび拡張することができます。

## Interacting with Clusters
- Rancher UI  
    Rancherはあなたのクラスタとやり取りするための直感的なユーザーインターフェースを提供します。UIで利用可能なすべてのオプションはRancher APIを使用します。
    したがって、UIで可能なアクションはすべてRancher CLIまたはRancher APIでも可能です。
- kubectl  
    Kubernetesコマンドラインツールのkubectlを使ってクラスタを管理できます。
    kubectlを使用する方法は2つあります。
    - Rancher kubectl shell  
    Rancher UIで利用可能なkubectlシェルを起動して、クラスターと対話します。
    このオプションでは、ユーザー側で設定作業は不要です。
    詳しくは、kubectlシェルを使用したクラスターへのアクセスを参照してください。
    - Terminal remote connection  
    ローカルデスクトップにkubectlをインストールしてから、クラスタのkubeconfigファイルをローカルの `〜/.kube/config` ディレクトリにコピーすることで、クラスタと対話することもできます。

    詳しくは、kubectlおよびkubeconfigファイルを使用したクラスターへのアクセスを参照してください。
- Rancher CLI  
    Rancher独自のコマンドラインインターフェイスRancher CLIをダウンロードすることで、クラスタを制御できます。
    このCLIツールは、さまざまなクラスターやプロジェクトと直接対話したり、それらにkubectlコマンドを渡したりすることができます。
- Rancher API  
    最後に、Rancher APIを介してクラスターと対話することができます。
    APIを使用する前に、APIキーを入手する必要があります。
    APIオブジェクトのさまざまなリソースフィールドとアクションを表示するには、API UIを開きます。
    これには、任意のRancher UIオブジェクトの **[View in API]** をクリックしてアクセスできます。

## Editing Clusters

---

以下略