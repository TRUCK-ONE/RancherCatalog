# Image Preloading

起動時に、RancherOSは `/var/lib/rancher/preload/docker` および `/var/lib/rancher/preload/system-docker` ディレクトリをスキャンし、そこに見つかったコンテナイメージアーカイブを`docker load` および `system-docker load` でロードしようとします。

アーカイブは `.tar` ファイルで、オプションで `xz` または `gzip` で圧縮されています。
これらは `docker save` コマンドで作成できます。

```
$ docker save my-image1 my-image2 some-other/image3 | xz > my-images.tar.xz
```

結果のファイルは、`/var/lib/rancher/preload/docker` または `/var/lib/rancher/preload/system-docker` に配置する必要があります（DockerとSystem Dockerのどちらにプリロードするかによって異なります）。

プリロードプロセスでは、新しいアーカイブを1回だけ読み込むので、その後の起動に時間がかかりません（`<archive>.done` ファイルは、読み込まれたアーカイブをマークするために作成されます）。
アーカイブを更新すると（同じ名前の新しいアーカイブを配置すると）、次回の起動時にも読み込まれます。

事前ロードプロセスはデフォルトでは`非同期`です。
オプションで、これはcloud-configファイルまたは`ros config set` コマンドを使用して`同期`に設定できます。
次の例では、cloud-configファイルと `ros config set` コマンドを使用して、RancherOSのプリロードプロセスを`同期`に設定します。

v1.4から利用可能

cloud-configファイル、例：

```
#cloud-config
rancher:
  preload_wait: true
```

ros config setコマンド、例：

```
$ ros config set rancher.preload_wait true
```

RancherOSディストリビューションをカスタマイズする場合（インフラストラクチャ用にクラウドVMイメージを構築する場合）は、dockerイメージを事前に梱包すると便利です。

