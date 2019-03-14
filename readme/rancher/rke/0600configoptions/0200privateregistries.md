# Private Registries

RKEは、複数のプライベートDockerレジストリを構成する機能をサポートしています。
レジストリと認証情報を渡すことで、ノードはこれらのプライベートレジストリからイメージを取得することができます。

```
private_registries:
    - url: registry.com
      user: Username
      password: password
    - url: myregistry.com
      user: myuser
      password: mypassword
```

> **Note：**  
> Docker Hubレジストリを使用している場合は、`URL` を省略するか、または `docker.io` に設定することができます。

## Default Registry

v0.1.10以降、RKEはすべての[システムイメージ](https://rancher.com/docs/rke/v0.1.x/en/config-options/system-images/)で使用されるプライベートレジストリのリストからデフォルトレジストリを指定することをサポートしています。
この例では、.RKEはすべてのシステムイメージのデフォルトレジストリとして `registry.com` を使用します。
`rancher/rke-tools:v0.1.14` は `registry.com/rancher/rke-tools:v0.1.14` になります。

```
private_registries:
    - url: registry.com
      user: Username
      password: password
      is_default: true # All system images will be pulled using this registry. 
```

## Air-gapped Setups

デフォルトでは、すべてのシステムイメージはDockerHubから取得されています。
DockerHubにアクセスできないシステムを使用している場合は、必要なすべての[システムイメージ](https://rancher.com/docs/rke/v0.1.x/en/config-options/system-images/)が格納されているプライベートレジストリを作成する必要があります。

v0.1.10以降、プライベートレジストリの認証情報を設定する必要がありますが、このレジストリをデフォルトのレジストリとして指定して、すべての[システムイメージ](https://rancher.com/docs/rke/v0.1.x/en/config-options/system-images/)が指定されたプライベートレジストリから取得されるようにすることができます。
`rke config --system-images` コマンドを使用して、デフォルトのシステムイメージのリストを取得し、プライベートレジストリに追加することができます。

v0.1.10より前のバージョンでは、プライベートレジストリのクレデンシャルを設定して、各イメージ名の前にプライベートレジストリURLが追加されるように `cluster.yml` 内のすべての[システムイメージ](https://rancher.com/docs/rke/v0.1.x/en/config-options/system-images/)の名前を更新する必要がありました。

