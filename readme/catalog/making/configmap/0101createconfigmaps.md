# ConfigMap の作成
## Create ConfigMaps from files(ファイルからConfigMapsを作成)

`kubectl create configmap` を使用して、個々のファイルから、または複数のファイルからConfigMapを作成できます。

例：

```
kubectl create configmap game-config-2 --from-file=configure-pod-container/configmap/kubectl/game.properties
```

次のConfigMapが生成されます。

```
kubectl describe configmaps game-config-2
Name:           game-config-2
Namespace:      default
Labels:         <none>
Annotations:    <none>

Data
====
game.properties:        158 bytes
```

--from-file引数を複数回渡して、複数のデータソースからConfigMapを作成できます。
```
kubectl create configmap game-config-2 --from-file=configure-pod-container/configmap/kubectl/game.properties --from-file=configure-pod-container/configmap/kubectl/ui.properties
```

```
kubectl describe configmaps game-config-2
Name:           game-config-2
Namespace:      default
Labels:         <none>
Annotations:    <none>

Data
====
game.properties:        158 bytes
ui.properties:          83 bytes
```

envファイルからConfigMapを作成するには、--from-env-fileオプションを使用します。次に例を示します。

```
# Env-files contain a list of environment variables.
# These syntax rules apply:
#   Each line in an env file has to be in VAR=VAL format.
#   Lines beginning with # (i.e. comments) are ignored.
#   Blank lines are ignored.
#   There is no special handling of quotation marks (i.e. they will be part of the ConfigMap value)).

wget https://k8s.io/docs/tasks/configure-pod-container/configmap/kubectl/game-env-file.properties -O configure-pod-container/configmap/kubectl/game-env-file.properties
cat configure-pod-container/configmap/kubectl/game-env-file.properties
enemies=aliens
lives=3
allowed="true"

# This comment and the empty line above it are ignored
```

```
kubectl create configmap game-config-env-file \
        --from-env-file=configure-pod-container/configmap/kubectl/game-env-file.properties
```

次のConfigMapが生成されます。

```
kubectl get configmap game-config-env-file -o yaml
```

```
apiVersion: v1
data:
  allowed: '"true"'
  enemies: aliens
  lives: "3"
kind: ConfigMap
metadata:
  creationTimestamp: 2017-12-27T18:36:28Z
  name: game-config-env-file
  namespace: default
  resourceVersion: "809965"
  selfLink: /api/v1/namespaces/default/configmaps/game-config-env-file
  uid: d9d1ca5b-eb34-11e7-887b-42010a8002b8
```

--from-env-fileを複数回渡して複数のデータソースからConfigMapを作成する場合、最後のenv-fileのみが使用されます。

```
wget https://k8s.io/docs/tasks/configure-pod-container/configmap/kubectl/ui-env-file.properties -O configure-pod-container/configmap/kubectl/ui-env-file.properties
kubectl create configmap config-multi-env-files \
        --from-env-file=configure-pod-container/configmap/kubectl/game-env-file.properties \
        --from-env-file=configure-pod-container/configmap/kubectl/ui-env-file.properties
```

次のConfigMapが生成されます。

```
kubectl get configmap config-multi-env-files -o yaml
```

```
apiVersion: v1
data:
  color: purple
  how: fairlyNice
  textmode: "true"
kind: ConfigMap
metadata:
  creationTimestamp: 2017-12-27T18:38:34Z
  name: config-multi-env-files
  namespace: default
  resourceVersion: "810136"
  selfLink: /api/v1/namespaces/default/configmaps/config-multi-env-files
  uid: 252c4572-eb35-11e7-887b-42010a8002b8
```
