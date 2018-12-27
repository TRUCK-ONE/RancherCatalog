# MySQL

## [更新履歴](history.md)

- ver0.1.0
- ver0.2.0

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

| Parameter                            | 説明                                      | 既定値                                                     |
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

## MySQL 初期化ファイル

[MySQL](https://hub.docker.com/_/mysql/) image は、`/docker-entrypoint-initdb.d` で、 *.sh, *.sql 及び *.sql.gz ファイルを受け入れます。これらのファイルはコンテナの初期化のために一度だけ実行され、その後のコンテナの再起動では無視されます。  
初期化スクリプトを使用する場合は、ファイルの内容を`initializationFiles` 属性に渡すことによって初期化ファイルを作成できます。

```yaml
initializationFiles:
  first-db.sql: |-
    CREATE DATABASE IF NOT EXISTS first DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
  second-db.sql: |-
    CREATE DATABASE IF NOT EXISTS second DEFAULT CHARACTER SET utf8 DEFAULT COLLATE utf8_general_ci;
```

## SSL

This chart supports configuring MySQL to use [encrypted connections](https://dev.mysql.com/doc/refman/5.7/en/encrypted-connections.html) with TLS/SSL certificates provided by the user. This is accomplished by storing the required Certificate Authority file, the server public key certificate, and the server private key as a Kubernetes secret. The SSL options for this chart support the following use cases:

* Manage certificate secrets with helm
* Manage certificate secrets outside of helm

## Manage certificate secrets with helm

Include your certificate data in the `ssl.certificates` section. For example:

```
ssl:
  enabled: false
  secret: mysql-ssl-certs
  certificates:
  - name: mysql-ssl-certs
    ca: |-
      -----BEGIN CERTIFICATE-----
      ...
      -----END CERTIFICATE-----
    cert: |-
      -----BEGIN CERTIFICATE-----
      ...
      -----END CERTIFICATE-----
    key: |-
      -----BEGIN RSA PRIVATE KEY-----
      ...
      -----END RSA PRIVATE KEY-----
```

> **Note**: Make sure your certificate data has the correct formatting in the values file.

## Manage certificate secrets outside of helm

1. Ensure the certificate secret exist before installation of this chart.
2. Set the name of the certificate secret in `ssl.secret`.
3. Make sure there are no entries underneath `ssl.certificates`.

To manually create the certificate secret from local files you can execute:
```
kubectl create secret generic mysql-ssl-certs \
  --from-file=ca.pem=./ssl/certificate-authority.pem \
  --from-file=server-cert.pem=./ssl/server-public-key.pem \
  --from-file=server-key.pem=./ssl/server-private-key.pem
```
> **Note**: `ca.pem`, `server-cert.pem`, and `server-key.pem` **must** be used as the key names in this generic secret.

If you are using a certificate your configurationFiles must include the three ssl lines under [mysqld]

```
[mysqld]
    ssl-ca=/ssl/ca.pem
    ssl-cert=/ssl/server-cert.pem
    ssl-key=/ssl/server-key.pem
```