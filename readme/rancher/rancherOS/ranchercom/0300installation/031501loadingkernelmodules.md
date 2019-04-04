# Loading Kernel Modules

RancherOS v0.8以降、私たちは未修正のkernel.org LTSカーネルを使用して独自のカーネルを構築しています。
カーネルモジュールへのパラメータのロードと追加のカーネルモジュールのロードの両方を提供します。

## Loading Kernel Modules with parameters
v1.4から利用可能

`rancher.modules` は、カーネルモジュールやモジュールパラメータを設定するのに役立ちます。

例として、カーネルモジュール`ndb`のパラメータを設定します。

```
sudo ros config set rancher.modules "['nbd nbds_max=1024', 'nfs']"
```
または
```
#cloud-config
rancher:
  modules: [nbd nbds_max=1024, nfs]
```

再起動後、`ndbs_max` パラメータが更新されたことを確認できます。

```
# cat /sys/module/nbd/parameters/nbds_max
1024
```

## Loading Extra Kernel Modules

また、ほとんどすべてのオプションの追加機能をモジュールとして構築します。
そのため、ほとんどのツリー内モジュールは `kernel-extras` サービスで利用できます。

RancherOS用のカーネルモジュールを構築する必要がある場合は、4つの選択肢があります。

- `kernel-extras` サービスを試してください。
- 次のリリースに追加するよう依頼してください
- ツリーから外れている場合は、zfsおよびopen-iscsiサービスに使用されているメソッドをコピーしてください。
- 自分で作ってください。

### Try the kernel-extras service

私たちは、オプションのRancherOSサービスにパッケージされた、カーネルモジュールとしてのオプションのドライバーのほとんどを使ってRancherOSカーネルを構築します。

これらをインストールするには、次のコマンドを実行してください。

```
sudo ros service enable kernel-extras
sudo ros service up kernel-extras
```

モジュールは `modprobe` にあなたのために今利用できるはずです

### Ask us to do it

[https://github.com/rancher/os](https://github.com/rancher/os) レポジトリでGitHubの問題を開きます - 次回カーネルを構築するときには、おそらくそれをkernel-extraに追加します。
初期設定時または起動時にモジュールが必要かどうかを教えてください。
デフォルトのカーネルモジュールに追加することもできます。

### Copy the out of tree build method

[https://github.com/rancher/os-services/blob/master/z/zfs.yml](https://github.com/rancher/os-services/blob/master/z/zfs.yml) および [https://github.com/rancher/os-services/tree/master/images/20-zfs](https://github.com/rancher/os-services/tree/master/images/20-zfs) を参照してください。

build containerとbuild.shスクリプトはソースを構築してから、ツールイメージを作成します。
これは、`docker run` を使用してこれらのツールをコンソールコンテナに"wonka.sh"インポートするために使用されます。

### Build your own

例として、私は `intel-ishtp hid` ドライバを作りましょう。
それらはそのカーネルのための適切なツールバージョンを含むべきであるので、作り出すために `rancher/os-zfs:<version>` イメージを使います。

```
sudo docker run --rm -it --entrypoint bash --privileged -v /lib:/host/lib -v $(pwd):/data -w /data rancher/os-zfs:$(ros -v | cut -d ' ' -f 2)

apt-get update
apt-get install -qy libncurses5-dev bc libssh-dev
curl -SsL -o src.tgz https://github.com/rancher/os-kernel/releases/download/v$(uname -r)/linux-$(uname -r)-src.tgz
tar zxvf src.tgz
zcat /proc/config.gz >.config
# Yes, ignore the name of the directory :/
cd v*
# enable whatever modules you want to add.
make menuconfig
# I finally found an Intel sound hub that wasn't enabled yet
# CONFIG_INTEL_ISH_HID=m
make modules SUBDIRS=drivers/hid/intel-ish-hid

# test it
insmod drivers/hid/intel-ish-hid/intel-ishtp.ko
rmmod intel-ishtp

# install it
ln -s /host/lib/modules/ /lib/
cp drivers/hid/intel-ish-hid/*.ko /host/lib/modules/$(uname -r)/kernel/drivers/hid/
depmod

# done
exit
```

それからあなたのコンソールで、あなたは走ることができるはずです

```
modprobe intel-ishtp
```


