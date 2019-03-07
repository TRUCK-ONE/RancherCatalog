# ConfigMap の作成
## Create ConfigMaps from literal values(リテラル値からConfigMapsを作成)

kubectl create configmapを--from-literal引数とともに使用して、コマンドラインからリテラル値を定義できます。
```
kubectl create configmap special-config --from-literal=special.how=very --from-literal=special.type=charm
```
複数のキーと値のペアを渡すことができます。 コマンドラインで提供される各ペアは、ConfigMapのデータセクション内の個別のエントリとして表されます。
```
kubectl get configmaps special-config -o yaml
```
```
apiVersion: v1
data:
  special.how: very
  special.type: charm
kind: ConfigMap
metadata:
  creationTimestamp: 2016-02-18T19:14:38Z
  name: special-config
  namespace: default
  resourceVersion: "651"
  selfLink: /api/v1/namespaces/default/configmaps/special-config
  uid: dadce046-d673-11e5-8cd0-68f728db1985
 ``` 