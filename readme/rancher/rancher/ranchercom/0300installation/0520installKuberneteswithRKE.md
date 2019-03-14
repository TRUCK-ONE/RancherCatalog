# 2. Install Kubernetes with RKE

RKEを使用して、高可用性etcd構成でKubernetesをインストールしてください。

> **Note：**  
> インターネットに直接アクセスできないシステムの場合、インストールの詳細については [Air Gap：High Availability Install](https://rancher.com/docs/rancher/v2.x/en/installation/air-gap-high-availability/) を参照してください。

## Create the `rancher-cluster.yml` File

以下のサンプルを使って `rancher-cluster.yml` ファイルを作成してください。
ノードリストのIPアドレスを、作成した3つのノードのIPアドレスまたはDNS名に置き換えます。

> **Note：**  
> ノードにパブリックアドレスと内部アドレスがある場合は、 `internal_address:` を設定することをお勧めします。
> したがって、Kubernetesはクラスタ内通信にそれを使用します。
> AWS EC2などの一部のサービスでは、自己参照型のセキュリティグループまたはファイアウォールを使用する場合は `internal_address:` を設定する必要があります。

```
nodes:
  - address: 165.227.114.63
    internal_address: 172.16.22.12
    user: ubuntu
    role: [controlplane,worker,etcd]
  - address: 165.227.116.167
    internal_address: 172.16.32.37
    user: ubuntu
    role: [controlplane,worker,etcd]
  - address: 165.227.127.226
    internal_address: 172.16.42.73
    user: ubuntu
    role: [controlplane,worker,etcd]

services:
  etcd:
    snapshot: true
    creation: 6h
    retention: 24h
```

### Common RKE Nodes Options(共通RKEノードオプション)

| Option | Required	| Description |
| --- | --- | --- |
| `address` | yes | パブリックDNSまたはIPアドレス |
| `user` | yes | dockerコマンドを実行できるユーザー |
| `role` | yes | ノードに割り当てられているKubernetesロールのリスト |
| `internal_address` | no | 内部クラスタトラフィックのプライベートDNSまたはIPアドレス |
| `ssh_key_path` | no | ノードへの認証に使用されるSSH秘密鍵へのパス（デフォルトは `~/.ssh/id_rsa` ） |

### Advanced Configurations(高度な設定)

RKEには、特定の環境に合わせてインストールをカスタマイズするための多くの設定オプションがあります。

オプションと機能の完全なリストについては [RKE Documentation](https://rancher.com/docs/rke/v0.1.x/en/config-options/) を見てください。

## Run RKE

```
rke up --config ./rancher-cluster.yml
```

終了したら、次の行で終わります：`Finished building Kubernetes cluster successfully`

## Testing Your Cluster

RKEはファイル `kube_config_rancher-cluster.yml` を作成しているはずです。
このファイルには、 `kubectl` と `helm` の認証情報があります。

> **Note：**  
> `rancher-cluster.yml` とは異なるファイル名を使用した場合、kube設定ファイルは `kube_config_<FILE_NAME>.yml` という名前になります。

このファイルを `$HOME/.kube/config` にコピーするか、複数のKubernetesクラスタを使用している場合は、 `KUBECONFIG` 環境変数を   `kube_config_rancher-cluster.yml` のパスに設定します。

```
export KUBECONFIG=$(pwd)/kube_config_rancher-cluster.yml
```

`kubectl` を使用して接続性をテストし、すべてのノードが `Ready` 状態になっているかどうかを確認してください。

```
kubectl get nodes

NAME                          STATUS    ROLES                      AGE       VERSION
165.227.114.63                Ready     controlplane,etcd,worker   11m       v1.10.1
165.227.116.167               Ready     controlplane,etcd,worker   11m       v1.10.1
165.227.127.226               Ready     controlplane,etcd,worker   11m       v1.10.1
```

## Check the Health of Your Cluster Pods(クラスタポッドの状態を確認する)

必要なポッドと容器がすべて正常であることを確認して、続行する準備が整いました。

- ポッドは `Runnning` または `Completed` です。
- `READY` 列には、 `STATUS` `Running` のポッドに対してすべてのコンテナが実行中（つまり `3/3`）であることが示されます。
- `STATUS` `Completed` のポッドは1回限りのジョブです。
これらのポッドの場合、 `READY` は `0/1` になります。

```
kubectl get pods --all-namespaces

NAMESPACE       NAME                                      READY     STATUS      RESTARTS   AGE
ingress-nginx   nginx-ingress-controller-tnsn4            1/1       Running     0          30s
ingress-nginx   nginx-ingress-controller-tw2ht            1/1       Running     0          30s
ingress-nginx   nginx-ingress-controller-v874b            1/1       Running     0          30s
kube-system     canal-jp4hz                               3/3       Running     0          30s
kube-system     canal-z2hg8                               3/3       Running     0          30s
kube-system     canal-z6kpw                               3/3       Running     0          30s
kube-system     kube-dns-7588d5b5f5-sf4vh                 3/3       Running     0          30s
kube-system     kube-dns-autoscaler-5db9bbb766-jz2k6      1/1       Running     0          30s
kube-system     metrics-server-97bc649d5-4rl2q            1/1       Running     0          30s
kube-system     rke-ingress-controller-deploy-job-bhzgm   0/1       Completed   0          30s
kube-system     rke-kubedns-addon-deploy-job-gl7t4        0/1       Completed   0          30s
kube-system     rke-metrics-addon-deploy-job-7ljkc        0/1       Completed   0          30s
kube-system     rke-network-plugin-deploy-job-6pbgj       0/1       Completed   0          30s
```

## Save Your Files(ファイルを保存する)

`kube_config_rancher-cluster.yml` ファイルと`rancher-cluster.yml` ファイルのコピーを保存します。
Rancherインスタンスを保守およびアップグレードするためにこれらのファイルが必要になります。

## Issues or errors?(問題やエラー)

[トラブルシューティングページ](https://rancher.com/docs/rancher/v2.x/en/installation/ha/kubernetes-rke/troubleshooting/)を参照してください。

## [Next: Initialize Helm (Install tiller)](https://rancher.com/docs/rancher/v2.x/en/installation/ha/helm-init/)

