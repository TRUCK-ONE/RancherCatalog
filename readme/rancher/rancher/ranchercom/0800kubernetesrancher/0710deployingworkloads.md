# Deploying Workloads

1つ以上のコンテナーでアプリケーションを実行するためのワークロードをデプロイします。

1. **グローバル**ビューから、ワークロードをデプロイしたいプロジェクトを開きます。

1. **ワークロード**ビューから、**デプロイ**をクリックします。

1. ワークロードの**名前**を入力してください。

1. [ワークロードの種類](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/workloads/)を選択します。
**その他のオプション**をクリックしてワークロードの種類を変更できるため、ワークロードはデフォルトでスケーラブルな展開になります。

1. **Dockerイメージ**フィールドから、プロジェクトにデプロイしたいDockerイメージの名前を入力します。
展開中に、Rancherは [Docker Hub](https://hub.docker.com/search/?q=&type=image) からこのイメージを取得します。
Docker Hubに表示されるとおりに名前を入力してください。

1. 既存の[ネームスペース](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/projects-and-namespaces/#namespaces)を選択するか、**新しいネームスペースに追加**をクリックして新しいネームスペースを入力します。

1. **ポートの追加**をクリックしてポートマッピングを入力します。これにより、クラスタの内外でアプリケーションにアクセスできます。
詳しくは、[サービス](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/workloads/#services)を参照してください。

1. 残りのオプションを設定します。
    - **Environment Variables(環境変数)**
        
        このセクションを使用して、ワークロードがオンザフライで消費するための環境変数を指定するか、またはシークレットや[ConfigMap](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/configmaps/)などの別のソースからそれらを取得します。
    
    - **Node Scheduling**

    - **Health Check**

    - **Volumes**

        このセクションを使用して、ワークロード用のストレージを追加します。
        追加するボリュームを手動で指定したり、永続的なボリューム要求を使用してワークロード用のボリュームを動的に作成したり、使用するボリュームのデータを [ConfigMap](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/configmaps/) などのファイルから読み取ることができます。

    - **Scaling/Upgrade Policy**

        > **Amazon Note for Volumes:**  
        > Amazon EBSボリュームをマウントするには
        > 
        > [Amazon AWS](https://aws.amazon.com/jp/)では、ノードは同じアベイラビリティーゾーン内にあり、ボリュームをアタッチ/アタッチ解除するためのIAMアクセス許可を所有している必要があります。
        >  
        > クラスターは[AWSクラウドプロバイダー](https://kubernetes.io/docs/concepts/cluster-administration/cloud-providers/#aws)オプションを使用している必要があります。
        > このオプションを有効にする方法の詳細については、[Amazon EC2クラスターの作成](https://rancher.com/docs/rancher/v2.x/en/cluster-provisioning/rke-clusters/node-pools/ec2/)または[カスタムクラスターの作成](https://rancher.com/docs/rancher/v2.x/en/cluster-provisioning/custom-clusters/)を参照してください。

1. **詳細オプションを表示**をクリックして設定します。
    - **Command**
    - **Networking**
    - **Labels & Annotations**
    - **Security and Host Config**

1. **起動**をクリックします。

**結果：** ワークロードは選択されたネームスペースにデプロイされます。
プロジェクトの**ワークロード**ビューからワークロードのステータスを確認できます。

