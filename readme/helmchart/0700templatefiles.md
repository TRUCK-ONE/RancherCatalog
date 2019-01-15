## Template Files

テンプレートファイルはGoテンプレートを書くための標準的な規約に従います（詳細は [Go text/template パッケージのドキュメント](https://golang.org/pkg/text/template/)を参照してください）。 テンプレートファイルの例は次のようになります。

```
apiVersion: v1
kind: ReplicationController
metadata:
  name: deis-database
  namespace: deis
  labels:
    app.kubernetes.io/managed-by: deis
spec:
  replicas: 1
  selector:
    app.kubernetes.io/name: deis-database
  template:
    metadata:
      labels:
        app.kubernetes.io/name: deis-database
    spec:
      serviceAccount: deis-database
      containers:
        - name: deis-database
          image: {{.Values.imageRegistry}}/postgres:{{.Values.dockerTag}}
          imagePullPolicy: {{.Values.pullPolicy}}
          ports:
            - containerPort: 5432
          env:
            - name: DATABASE_STORAGE
              value: {{default "minio" .Values.storage}}
```

[https://github.com/deis/charts](https://github.com/deis/charts)に大まかに基づいている上記の例は、Kubernetesレプリケーションコントローラのテンプレートです。 次の4つのテンプレート値（通常はファイル(values.yaml) に定義されています）を使用できます。

* imageRegistry：Docker image のソースレジストリ
* dockerTag：docker image のタグ
* pullPolicy：Kubernetes プルポリシー
* storage：デフォルトで "minio"に設定されているストレージバックエンド

これらの値はすべてテンプレート作成者によって定義されます。 Helmはパラメータを要求も指示もしません。

多くの作業チャートを見るには、[Helm Chartsプロジェクト](https://github.com/helm/charts)を調べてください。
