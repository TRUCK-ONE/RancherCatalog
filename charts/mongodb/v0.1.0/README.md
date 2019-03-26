# MongoDB

[MongoDB](https://www.mongodb.com/)は、クロスプラットフォームのドキュメント指向データベースです。 NoSQLデータベースとして分類されたMongoDBは、動的スキーマを持つJSONのような文書を優先して従来のテーブルベースのリレーショナルデータベース構造を避け、特定の種類のアプリケーションでのデータの統合をより簡単かつ迅速にします。

追記：
この Chart は、MongoDB のオフィシャルではなく、[Bitnami](https://bitnami.com/)が、提供しているプロジェクトを使用しております。

## [更新履歴](history.md)

- ver0.1.0  
  mongoDB を 4.0.6 から 4.1.8 に  

## TL;DR;

```bash
$ helm install stable/mongodb
```

## 前書き

この Chart は、[Helm](https://helm.sh)パッケージマネージャを使用して、[Kubernetes](http://kubernetes.io)クラスタ上の[MongoDB](https://github.com/bitnami/bitnami-docker-mongodb)デプロイメントをブートストラップします。

[Bitnami](https://bitnami.com/)チャートは、クラスタ内のHelmチャートの展開と管理のために[Kubeapps](https://kubeapps.com/)と一緒に使用できます。

## 前提条件

- Kubernetes 1.4+ with Beta APIs enabled
- PV provisioner support in the underlying infrastructure

## Chart の インストール

チャートをリリース名 `my-release` でインストールするには、次の手順を実行します。

```bash
$ helm install --name my-release stable/mongodb
```

このコマンドは、デフォルト設定のKubernetesクラスタにMongoDBをデプロイします。 構成セクションには、インストール中に構成できるパラメーターがリストされています。

> **Tip**: `helm list` を使ってすべてのリリースを一覧表示する

## Chart の アンインストール

`my-release` デプロイメントをアンインストール/削除するには：

```bash
$ helm delete my-release
```

このコマンドは、チャートに関連付けられているすべてのKubernetesコンポーネントを削除し、リリースを削除します。

## Configuration

次の表は、MongoDBチャートの設定可能なパラメータとそれらのデフォルト値の一覧です。

| Parameter                                          | 説明                                                                                  | Default                                                 |
| -------------------------------------------------- | -------------------------------------------------------------------------------------------- | ------------------------------------------------------- |
| `global.imageRegistry`                             | Global Docker image registry                                                                 | `nil`                                                   |
| `image.registry`                                   | MongoDB image registry                                                                       | `docker.io`                                             |
| `image.repository`                                 | MongoDB Image name                                                                           | `bitnami/mongodb`                                       |
| `image.tag`                                        | MongoDB Image tag                                                                            | `{VERSION}`                                             |
| `image.pullPolicy`                                 | Image pull policy                                                                            | `Always`                                                |
| `image.pullSecrets`                                | Specify docker-registry secret names as an array                                             | `[]` (does not add image pull secrets to deployed pods) |
| `image.debug`                                      | デバッグログを有効にするかどうかを指定                                                   | `false`                                                 |
| `usePassword`                                      | パスワード認証を有効にする                                                               | `true`                                                  |
| `existingSecret`                                   | Existing secret with MongoDB credentials                                                     | `nil`                                                   |
| `mongodbRootPassword`                              | MongoDB admin password                                                                       | `random alphanumeric string (10)`                       |
| `mongodbUsername`                                  | MongoDB custom user                                                                          | `nil`                                                   |
| `mongodbPassword`                                  | MongoDB custom user password                                                                 | `random alphanumeric string (10)`                       |
| `mongodbDatabase`                                  | Database to create                                                                           | `nil`                                                   |
| `mongodbEnableIPv6`                                | MongoDBでIPv6を有効/無効に切り替える                                                    | `true`                                                  |
| `mongodbSystemLogVerbosity`                        | MongoDBシステムログの詳細レベル                                                          | `0`                                                     |
| `mongodbDisableSystemLog`                          | MongoDBシステムログを無効にするかどうか                                                 | `false`                                                 |
| `mongodbExtraFlags`                                | MongoDBの追加のコマンドラインフラグ                                                        | []                                                      |
| `service.annotations`                              | Kubernetes service annotations                                                               | `{}`                                                    |
| `service.type`                                     | Kubernetes Service type                                                                      | `ClusterIP`                                             |
| `service.clusterIP`                                | Static clusterIP or None for headless services                                               | `nil`                                                   |
| `service.nodePort`                                 | Port to bind to for NodePort service type                                                    | `nil`                                                   |
| `port`                                             | MongoDB service port                                                                         | `27017`                                                 |
| `replicaSet.enabled`                               | Switch to enable/disable replica set configuration                                           | `false`                                                 |
| `replicaSet.name`                                  | Name of the replica set                                                                      | `rs0`                                                   |
| `replicaSet.useHostnames`                          | Enable DNS hostnames in the replica set config                                               | `true`                                                  |
| `replicaSet.key`                                   | Key used for authentication in the replica set                                               | `nil`                                                   |
| `replicaSet.replicas.secondary`                    | Number of secondary nodes in the replica set                                                 | `1`                                                     |
| `replicaSet.replicas.arbiter`                      | Number of arbiter nodes in the replica set                                                   | `1`                                                     |
| `replicaSet.pdb.minAvailable.primary`              | PDB for the MongoDB Primary nodes                                                            | `1`                                                     |
| `replicaSet.pdb.minAvailable.secondary`            | PDB for the MongoDB Secondary nodes                                                          | `1`                                                     |
| `replicaSet.pdb.minAvailable.arbiter`              | PDB for the MongoDB Arbiter nodes                                                            | `1`                                                     |
| `podAnnotations`                                   | Annotations to be added to pods                                                              | {}                                                      |
| `podLabels`                                        | Additional labels for the pod(s).                                                            | {}                                                      |
| `resources`                                        | Pod resources                                                                                | {}                                                      |
| `priorityClassName`                                | Pod priority class name                                                                      | ``                                                      |
| `nodeSelector`                                     | Node labels for pod assignment                                                               | {}                                                      |
| `affinity`                                         | Affinity for pod assignment                                                                  | {}                                                      |
| `tolerations`                                      | Toleration labels for pod assignment                                                         | {}                                                      |
| `securityContext.enabled`                          | Enable security context                                                                      | `true`                                                  |
| `securityContext.fsGroup`                          | Group ID for the container                                                                   | `1001`                                                  |
| `securityContext.runAsUser`                        | User ID for the container                                                                    | `1001`                                                  |
| `persistence.enabled`                              | Use a PVC to persist data                                                                    | `true`                                                  |
| `persistence.storageClass`                         | Storage class of backing PVC                                                                 | `nil` (uses alpha storage class annotation)             |
| `persistence.accessMode`                           | Use volume as ReadOnly or ReadWrite                                                          | `ReadWriteOnce`                                         |
| `persistence.size`                                 | Size of data volume                                                                          | `8Gi`                                                   |
| `persistence.annotations`                          | Persistent Volume annotations                                                                | `{}`                                                    |
| `persistence.existingClaim`                        | Name of an existing PVC to use (avoids creating one if this is given)                        | `nil`                                                   |
| `livenessProbe.initialDelaySeconds`                | Delay before liveness probe is initiated                                                     | `30`                                                    |
| `livenessProbe.periodSeconds`                      | How often to perform the probe                                                               | `10`                                                    |
| `livenessProbe.timeoutSeconds`                     | When the probe times out                                                                     | `5`                                                     |
| `livenessProbe.successThreshold`                   | Minimum consecutive successes for the probe to be considered successful after having failed. | `1`                                                     |
| `livenessProbe.failureThreshold`                   | Minimum consecutive failures for the probe to be considered failed after having succeeded.   | `6`                                                     |
| `readinessProbe.initialDelaySeconds`               | Delay before readiness probe is initiated                                                    | `5`                                                     |
| `readinessProbe.periodSeconds`                     | How often to perform the probe                                                               | `10`                                                    |
| `readinessProbe.timeoutSeconds`                    | When the probe times out                                                                     | `5`                                                     |
| `readinessProbe.failureThreshold`                  | Minimum consecutive failures for the probe to be considered failed after having succeeded.   | `6`                                                     |
| `readinessProbe.successThreshold`                  | Minimum consecutive successes for the probe to be considered successful after having failed. | `1`                                                     |
| `configmap`                                        | MongoDB configuration file to be used                                                        | `nil`                                                   |
| `metrics.enabled`                                  | Start a side-car prometheus exporter                                                         | `false`                                                 |
| `metrics.image.registry`                           | MongoDB exporter image registry                                                              | `docker.io`                                             |
| `metrics.image.repository`                         | MongoDB exporter image name                                                                  | `forekshub/percona-mongodb-exporter`                    |
| `metrics.image.tag`                                | MongoDB exporter image tag                                                                   | `latest`                                                |
| `metrics.image.pullPolicy`                         | Image pull policy                                                                            | `IfNotPresent`                                          |
| `metrics.image.pullSecrets`                        | Specify docker-registry secret names as an array                                             | `[]` (does not add image pull secrets to deployed pods) |
| `metrics.podAnnotations`                           | Additional annotations for Metrics exporter pod                                              | {}                                                      |
| `metrics.extraArgs`               | String with extra arguments for the MongoDB Exporter                                                          | ``                                                      |
| `metrics.resources`                                | Exporter resource requests/limit                                                             | Memory: `256Mi`, CPU: `100m`                            |
| `metrics.serviceMonitor.enabled`                   | Create ServiceMonitor Resource for scraping metrics using PrometheusOperator                 | `false`                                                 |
| `metrics.serviceMonitor.additionalLabels`          | Used to pass Labels that are required by the Installed Prometheus Operator                   | {}                                                      |
| `metrics.serviceMonitor.relabellings`              | Specify Metric Relabellings to add to the scrape endpoint                                    | `nil`                                                   |
| `metrics.serviceMonitor.alerting.rules`            | Define individual alerting rules as required                                                 | {}                                                      |
| `metrics.serviceMonitor.alerting.additionalLabels` | Used to pass Labels that are required by the Installed Prometheus Operator                   | {}                                                      |


`helm install` の `--set key = value [、key = value]` 引数を使用して各パラメーターを指定します。 例えば、

```bash
$ helm install --name my-release \
  --set mongodbRootPassword=secretpassword,mongodbUsername=my-user,mongodbPassword=my-password,mongodbDatabase=my-database \
    stable/mongodb
```

上記のコマンドはMongoDBの `root` アカウントのパスワードを `secretpassword` に設定します。さらに、 `my-database` という名前のデータベースにアクセスできる、パスワード `my-password` を持つ `my-user` という名前の標準データベースユーザーを作成します。

あるいは、チャートのインストール中に、パラメーターの値を指定するYAMLファイルを提供することもできます。 例えば、

```bash
$ helm install --name my-release -f values.yaml stable/mongodb
```

> **Tip**: デフォルトの[values.yaml](values.yaml)を使用することもできます。 

## Replication

次のコマンドを使用して、MongoDBチャートをレプリカセットモードで起動できます。

```bash
$ helm install --name my-release stable/mongodb --set replicaSet.enabled=true
```

## Production settings and horizontal scaling

ファイル( [values-production.yaml](values-production.yaml) )は、本番環境用にスケーラブルで可用性の高いMongoDBデプロイメントをデプロイするための設定です。 実稼働構成をこのテンプレートに基づいて作成し、パラメーターを適切に調整することをお勧めします。


```console
$ curl -O https://raw.githubusercontent.com/kubernetes/charts/master/stable/mongodb/values-production.yaml
$ helm install --name my-release -f ./values-production.yaml stable/mongodb
```

このグラフを水平方向に拡大縮小するには、次のコマンドを実行してMongoDBレプリカセット内のセカンダリノードの数を拡大縮小します。

```console
$ kubectl scale statefulset my-release-mongodb-secondary --replicas=3
```

このチャートの特徴は次のとおりです。

- レプリケーションの各参加者には固定されたステートフルセットがあるため、プライマリ、セカンダリ、またはアービターノードを見つける場所が常にわかります。
- セカンダリノードとアービターノードの数は、独立してスケールアウトできます。
- レプリカセットを使用するためにスタンドアロンのMongoDBサーバーを使用することからアプリケーションを移動することは簡単です。

## 新しいインスタンスを初期化する

[Bitnami MongoDB](https://github.com/bitnami/bitnami-docker-mongodb) イメージを使用すると、カスタムスクリプトを使用して新しいインスタンスを初期化できます。 スクリプトを実行するには、スクリプトをチャートフォルダ `files/docker-entrypoint-initdb.d` の内側に配置して、ConfigMapとして使用できるようにする必要があります。

許可されている拡張子は `.sh`、および`.js`です。

## 持続性

[Bitnami MongoDB](https://github.com/bitnami/bitnami-docker-mongodb)イメージは、MongoDBデータおよび構成をコンテナーの `/bitnami/mongodb` パスに格納します。

チャートはこの場所に [Persistent Volume](http://kubernetes.io/docs/user-guide/persistent-volumes/) をマウントします。 ボリュームは動的ボリュームプロビジョニングを使用して作成されます。

## Upgrading

### To 5.0.0

レプリカセット設定を有効にした場合、チャートのステートフルセットで使用されているラベルを変更しない限り、下位互換性は保証されません。
以下の回避策を使用して、5.0.0より前のバージョンからアップグレードしてください。 次の例では、リリース名が `my-release`であると仮定しています。

```consoloe
$ kubectl delete statefulset my-release-mongodb-arbiter my-release-mongodb-primary my-release-mongodb-secondary --cascade=false
```
