# Flexvolume

参考URL： [Flexvolume](https://github.com/kubernetes/community/blob/master/contributors/devel/sig-storage/flexvolume.md)

Flexvolumeを使用すると、ユーザーは独自のドライバを作成したり、Kubernetesでボリュームのサポートを追加したりできます。
ドライバがアタッチ機能を必要とする場合（--enable-controller-attach-detach Kubeletオプションがfalseに設定されていない限り）、ベンダードライバはすべてのノードのボリュームプラグインパスとマスターにインストールする必要があります。 従来の動作モード）。

FlexvolumeはKubernetes 1.8リリース以降のGA機能です。

## 前提条件

プラグインのパスにあるすべてのノード（「--enable-controller-attach-detach」Kubeletオプションが有効な場合はマスターノードにも）にベンダードライバーをインストールします。
プラグインをインストールするためのパス：`<plugindir>/<vendor〜driver>/<driver>` デフォルトのプラグインディレクトリは `/usr/libexec/kubernetes/kubelet-plugins/volume/exec/` です。
kubeletでは--volume-plugin-dirフラグを介して、コントローラマネージャでは--flex-volume-plugin-dirフラグを介して変更できます。

たとえば、cifsドライバを追加するには、ベンダによってfooドライバが次の場所にインストールされます：`/usr/libexec/kubernetes/kubelet-plugins/volume/exec/foo〜cifs/cifs`

ベンダー名とドライバ名は、ボリューム仕様のflexVolume.driverと一致し、「〜」が「/」に置き換えられている必要があります。
たとえば、flexVolume.driver が `foo/cifs` に設定されている場合、ベンダーはfoo、ドライバはcifsです。

## Dynamic Plugin Discovery

v1.8から、Flexvolumeはドライバをその場で検出する機能をサポートします。
システムの初期化時にドライバを存在させたり、kubeletやコントローラマネージャを再起動したりする代わりに、システムの実行中にドライバをインストール、アップグレード/ダウングレード、およびアンインストールすることができます。
詳細については、[設計書](Flexvolume01.md)を参照してください。

## 自動プラグインのインストール/アップグレード

Flexvolumeドライバをインストールおよびアップグレードする方法の1つは、DaemonSetを使用することです。
詳細については、推奨されるドライバの展開方法を参照してください。
また、例についてはこちらを参照してください。

## プラグイン詳細

このプラグインは、バックエンドドライバ用に次のコールアウトが実装されていることを期待しています。
いくつかの呼び出しはオプションです。
コールアウトはKubeletとController Managerから呼び出されます。

### Driver invocation model:

#### Init：
ドライバを初期化します。
Kubelet＆Controllerマネージャの初期化中に呼び出されます。
成功すると、この関数は各Flexvolume機能がドライバによってサポートされているかどうかを示す機能マップを返します。 
現在の機能：

---
以下略

---
