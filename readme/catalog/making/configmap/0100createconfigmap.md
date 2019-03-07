# ConfigMap の作成

コマンド `kubectl create configmap` を使用して、ディレクトリー、ファイル、またはリテラル値から構成マップを作成します。

```
kubectl create configmap <map-name> <data-source>
```

ここで、`<map-name>` は ConfigMap に割り当てる名前、`<data-source>` はデータを取得するディレクトリ、ファイル、またはリテラル値です。

データソースはConfigMapのキーと値のペアに対応します。ここで、

* key =コマンドラインで指定したファイル名またはキー
* value =ファイルの内容またはコマンドラインで指定したリテラル値。

`kubectl describe` または `kubectl get` を使用して、ConfigMap に関する情報を取得できます。

## Create ConfigMaps from directories(ディレクトリからConfigMapsを作成する)

`kubectl create configmap`を使用して、同じディレクトリ内の複数のファイルから ConfigMap を作成できます。

例：

```
mkdir -p configure-pod-container/configmap/kubectl/
wget https://k8s.io/docs/tasks/configure-pod-container/configmap/kubectl/game.properties -O configure-pod-container/configmap/kubectl/game.properties
wget https://k8s.io/docs/tasks/configure-pod-container/configmap/kubectl/ui.properties -O configure-pod-container/configmap/kubectl/ui.properties
kubectl create configmap game-config --from-file=configure-pod-container/configmap/kubectl/
```

ディレクトリ(configure-pod-container/configmap/kubectl/) の内容を結合します

```
ls configure-pod-container/configmap/kubectl/
game.properties
ui.properties
```

次のConfigMapに入れます。

```
kubectl describe configmaps game-config
Name:           game-config
Namespace:      default
Labels:         <none>
Annotations:    <none>

Data
====
game.properties:        158 bytes
ui.properties:          83 bytes
```

ディレクトリ(configure-pod-container/configmap/kubectl/) 内のファイル (game.properties) とファイル (ui.properties)は、ConfigMapのデータセクションにあります。

```
kubectl get configmaps game-config -o yaml
```

```
apiVersion: v1
data:
  game.properties: |
    enemies=aliens
    lives=3
    enemies.cheat=true
    enemies.cheat.level=noGoodRotten
    secret.code.passphrase=UUDDLRLRBABAS
    secret.code.allowed=true
    secret.code.lives=30
  ui.properties: |
    color.good=purple
    color.bad=yellow
    allow.textmode=true
    how.nice.to.look=fairlyNice
kind: ConfigMap
metadata:
  creationTimestamp: 2016-02-18T18:52:05Z
  name: game-config
  namespace: default
  resourceVersion: "516"
  selfLink: /api/v1/namespaces/default/configmaps/game-config
  uid: b4952dc3-d670-11e5-8cd0-68f728db1985
```

