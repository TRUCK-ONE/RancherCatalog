## MySQL

参考URL(https://github.com/rancher/charts/blob/master/charts/mysql/v0.3.7/)

参考URLの内容をアレンジ  
* MySQL を 5.0系 から 8.0系に  
* 注釈等の日本語化  

## 前提条件

- Kubernetes 1.6+ with Beta APIs を有効に  
- PV provisioner support in the underlying infrastructure

## Chart の インストール

デフォルトでは、root用にランダムパスワードが、生成されます。  
独自のパスワードを設定したい場合は、 values.yaml の mysqlRootPassword を変更すること。

次のコマンドを実行して、ルートパスワードを取得できます。  
Make sure to replace [YOUR_RELEASE_NAME]:

    printf $(printf '\%o' `kubectl get secret [YOUR_RELEASE_NAME]-mysql -o jsonpath="{.data.mysql-root-password[*]}"`)

> **Tip**: 使用中リリース一覧 `helm list`

## Configuration

次の表に、MySQLチャートの設定可能なパラメータとそのデフォルト値を示します。  

| Parameter                            | Description                               | Default                                              |
| ------------------------------------ | ----------------------------------------- | ---------------------------------------------------- |
| `imageTag`                           | `mysql` image tag.                        | 最新のリリース                                 |
| `imagePullPolicy`                    | Image pull policy                         | `IfNotPresent`                                       |
| `mysqlRootPassword`                  | `root` user のパスワード             | `nil`                                                |
| `mysqlUser`                          | 新規作成するユーザー名           | `nil`                                                |
| `mysqlPassword`                      | 新規作成ユーザーのパスワード                | `nil`                                                |
| `mysqlDatabase`                      | 新規作成するデータベース名          | `nil`                                                |
| `livenessProbe.initialDelaySeconds`  | Delay before liveness probe is initiated  | 30                                                   |
| `livenessProbe.periodSeconds`        | How often to perform the probe            | 10                                                   |
| `livenessProbe.timeoutSeconds`       | When the probe times out                  | 5                                                    |
| `livenessProbe.successThreshold`     | Minimum consecutive successes for the probe to be considered successful after having failed. | 1 |
| `livenessProbe.failureThreshold`     | Minimum consecutive failures for the probe to be considered failed after having succeeded.   | 3 |
| `readinessProbe.initialDelaySeconds` | Delay before readiness probe is initiated | 5                                                    |
| `readinessProbe.periodSeconds`       | How often to perform the probe            | 10                                                   |
| `readinessProbe.timeoutSeconds`      | When the probe times out                  | 1                                                    |
| `readinessProbe.successThreshold`    | Minimum consecutive successes for the probe to be considered successful after having failed. | 1 |
| `readinessProbe.failureThreshold`    | Minimum consecutive failures for the probe to be considered failed after having succeeded.   | 3 |
| `persistence.enabled`                | データ保存のための volume を作成するか？             | true                                                 |
| `persistence.size`                   | 永続 volume のサイズ           | 8Gi RW                                               |
| `nodeSelector`                       | Node labels for pod assignment            | {}                                                   |
| `persistence.storageClass`           | 永続 volume のタイプ           | nil  (uses alpha storage class annotation)           |
| `persistence.accessMode`             | ReadWriteOnce or ReadOnly                 | ReadWriteOnce                                        |
| `persistence.existingClaim`          | 既存の永続 volume 名        | `nil`                                                |
| `persistence.subPath`                | マウントする volume の サブディレクトリ       | `nil`                                                |
| `resources`                          | CPU/Memory resource requests/limits       | Memory: `256Mi`, CPU: `100m`                         |
| `configurationFiles`                 | mysql 設定ファイルのリスト         | `nil`                                                |

上記のパラメータのいくつかは、 [MySQL DockerHub image](https://hub.docker.com/_/mysql/) で定義されている env変数 に対応しています。

`helm install`の` --set key = value [、key = value] `引数を使って各パラメータを指定してください。  
例えば、

```bash
$ helm install --name my-release \
  --set mysqlRootPassword=secretpassword,mysqlUser=my-user,mysqlPassword=my-password,mysqlDatabase=my-database \
    stable/mysql
```

上記のコマンドは、MySQL の `root` アカウントパスワードを `secretpassword` に設定します。また、パスワード `my-password`を持つ` my-user`というデータベースユーザを作成します。このユーザは `my-database`という名前のデータベースにアクセスします。

あるいは、チャートのインストール中に、パラメータの値を指定するYAMLファイルを提供することもできます。  
例えば、

```bash
$ helm install --name my-release -f values.yaml stable/mysql
```

> **Tip**: You can use the default [values.yaml](values.yaml)

## Persistence

[MySQL](https://hub.docker.com/_/mysql/) image  は、コンテナの `/var/lib/mysql` に MySQL の データ と 設定 を保存します。

デフォルトでは、PersistentVolumeClaimが作成され、そのディレクトリにマウントされます。 この機能を無効にするには、values.yamlを変更して永続性を無効にし、代わりにemptyDirを使用します。

> *"An emptyDir volume is first created when a Pod is assigned to a Node, and exists as long as that Pod is running on that node. When a Pod is removed from a node for any reason, the data in the emptyDir is deleted forever."*

## MySQL 設定ファイル

[MySQL](https://hub.docker.com/_/mysql/) image は、`/etc/mysql/conf.d` で 設定ファイルを受け入れます。カスタマイズされたMySQL設定を使用したい場合は、`configurationFiles` 属性にファイルの内容を渡すことで代替設定ファイルを作成できます。MySQLのドキュメントによれば、`.cnf` で終わるファイルだけがロードされます。

```yaml
configurationFiles:
  mysql.cnf: |-
    [mysqld]
    skip-host-cache
    skip-name-resolve
    sql-mode=STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
  mysql_custom.cnf: |-
    [mysqld]
```
