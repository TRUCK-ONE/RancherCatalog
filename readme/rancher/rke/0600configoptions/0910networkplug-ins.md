# Network Plug-ins

RKEは、アドオンとして展開されている次のネットワークプラグインを提供します。

- Flannel
- Calico
- Canal
- Weave

デフォルトでは、ネットワークプラグインは `canal` です。
他のネットワークプラグインを使用したい場合は、  `cluster.yml` でクラスタレベルで有効にするネットワークプラグインを指定する必要があります。

```
# Setting the flannel network plug-in
network:
    plugin: flannel
```

ネットワークプラグインに使用されるイメージは `system_images` [ディレクティブ](https://rancher.com/docs/rke/v0.1.x/en/config-options/system-images/)の下にあります。
各Kubernetesバージョンには、各ネットワークプラグインに関連付けられているデフォルトのイメージがありますが、`system_images` のimageタグを変更することでこれらをオーバーライドできます。

## Network Plug-in Options

ネットワークプラグインを展開するために使用できるさまざまなイメージに加えて、特定のネットワークプラグインはネットワークプラグインをカスタマイズするために使用できる追加のオプションをサポートします。

### Canal Network Plug-in Options

```
network:
    plugin: canal
    options:
        canal_iface: eth1
        canal_flannel_backend_type: vxlan
```

#### Canal Interface

`canal_iface` を設定することで、ホスト間通信に使用するインタフェースを設定できます。
`canal_flannel_backend_type` オプションを使用すると、使用する[フランネルバックエンド](https://github.com/coreos/flannel/blob/master/Documentation/backends.md)の種類を指定できます。
デフォルトでは `vxlan` バックエンドが使われます。

### Flannel Network Plug-in Options

```
network:
    plugin: flannel
    options:
      flannel_iface: eth1
      flannel_backend_type: vxlan
```

#### Flannel Interface

`flannel_iface` を設定することで、ホスト間通信に使用するインタフェースを設定できます。
`flannel_backend_type` オプションを使用すると、使用する[フランネルバックエンド](https://github.com/coreos/flannel/blob/master/Documentation/backends.md)のタイプを指定できます。
デフォルトでは `vxlan` バックエンドが使われます。

### Calico Network Plug-in Options

```
network:
    plugin: calico
    calico_cloud_provider: aws
```

#### Cloud Provider

Calicoは現在、`calico_cloud_provider` を使用して設定できる2つのクラウドプロバイダー、AWSまたはGCEのみをサポートしています。

**有効なオプション**

- `aws`
- `gce`

