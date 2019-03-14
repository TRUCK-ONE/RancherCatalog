# Custom Cloud Provider

別のクラウドプロバイダを有効にしたい場合は、RKEはカスタムクラウドプロバイダオプションを許可します。
名前を指定する必要があり、カスタムクラウドプロバイダオプションを `customCloudProvider` に複数行の文字列として渡すことができます。

たとえば、KubernetesでoVirtクラウドプロバイダーを使用するには、次のクラウドプロバイダー情報があります。

```
[connection]
uri = https://localhost:8443/ovirt-engine/api
username = admin@internal
password = admin
```

このクラウド設定ファイルをRKEに追加するには、 `cloud_provider` を設定する必要があります。

```
cloud_provider:
    name: ovirt
    # Note the pipe as this is what indicates a multiline string
    customCloudProvider: |-
      [connection]
      uri = https://localhost:8443/ovirt-engine/api
      username = admin@internal
      password = admin
```

