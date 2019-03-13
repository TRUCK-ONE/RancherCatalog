# Troubleshooting

## よくある問題

### ボリュームはUIからアタッチ/デタッチできますが、Kubernetes Pod/StatefulSet などは使用できません

#### Flexvolumeプラグインとの併用

ボリュームプラグインディレクトリが正しく設定されているか確認してください。
ユーザーが明示的に設定しない限り、これは自動的に検出されます。

[公式文書](https://github.com/kubernetes/community/blob/master/contributors/devel/sig-storage/flexvolume.md)で述べられているように、デフォルトで、Kubernetes は /usr/libexec/kubernetes/kubelet-plugins/volume/exec/ を使用します。

さまざまな理由でディレクトリを変更することを選択するベンダーもあります。 たとえば、GKEは代わりに /home/kubernetes/flexvolume を使用します。

ユーザーは、ホスト上でps aux | grep kubeletを実行して--volume-plugin-dirパラメーターをチェックすることによって正しいディレクトリーを見つけることができます。
何もなければ、デフォルトの /usr/libexec/kubernetes/kubelet-plugins/volume/exec/ が使用されます。

## Troubleshooting guide

Longhornにはいくつかのコンポーネントがあります。 マネージャ、エンジン、ドライバ、UI これらのコンポーネントはすべて、デフォルトでKubernetesクラスタ内のlonghorn-system名前空間でポッドとして実行されています。

### UI

Longhorn UIを使用することはトラブルシューティングのための良いスタートです。
たとえば、Kubernetesが1つのボリュームを正しくマウントできない場合は、ワークロードを停止した後、そのボリュームを1つのノードに手動で接続してマウントし、内容にアクセスしてボリュームが損なわれていないか確認します。

また、UIダッシュボードのイベントログには、おそらく問題に関する情報がいくつかあります。
警告レベルでイベントログを確認します。

### Manager and engines

トラブルシューティングを支援するためにLonghornマネージャおよびエンジンからログを得ることができます。
最も有用なログは、longhorn-manager-xxx、およびLonghorn Engine内部のログです。
`<volname> -e-xxxx` および `<volname> -r-xxxx`

通常は複数のLonghorn Managerが同時に実行されているため、kubetailを使用することをお勧めします。
これは、複数のpodのログを追跡するための優れたツールです。
あなたが使用することができます：

```
kubetail longhorn-manager -n longhorn-system
```

マネージャログを追跡するためにリアルタイムで。

### CSI driver

CSIドライバの場合は、longhorn-csi-plugin-xxxのコンテナだけでなく、csi-attacher-0およびcsi-provisioner-0のログも確認してください。

### Flexvolume driver

Flexvolumeドライバの場合は、まず、ドライバがノードのどこにインストールされているかを確認します。 
その情報については、longhorn-driver-deployer-xxxxのログを確認してください。

その後、kubeletのログを確認してください。
Flexvolumeドライバ自体はコンテナ内では動作しません。
それはkubeletプロセスと共に走ります。

kubeletがノード上でネイティブに実行されている場合は、次のコマンドを使用してログを取得できます。

```
journalctl -u kubelet
```

または、kubeletがコンテナとして実行されている場合（たとえばRKE）、代わりに次のコマンドを使用してください。

```
docker logs kubelet
```

Longhorn Flexvolumeのさらに詳細なログを表示するには、ノード上またはコンテナ内で次のコマンドを実行します（kubeletがコンテナとして、たとえばRKEで実行されている場合）。

```
touch /var/log/longhorn_driver.log
```
