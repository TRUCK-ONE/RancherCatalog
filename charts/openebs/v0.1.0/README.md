# OpenEBS

[OpenEBS](https://github.com/openebs/openebs) は、DevOpsおよびコンテナ環境に永続的でコンテナ化されたブロックストレージを提供するオープンソースのストレージプラットフォームです。

OpenEBSは、クラウド、オンプレミス、または開発者用ラップトップ（ミニキューブ）のいずれかのKubernetesクラスタにデプロイできます。
OpenEBS自体は、クラスタ上の単なる別のコンテナとしてデプロイされ、ポッド、アプリケーション、クラスタ、またはコンテナ単位のレベルで指定できるストレージサービスを可能にします。

## Introduction(前書き)

このチャートは、 [Helm](https://helm.sh) パッケージマネージャを使用して [Kubernetes](http://kubernetes.io) クラスタへのOpenEBSの導入を促進します。

### Prerequisites(前提条件)
- RBACを有効にしたKubernetes 1.7.5以降
- 基盤となるインフラストラクチャでのiSCSI PVサポート

### Installing OpenEBS
```
helm install --namespace openebs stable/openebs
```

### Installing OpenEBS with the release name `my-release`:
```
helm install --name `my-release` --namespace openebs stable/openebs
```

### To uninstall/delete the `my-release` deployment:
```
helm ls --all
helm delete `my-release`
```
## Configuration(設定)

次の表は、OpenEBSチャートの設定可能なパラメータとそのデフォルト値の一覧です。

| Parameter                               | Description                                   | Default                                   |
| ----------------------------------------| --------------------------------------------- | ----------------------------------------- |
| `rbac.create`                           | Enable RBAC Resources                         | `true`                                    |
| `image.pullPolicy`                      | Container pull policy                         | `IfNotPresent`                            |
| `apiserver.image`                       | Image for API Server                          | `quay.io/openebs/m-apiserver`             |
| `apiserver.imageTag`                    | Image Tag for API Server                      | `0.8.0`                                   |
| `apiserver.replicas`                    | Number of API Server Replicas                 | `1`                                       |
| `provisioner.image`                     | Image for Provisioner                         | `quay.io/openebs/openebs-k8s-provisioner` |
| `provisioner.imageTag`                  | Image Tag for Provisioner                     | `0.8.0`                                   |
| `provisioner.replicas`                  | Number of Provisioner Replicas                | `1`                                       |
| `snapshotOperator.provisioner.image`    | Image for Snapshot Provisioner                | `quay.io/openebs/snapshot-provisioner`    |
| `snapshotOperator.provisioner.imageTag` | Image Tag for Snapshot Provisioner            | `0.8.0`                                   |
| `snapshotOperator.controller.image`     | Image for Snapshot Controller                 | `quay.io/openebs/snapshot-controller`     |
| `snapshotOperator.controller.imageTag`  | Image Tag for Snapshot Controller             | `0.8.0`                                   |
| `snapshotOperator.replicas`             | Number of Snapshot Operator Replicas          | `1`                                       |
| `ndm.image`                             | Image for Node Disk Manager                   | `quay.io/openebs/openebs/node-disk-manager-amd64` |
| `ndm.imageTag`                          | Image Tag for Node Disk Manager               | `v0.2.0`                                  |
| `ndm.sparse.enabled`                    | Create Sparse files and cStor Sparse Pool     | `true`                                    |
| `ndm.sparse.path`                       | Directory where Sparse files are created      | `/var/openebs/sparse`                     |
| `ndm.sparse.size`                       | Size of the sparse file in bytes              | `10737418240`                             |
| `ndm.sparse.count`                      | Number of sparse files to be created          | `1`                                       |
| `ndm.sparse.filters.excludeVendors`     | Exclude devices with specified vendor         | `CLOUDBYT,OpenEBS`                        |
| `ndm.sparse.filters.excludePaths`       | Exclude devices with specified path patterns  | `loop,fd0,sr0,/dev/ram,/dev/dm-,/dev/md`  |
| `jiva.image`                            | Image for Jiva                                | `quay.io/openebs/jiva`                    |
| `jiva.imageTag`                         | Image Tag for Jiva                            | `0.8.0`                                   |
| `jiva.replicas`                         | Number of Jiva Replicas                       | `3`                                       |
| `cstor.pool.image`                      | Image for cStor Pool                          | `quay.io/openebs/cstor-pool`              |
| `cstor.pool.imageTag`                   | Image Tag for cStor Pool                      | `0.8.0`                                   |
| `cstor.poolMgmt.image`                  | Image for cStor Pool  Management              | `quay.io/openebs/cstor-pool-mgmt`         |
| `cstor.poolMgmt.imageTag`               | Image Tag for cStor Pool Management           | `0.8.0`                                   |
| `cstor.target.image`                    | Image for cStor Target                        | `quay.io/openebs/cstor-istgt`             |
| `cstor.target.imageTag`                 | Image Tag for cStor Target                    | `0.8.0`                                   |
| `cstor.volumeMgmt.image`                | Image for cStor Volume  Management            | `quay.io/openebs/cstor-volume-mgmt`       |
| `cstor.volumeMgmt.imageTag`             | Image Tag for cStor Volume Management         | `0.8.0`                                   |
| `policies.monitoring.image`             | Image for Prometheus Exporter                 | `quay.io/openebs/m-exporter`              |
| `policies.monitoring.imageTag`          | Image Tag for Prometheus Exporter             | `0.8.0`                                   |
| `analytics.enabled`                     | Enable sending stats to Google Analytics      | `true`                                    |
| `analytics.pingInterval`                | Duration(hours) between sending ping stat     | `24h`                                      |


`helm install` の `--set key=value[,key=value]` 引数を使用して各パラメーターを指定します。

あるいは、チャートのインストール中に、パラメーターの値を指定するYAMLファイルを提供することもできます。 例えば、

```shell
helm install --name `my-release` -f values.yaml stable/openebs
```

> **ヒント**: デフォルト [values.yaml](values.yaml) を使用することができます。
