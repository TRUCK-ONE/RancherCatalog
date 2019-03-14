# Config Options

`cluster.yml` をRKE用に設定するとき、RKEがKubernetesを起動する方法の動作を制御するために設定できるたくさんの異なるオプションがあります。

クラスタ構成オプションで構成できるオプションはいくつかあります。
すべてのオプションを含むいくつかの[例のyamls](https://rancher.com/docs/rke/v0.1.x/en/example-yamls/)があります。

## Configuring Nodes

- [Nodes](https://rancher.com/docs/rke/v0.1.x/en/config-options/nodes/)
- [Ignoring unsupported Docker versions](https://rancher.com/docs/rke/v0.1.x/en/config-options/#supported-docker-versions)
- [Private Registries](https://rancher.com/docs/rke/v0.1.x/en/config-options/private-registries/)
- [Cluster Level SSH Key Path](https://rancher.com/docs/rke/v0.1.x/en/config-options/#cluster-level-ssh-key-path)
- [SSH Agent](https://rancher.com/docs/rke/v0.1.x/en/config-options/#ssh-agent)
- [Bastion Host](https://rancher.com/docs/rke/v0.1.x/en/config-options/bastion-host/)

## Configuring Kubernetes Cluster

- [Cluster Name](https://rancher.com/docs/rke/v0.1.x/en/config-options/#cluster-name)
- [Kubernetes Version](https://rancher.com/docs/rke/v0.1.x/en/config-options/#kubernetes-version)
- [System Images](https://rancher.com/docs/rke/v0.1.x/en/config-options/system-images/)
- [Services](https://rancher.com/docs/rke/v0.1.x/en/config-options/services/)
- [Extra Args and Binds and Environment Variables](https://rancher.com/docs/rke/v0.1.x/en/config-options/services/services-extras/)
- [External Etcd](https://rancher.com/docs/rke/v0.1.x/en/config-options/services/external-etcd/)
- [Authentication](https://rancher.com/docs/rke/v0.1.x/en/config-options/authentication/)
- [Authorization](https://rancher.com/docs/rke/v0.1.x/en/config-options/authorization/)
- [Cloud Providers](https://rancher.com/docs/rke/v0.1.x/en/config-options/cloud-providers/)
- [Add-ons](https://rancher.com/docs/rke/v0.1.x/en/config-options/add-ons/)
    - Add-ons Jobs Timeout
    - [Network Plugins](https://rancher.com/docs/rke/v0.1.x/en/config-options/add-ons/network-plugins/)
    - [Ingress Controller](https://rancher.com/docs/rke/v0.1.x/en/config-options/add-ons/ingress-controllers/)
    - [User-Defined-Add-ons](https://rancher.com/docs/rke/v0.1.x/en/config-options/add-ons/user-defined-add-ons/)

## Cluster Level Options(クラスタレベルのオプション)

### Cluster Name

デフォルトでは、クラスタの名前は `local` になります。
別の名前が必要な場合は、`cluster_name` ディレクティブを使用してクラスタの名前を変更します。
名前はクラスタの生成されたkubeconfigファイルに設定されます。

```
cluster_name: mycluster
```

## Supported Docker Versions(サポートされているDockerのバージョン)

デフォルトでは、RKEはすべてのホストにインストールされているDockerのバージョンを確認し、そのバージョンがKubernetesでサポートされていない場合はエラーで失敗します。
[サポートされているDockerのバージョンのリスト](https://github.com/rancher/rke/blob/master/docker/docker.go#L37-L42)は、各Kubernetesのバージョンに合わせて設定されています。
この動作を無効にするには、このオプションを `true` に設定します。

デフォルト値は `false` です。

```
ignore_docker_version: true
```

## Kubernetes Version(Kubernetesのバージョン)

クラスタにインストールするKubernetesのバージョンを選択できます。
RKEの各バージョンには、サポートされているKubernetesのバージョンの特定のリストがあります。
バージョンが　`kubernetes_version` で定義されていて、このリストに見つからない場合は、デフォルトのバージョンが使用されます。
下記のリストと異なるバージョンを使用したい場合は、[システムイメージ](https://rancher.com/docs/rke/v0.1.x/en/config-options/system-images/)オプションを使用してください。

サポートされているKubernetesバージョンのリストがご使用のRKEのバージョンに対してどのようなものであるかを調べるには、実行しているRKEバージョンの[リリースノート](https://github.com/rancher/rke/releases)を参照してください。

次のようにKubernetesのバージョンを設定できます。

```
kubernetes_version: "v1.11.6-rancher1-1"
```

`kubernetes_version` とシステムイメージの両方が定義されている場合は、システムイメージの設定が `kubernetes_version` よりも優先されます。

## Cluster Level SSH Key Path(クラスタレベルのSSHキーパス)

RKEは `ssh` を使ってホストに接続します。
通常、各ノードにはsshキーごとに独立したパス、つまり `ssh_key_path` が `nodes` セクションにありますが、クラスタ設定ファイル内のすべてのホストにアクセスできるSSHキーがある場合は、そのsshへのパスを設定できます。
最上位レベルのキー。
それ以外の場合は、[ノード](https://rancher.com/docs/rke/v0.1.x/en/config-options/nodes/)にSSHキーパスを設定します。

SSHキーパスがクラスタレベルおよびノードレベルで定義されている場合は、ノードレベルのキーが優先されます。

```
ssh_key_path: ~/.ssh/test
```

## SSH Agent

RKEは、ローカルSSHエージェントからのSSH接続設定の使用をサポートします。
このオプションのデフォルト値は `false` です。
ローカルのsshエージェントを使って設定したい場合は、これを `true` に設定します。

```
ssh_agent_auth: true
```

SSH秘密鍵をパスフレーズと一緒に使用したい場合は、その鍵を `ssh-agent` に追加し、環境変数 `SSH_AUTH_SOCK` を構成する必要があります。

```
$ eval "$(ssh-agent -s)"
Agent pid 3975
$ ssh-add /home/user/.ssh/id_rsa
Enter passphrase for /home/user/.ssh/id_rsa:
Identity added: /home/user/.ssh/id_rsa (/home/user/.ssh/id_rsa)
$ echo $SSH_AUTH_SOCK
/tmp/ssh-118TMqxrXsEx/agent.3974
```

## Add-ons Job Timeout

Kubernetesクラスタが起動した後に展開される [add-one](https://rancher.com/docs/rke/v0.1.x/en/config-options/add-ons/) を定義できます。
これはKubernetes[ジョブ](https://rancher.com/docs/rke/v0.1.x/en/config-options/add-ons/)を使用します。
RKEは、タイムアウト（秒単位）の後にジョブ状況を検索しようとするのを停止します。
デフォルトのタイムアウト値は `30` 秒です。

```
addon_job_timeout: 30
```

