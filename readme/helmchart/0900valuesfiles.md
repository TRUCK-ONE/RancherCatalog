## Values files

前のセクションのテンプレートを考慮すると、必要な値を提供するファイル(values.yaml) は次のようになります。
```
imageRegistry: "quay.io/deis"
dockerTag: "latest"
pullPolicy: "Always"
storage: "s3"
```
値ファイルはYAMLでフォーマットされています。 チャートにはデフォルトのファイル(values.yaml) を含めることができます。 コマンド(Helm install) を使用すると、ユーザーは追加のYAML値を指定して値をオーバーライドできます。
```
$ helm install --values=myvals.yaml wordpress
```
値がこの方法で渡されると、それらはデフォルト値ファイルにマージされます。 たとえば、次のようなファイル(myvals.yaml) を考えます。
```
storage: "gcs"
```
これをチャートの values.yaml とマージすると、生成されるコンテンツは次のようになります。
```
imageRegistry: "quay.io/deis"
dockerTag: "latest"
pullPolicy: "Always"
storage: "gcs"
```
最後のフィールドだけがオーバーライドされたことに注意してください。

**注意：** チャートの内部に含まれるデフォルト値ファイルは values.yaml という名前でなければなりません。 しかし、コマンドラインで指定されたファイルは何でも名前を付けることができます。

**注意：** --setフラグがhelmインストールまたはhelmアップグレードで使用されている場合、それらの値はクライアント側で単純にYAMLに変換されます。

**注意：** valuesファイルに必要なエントリが存在する場合は、 ['required'関数](https://github.com/helm/helm/blob/master/docs/charts_tips_and_tricks.md)を使用してそれらをチャートテンプレートで必要に応じて宣言できます。

これらの値はすべて、.Valuesオブジェクトを使用してテンプレート内からアクセスできます。

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
