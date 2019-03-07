# Longhorn Manager

Longhornのマネージャー

## Requirement(要件)

1. 既存のKubernetesクラスタ1.8以降
2. iscsiadm / open-iscsiがホストにインストールされていることを確認します。
3. Longhorn Flexvolume Driver用に、`jq`、`findmnt`、`curl` がホストにインストールされていることを確認します。

## Build

make

## Deployment

```
kubectl create -Rf deploy/install
```

`Longhorn-system` 名前空間に次のコンポーネントを配置します。

1. Longhorn Manager
2. Longhorn Flexvolume Driver for Kubernetes
3. Longhorn UI

## Cleanup

Longhorn CRDにはファイナライザがあります。
そのため、ユーザーは最初にボリュームと関連リソースを削除し、マネージャにそれらをクリーンアップする機会を与えます。

Kubernetesクラスターへの損傷を防ぐために、Longhornボリューム（PersistentVolume、PersistentVolumeClaim、StorageClass、Deployment、StatefulSet、DaemonSetなど）を使用してすべてのKubernetesワークロードを削除することをお勧めします。

1. アンインストールジョブを作成してシステムからCRDをクリーンにパージし、成功を待ちます。

```
kubectl create -f deploy/uninstall/uninstall.yaml
kubectl -n longhorn-system get job/longhorn-uninstall -w
```

出力例：

```
$ kubectl create -f deploy/uninstall/uninstall.yaml
job.batch/longhorn-uninstall created
$ kubectl -n longhorn-system get job/longhorn-uninstall -w
NAME                 DESIRED   SUCCESSFUL   AGE
longhorn-uninstall   1         0            3s
longhorn-uninstall   1         1            45s
^C
```

2. 残りの部品を取り外します。

```
kubectl delete -Rf deploy/install
```

## Integration test(統合テスト)

longhorn-testsレポを見る。

## Notes:

### Google Kubernetes Engine

デプロイメントを作成する前に、自分でcluster-adminロールバインディングを作成する必要があります。詳細については、[こちら](https://cloud.google.com/kubernetes-engine/docs/how-to/role-based-access-control)を参照してください。

```
kubectl create clusterrolebinding cluster-admin-binding --clusterrole=cluster-admin --user=<name@example.com>
```

### Flexvolume Plugin Directory

デフォルトでは、[Flexvolumeプラグイン](https://github.com/kubernetes/community/blob/master/contributors/devel/sig-storage/flexvolume.md)ディレクトリ（ /usr/libexec/kubernetes/kubelet-plugins/volume/exec/ ）を使用しています。

GKE 1.8以降の場合は、`/home/kubernetes/flexvolume` にあります。

ご自分の環境に応じて、deploy/deploy.yamlボリュームの flexvolume-longhorn マウント位置を変更する必要があるかもしれません。

## License

Copyright (c) 2014-2018 [Rancher Labs, Inc.](https://rancher.com/)

Apache License、Version 2.0（以下「ライセンス」）に基づきライセンスされています。 ライセンスに準拠している場合を除き、このファイルを使用することはできません。 あなたは、ライセンスのコピーを以下から入手することができます。

[http://www.apache.org/licenses/LICENSE-2.0](http://www.apache.org/licenses/LICENSE-2.0)

適用法で義務付けられている場合、または書面で合意されている場合を除き、ライセンスに基づいて頒布されるソフトウェアは、明示的または黙示的を問わず、「現状のまま」ベースで保証されます。
ライセンスに基づく許可および制限を規定する特定の言語については、ライセンスを参照してください。

