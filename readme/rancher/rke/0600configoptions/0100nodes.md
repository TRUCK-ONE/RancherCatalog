# Nodes

`nodes` ディレクティブは `cluster.yml` ファイルの唯一の必須セクションです。
これは、クラスタノード、ノードへのアクセスに使用されるssh認証情報、およびこれらのノードがKubernetesクラスタ内でどの役割を果たすかを指定するためにRKEによって使用されます。

```
nodes:
    nodes:
    - address: 1.1.1.1
      user: ubuntu
      role:
      - controlplane
      - etcd
      ssh_key_path: /home/user/.ssh/id_rsa
      port: 2222
    - address: 2.2.2.2
      user: ubuntu
      role:
      - worker
      ssh_key: |-
        -----BEGIN RSA PRIVATE KEY-----

        -----END RSA PRIVATE KEY-----
    - address: example.com
      user: ubuntu
      role:
      - worker
      hostname_override: node3
      internal_address: 192.168.1.6
      labels:
        app: ingress
```

## Node Options

各ノード内には、使用できるディレクティブが複数あります。

### Address

`address` ディレクティブは、ノードのホスト名またはIPアドレスを設定するために使用されます。
RKEはこのアドレスに接続できなければなりません。

### Internal Address(内部アドレス)

`internal_address` を使用すると、複数のアドレスを持つノードに、プライベートネットワーク上のホスト間通信に使用する特定のアドレスを設定させることができます。
`internal_address` が設定されていない場合、 `address` はホスト間通信に使用されます。

### Overriding the Hostname

`hostname_override` は、ノードをKubernetesに登録するときにRKEが使用するわかりやすい名前を提供できるようにするために使用されます。
このホスト名はルーティング可能なアドレスである必要はありませんが、有効な [Kubernetesリソース名](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#names)である必要があります。
`hostname_override` が設定されていない場合は、Kubernetesにノードを登録するときに `address` ディレクティブが使用されます。

> **Note：**  
> [クラウドプロバイダ](https://rancher.com/docs/rke/v0.1.x/en/config-options/cloud-providers/)が設定されている場合、クラウドプロバイダを正しく使用するためにホスト名を上書きする必要があるかもしれません。
> `hostname_override` フィールドが明示的に無視される[AWSクラウドプロバイダー](https://kubernetes.io/docs/concepts/cluster-administration/cloud-providers/#aws)には例外があります。

### SSH Portlink

各ノードで、このノードに接続するときに使用するポートを指定します。
デフォルトのポートは `22` です。

### SSH Users

ノードごとに、このノードに接続するときに使用される `user` を指定します。
このユーザーはDockerグループのメンバーであるか、ノードのDockerソケットへの書き込みを許可されている必要があります。

### SSH Key Path

ノードごとに、このノードに接続するときに使用するSSH秘密鍵のパス、つまり `ssh_key_path` を指定します。
各ノードのデフォルトのキーパスは `~/.ssh/id_rsa` です。

> **Note：**  
> すべてのノードで使用できる秘密鍵がある場合は、[SSHキーパスをクラスターレベル](https://rancher.com/docs/rke/v0.1.x/en/config-options/#cluster-level-ssh-key-path)で設定できます。
> 各ノードに設定されたSSHキーパスは常に優先されます。

### SSH Key

SSHキーへのパスを設定する代わりに、ノードへの接続に使用される実際のキー、つまりssh_keyを代わりに指定できます。

### Kubernetes Roles

ノードをKubernetesクラスターの一部としてする役割のリストを指定できます。3つの役割がサポートされています： `controlplane`、`etcd` と `worker`。
ノードの役割は相互に排他的ではありません。
ロールの任意の組み合わせを任意のノードに割り当てることができます。
アップグレードプロセスを使用してノードの役割を変更することもできます。

> **Note：**  
> v0.1.8より前では、ワークロード/ポッドは `worker` または `controlplane` の役割を持つ任意のノードで実行されていましたが、v0.1.8の時点では、それらは任意の `worker` ノードにのみデプロイされます。

- etcd

この役割では、`etcd` コンテナーはこれらのノード上で実行されます。
Etcdはクラスタの状態を保持し、クラスタ内で最も重要なコンポーネントであり、クラスタの真実の源泉です。
1つのノードでのみetcdを実行できますが、HA構成を作成するには通常3、5、またはそれ以上のノードが必要です。
Etcdは、すべてのKubernetesの状態を格納する、信頼性の高い分散型Key-Valueストアです。
**etcd**ロールを持つ[Taints and Tolerations](https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/)を以下に示します。

| Taint Key | Taint Value | Taint Effect |
| --- | --- | --- |
| `node-role.kubernetes.io/etcd` | `true` | `NoExecute` |

- controlplane

この役割では、Kubernetesをデプロイするために使用されるステートレスコンポーネントがこれらのノード上で実行されます。
これらのコンポーネントは、APIサーバー、スケジューラー、およびコントローラーを実行するために使用されます。
**コントロールプレーン**の役割を持つ[Taints and Tolerations](https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/)を以下に示します。

| Taint Key | Taint Value | Taint Effect |
| --- | --- | --- |
| `node-role.kubernetes.io/controlplane` | `true` | `NoSchedule` |

- worker

この役割では、デプロイされているワークロードまたはポッドがこれらのノードに着地します。

### Docker Socket

Dockerソケットがデフォルトと異なる場合は、 `docker_socket` を設定できます。
デフォルトは `/var/run/docker.sock` です。

### Labels

各ノードに任意のラベルのマップを追加することができます。
これは、[イングレスコントローラ](https://rancher.com/docs/rke/v0.1.x/en/config-options/add-ons/ingress-controllers/)の `node_selector` オプションを使うときに使うことができます。

