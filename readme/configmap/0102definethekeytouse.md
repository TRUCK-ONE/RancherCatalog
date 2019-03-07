# ConfigMap の作成
## Define the key to use when creating a ConfigMap from a file(ファイルからConfigMapを作成するときに使用するキーの定義)

--from-file　引数を使用するときに、ConfigMapのデータセクションで使用するファイル名以外のキーを定義できます。
```
kubectl create configmap game-config-3 --from-file=<my-key-name>=<path-to-file>
```
ここで、`<my-key-name>` はConfigMapで使用するキー、`<path-to-file>` はキーに表示するデータソースファイルの場所です。
例：
```
kubectl create configmap game-config-3 --from-file=game-special-key=configure-pod-container/configmap/kubectl/game.properties

kubectl get configmaps game-config-3 -o yaml
```
```
apiVersion: v1
data:
  game-special-key: |
    enemies=aliens
    lives=3
    enemies.cheat=true
    enemies.cheat.level=noGoodRotten
    secret.code.passphrase=UUDDLRLRBABAS
    secret.code.allowed=true
    secret.code.lives=30
kind: ConfigMap
metadata:
  creationTimestamp: 2016-02-18T18:54:22Z
  name: game-config-3
  namespace: default
  resourceVersion: "530"
  selfLink: /api/v1/namespaces/default/configmaps/game-config-3
  uid: 05f8da22-d671-11e5-8cd0-68f728db1985
```