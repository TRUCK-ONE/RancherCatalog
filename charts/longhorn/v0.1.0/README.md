# Rancher Longhorn Chart

次の文書は、Rancher 2.0チャートからLonghornを実行することに関するものです。

## Source Code

Longhornは100％オープンソースソフトウェアです。 プロジェクトのソースコードは多数のリポジトリに分散しています。

1. Longhorn Engine -- Core controller/replica logic https://github.com/rancher/longhorn-engine
2. Longhorn Manager -- Longhorn orchestration, includes Flexvolume driver for Kubernetes https://github.com/rancher/longhorn-manager
3. Longhorn UI -- Dashboard https://github.com/rancher/longhorn-ui

## 前提条件(Prerequisites)

1. Rancher v2.1+
2. Docker v1.13+
3. Kubernetes v1.8+ cluster で 1つ以上のノードとマウント伝搬機能を有効にしたもの。 ご使用のKubernetes クラスタが Rancher v2.0.7 以降でプロビジョニングされている場合、MountPropagation 機能はデフォルトで有効になっています。 [あなたのKubernetes環境をチェックしてください](https://github.com/rancher/longhorn#environment-check-script)。 MountPropagationが無効になっている場合は、デフォルトのCSIドライバの代わりにKubernetes Flexvolumeドライバがデプロイされます。 MountPropagationが無効になっている場合、ベースイメージ機能も無効になります。
4. `curl`・`findmnt`・`grep`・`awk`・`blkid` が、Kubernetesクラスタのすべてのノードにインストールされていることを確認してください。
5. `open-iscsi`がKubernetesクラスタの全てのノードにインストールされていることを確認してください。 GKEに関しては、ゲストOSイメージとしてUbuntuが推奨されています。なぜならそれはすでに `open-iscsi`を含んでいるからです。

## アンインストール

1. Kubernetesクラスターへの損傷を防ぐために、Longhornボリューム（PersistentVolume、PersistentVolumeClaim、StorageClass、Deployment、StatefulSet、DaemonSetなど）を使用してすべてのKubernetesワークロードを削除することをお勧めします。

2. Rancher UIから、`Catalog Apps`タブに移動してLonghorn アプリを削除します。

## トラブルシューティング

### アンインストール手順に従ってではなく、Rancher UIからLonghorn Appを削除しました。

Longhorn App（同じバージョン）を再デプロイします。 上記のアンインストール手順に従ってください。

### CRDs に関する問題

何らかの理由でCRDインスタンスまたはCRDsを削除できない場合は、以下のコマンドを実行してクリーンアップしてください。 注意：これはLonghornの状態をすべて抹消します！

```
# Delete CRD finalizers, instances and definitions
for crd in $(kubectl get crd -o jsonpath={.items[*].metadata.name} | tr ' ' '\n' | grep longhorn.rancher.io); do
  kubectl -n ${NAMESPACE} get $crd -o yaml | sed "s/\- longhorn.rancher.io//g" | kubectl apply -f -
  kubectl -n ${NAMESPACE} delete $crd --all
  kubectl delete crd/$crd
done
```

### ボリュームはUIからアタッチ/デタッチできますが、Kubernetes Pod/StatefulSet などは使用できません。

ボリュームプラグインディレクトリが正しく設定されているか確認してください。 ユーザーが明示的に設定しない限り、これは自動的に検出されます。

デフォルトで Kubernetes を使います `/usr/libexec/kubernetes/kubelet-plugins/volume/exec/`, [公式文書](https://github.com/kubernetes/community/blob/master/contributors/devel/flexvolume.md#prerequisites)に記載されているように。

さまざまな理由でディレクトリを変更することを選択するベンダーもあります。 たとえば、GKEは代わりに `/ home/kubernetes/flexvolume` を使用します。

ユーザーは、ホスト上で `ps aux | grep kubelet` を実行してパラメーター `--volume-plugin-dir` をチェックすることによって正しいディレクトリーを見つけることができます。 何もなければ、デフォルトの `/usr/libexec/kubernetes/kubelet-plugins/volume/exec/` が使用されます。

---
詳しくは[リンク](https://github.com/rancher/longhorn)をご覧ください。
