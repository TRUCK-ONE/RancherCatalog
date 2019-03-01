# Service Discovery

作成されたワークロードごとに、補足的なService Discoveryエントリが作成されます。
このService Discoveryエントリは、`<workload>.<namespace>.svc.cluster.local`という命名規則を使用して、ワークロードのポッドに対するDNS解決を有効にします。

ただし、追加のService Discoveryレコードを作成することもできます。
これらの追加レコードを使用して、特定のネームスペースが1つ以上の外部IPアドレス、外部ホスト名、別のDNSレコードへのエイリアス、他のワークロード、または作成したセレクタに一致する一連のPodで解決されます。

1. **Global** viewから、DNSレコードを追加するプロジェクトを開きます。
1. [Service Discovery]タブを選択します。
    次にレコードの追加をクリックします。
1. DNSレコードの名前を入力してください。
    この名前はDNS解決に使用されます。
1. ドロップダウンリストからネームスペースを選択します。
    または、[新しいネームスペースに追加]をクリックして、新しいネームスペースをその場で作成できます。
1. DNSレコードに要求をルーティングするには、[解決先]オプションの1つを選択します。
    - 1つ以上の外部IPアドレス  
    [Target IP Addresses]フィールドにIPアドレスを入力します。
    [ターゲットIPの追加]をクリックしてIPアドレスを追加します。
    - 外部ホスト名  
    ホスト名を入力してください。
    - 他のDNSレコードのエイリアス  
    [Add Target Record]をクリックして、[Value]ドロップダウンから別のDNSレコードを選択します。
    - 1つ以上のワークロード  
    [Add Target Workload]をクリックして、[Value]ドロップダウンから別のワークロードを選択します。
    - セレクタに一致するポッドのセット  
    パラメータに一致するすべてのポッドのレコードを作成するには、ラベルセレクタのキーと値のペアを入力します。
1. 作成をクリック

結果：新しいDNSレコードが作成されます。

- プロジェクトの[Service Discovery]タブからレコードを表示できます。
- 作成した新しいレコードの新しいDNS名（`<recordname>.<namespace> .svc.cluster.local`）にアクセスすると、選択した名前空間が解決されます。

## Related Links(関連リンク)
- [Adding entries to Pod /etc/hosts with HostAliases](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
