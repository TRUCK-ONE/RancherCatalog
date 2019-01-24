# Understanding ConfigMaps and Pods

ConfigMap APIリソースは、構成データをキーと値のペアとして格納します。 データはポッドで消費されるか、コントローラなどのシステムコンポーネントの設定を提供します。 ConfigMapはSecretsと似ていますが、機密情報を含まない文字列を扱う手段を提供します。 ユーザーとシステムコンポーネントは、ConfigMapに構成データを保存できます。

**注：** ConfigMapsはプロパティファイルを参照し、それらを置き換えないでください。 ConfigMapは、Linuxの/ etcディレクトリとその内容に似たものを表していると考えてください。 たとえば、ConfigMapからKubernetesボリュームを作成した場合、ConfigMap内の各データ項目はボリューム内の個々のファイルによって表されます。

ConfigMapのデータフィールドには設定データが含まれています。 以下の例に示すように、これは--from-literalを使用して定義された個々のプロパティのような単純なもの、または--from-fileを使用して定義されたJSON BLOBのような複雑なものです。
```
kind: ConfigMap
apiVersion: v1
metadata:
  creationTimestamp: 2016-02-18T19:14:38Z
  name: example-config
  namespace: default
data:
  # example of a simple property defined using --from-literal
  example.property.1: hello
  example.property.2: world
  # example of a complex property defined using --from-file
  example.property.file: |-
    property.1=value-1
    property.2=value-2
    property.3=value-3
```
# Restrictions

* Pod仕様でConfigMapを参照する前に、ConfigMapを作成する必要があります（ConfigMapを「オプショナル」とマークしていない限り）。 存在しないConfigMapを参照した場合、Podは起動しません。 同様に、ConfigMapに存在しないキーを参照すると、ポッドが起動しなくなります。

* envFromを使用してConfigMapsから環境変数を定義した場合、無効と見なされたキーはスキップされます。 ポッドの起動は許可されますが、無効な名前はイベントログに記録されます（InvalidVariableNames）。 ログメッセージはスキップされた各キーをリストします。

例：
```
kubectl get events
   LASTSEEN FIRSTSEEN COUNT NAME          KIND  SUBOBJECT  TYPE      REASON                            SOURCE                MESSAGE
   0s       0s        1     dapi-test-pod Pod              Warning   InvalidEnvironmentVariableNames   {kubelet, 127.0.0.1}  Keys [1badkey, 2alsobad] from the EnvFrom configMap default/myconfig were skipped since they are considered invalid environment variable names.
```
* ConfigMapは特定のネームスペースにあります。 ConfigMapは、同じネームスペースに存在するポッドによってのみ参照できます。

* Kubeletは、APIサーバーに見つからないポッドに対するConfigMapsの使用をサポートしていません。 これには、Kubeletの-manifest-urlフラグ、-configフラグ、またはKubelet REST APIを介して作成されたポッドが含まれます。

**注意：** これらはポッドを作成する一般的な方法ではありません。
