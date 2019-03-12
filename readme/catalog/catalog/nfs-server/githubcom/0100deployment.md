# Deployment

[参考](https://github.com/kubernetes-incubator/external-storage/blob/master/nfs/docs/deployment.md)

## Getting the provisioner image

nock-provisionerを実行したいマシンにDockerイメージを取り込むには、それをビルドするか、Quayから最新リリースを入手することができます。
あなたが望むなら不安定な最新のタグを使用することができますが、すべての例のyamlsは最新のバージョン管理されたリリースタグを参照します。

### Building

プロジェクトの構築は、プロジェクトがあなたのGOPATHにある場合にのみ機能します。
go getを使用するか手動でクローンを作成して、プロジェクトをGOPATHディレクトリにダウンロードします。

```
$ go get github.com/kubernetes-incubator/external-storage
```

最新のリリースをチェックアウトし、プロジェクトディレクトリのmakeコンテナを実行して、プロジェクトとDockerイメージをビルドします。

```
$ cd $GOPATH/src/github.com/kubernetes-incubator/external-storage/nfs
$ make container
```

### Pulling

あなたがKubernetesで走っているなら、それはあなたのためにQuayからimageを引き出すでしょう。
それともあなた自身でそれをすることができます。

```
$ docker pull quay.io/kubernetes_incubator/nfs-provisioner:latest
```

## Deploying the provisioner

これでDockerのイメージはあなたのマシン上にあります。
まだ持っていなければ、1.4以降のクラスタを立ち上げます。

```
$ ALLOW_SECURITY_CONTEXT=true API_HOST_IP=0.0.0.0 $GOPATH/src/k8s.io/kubernetes/hack/local-up-cluster.sh
```

命名規則に従うプロビジョニング担当者に付ける固有の名前を決定します。
`<vendor name>/<provisioner name>`ここで、`<vendor name>`は "kubernetes.io"にはできません。
プロビジョニング担当者は、この名前に等しいプロビジョニング担当者フィールドを設定してStorageClassを要求するクレームに対してのみボリュームをプロビジョニングします。
たとえば、ツリー内のGCEおよびAWSプロビジョニング担当者の名前は、`kubernetes.io/gce-pd` および `kubernetes.io/aws-ebs` です。

nfs-provisionerの実行方法を決定して、以下のセクションのいずれかに従ってください。
推奨される方法は、`Deployment/StatefulSet` を作成し、それをhostPathボリュームなどの永続的なストレージでバックアップする、単一インスタンスのステートフルアプリケーションとして実行することです。
スタンドアロンのコンテナまたはバイナリとしてKubernetesの外部で実行するのは、アプリのライフサイクルやPV単位のクォータを設定する機能をさらに制御したい場合のためです。

- In Kubernetes - Deployment
- In Kubernetes - StatefulSet
- Outside of Kubernetes - container
- Outside of Kubernetes - binary

## In Kubernetes - Deployment of 1 replica

`deploy/kubernetes/deployment.yaml` のargsフィールドのprovisioner引数を編集して、決めたプロビジョニング担当者の名前にします。

`deploy/kubernetes/deployment.yaml` は、`/export` にマウントされたhostPathボリューム `/srv` を指定します。
`/export` ディレクトリは、プロビジョニング担当者が状態とプロビジョニングされたPersistentVolumesのデータを格納する場所であるため、そこにボリュームをマウントすることで、プロビジョニングされたPVのバッキングストレージとして指定します。
hostPathを編集したり、PersistentVolumeClaimのように他の種類のボリュームを `/export` にマウントすることもできます。
マウントされたボリュームにはサポートされているファイルシステムが必要です。
Linux上のローカルファイルシステムはサポートされていますが、NFSはサポートされていません。

hostPathボリュームを使用する場合は、そのパスがプロビジョニング担当者のスケジュールされているノードに存在する必要があるので、nodeSelectorを使用して特定のノードを選択し、そこにディレクトリが存在するようにします。
`mkdir -p /srv`。
SELinuxがノードに適用されている場合は、コンテナを特権付きにするか、ノード上のディレクトリのセキュリティコンテキストを変更する必要があります。
`sudo chcon -Rt svirt_sandbox_file_t/srv`。

`deploy/kubernetes/deployment.yaml` もサービスを設定します。
サービスの名前はSERVICE_NAME env変数を介して渡されるため、デプロイメントのポッドは、自身の不安定なポッドIPではなく、サービスのクラスタIPをPersistentVolumesに配置するためのNFSサーバーIPとして使用します。
ポッドごとに常に1つのサービスが存在する必要があります。
そうしないとプロビジョニングが失敗します。
つまり、展開は1レプリカを超えて拡張できません。
規模を拡大するには（複数のインスタンスがリーダー選出を行う場合）、ラベル+セレクターとSERVICE_NAME変数を一致させて、新しい名前で新しい展開とサービスのペアを作成する必要があります。

展開とそのサービスを作成します。

```
$ kubectl create -f deploy/kubernetes/psp.yaml # or if openshift: oc create -f deploy/kubernetes/scc.yaml\
# Set the subject of the RBAC objects to the current namespace where the provisioner is being deployed
$ NAMESPACE=`kubectl config get-contexts | grep '^*' | tr -s ' ' | cut -d' ' -f5`
$ sed -i'' "s/namespace:.*/namespace: $NAMESPACE/g" ./deploy/kubernetes/rbac.yaml
$ kubectl create -f deploy/kubernetes/rbac.yaml
$ kubectl create -f deploy/kubernetes/deployment.yaml
```

## In Kubernetes - StatefulSet of 1 replica

ステートフルセットを実行する手順は、上記のデプロイメントの手順と同じです。
そのため、そこにデプロイメントが表示されている場合は常に、それをステートフルセットに置き換えます。
利点は安定したホスト名を取得できることです。
ステートフルセットのほとんどの例とは異なり、サービスはヘッドレスにはできません。

## Outside of Kubernetes - container

コンテナーは、マスターまたはkubeconfigセットのいずれかで実行する必要があります。
kubeconfig引数が機能するには、configファイル、およびcertificate-authorityのようにパスによって参照される証明書ファイル（/var/run/kubernetes/apiserver.crt）が、なんらかの方法でコンテナ内に存在する必要があります。
これは、Dockerボリュームを作成するか、Dockerfileがあるフォルダーにファイルをコピーし、イメージを構築する前に `COPY config /.kube/config` のような行をDockerfileに追加することによって実行できます。

あなたが決めた名前に等しいprovisionerとmasterかkubeconfig setのどちらかでnfs-provisionerを実行します。
Ganeshaが機能するためには、機能DAC_READ_SEARCHで実行する必要があります。
オプションで、機能SYS_RESOURCEでも実行して、Ganeshaが開くことのできるファイルの上限数を設定できるようにする必要があります。
Docker 1.10以降を使用している場合は、より寛容なseccompプロファイル（unconfined または deploy/docker/nfs-provisioner-seccomp.json）も必要です。

server-hostnameフラグを設定することで、NFSサーバーがエクスポートするホスト名、つまりPVに置くサーバーIPを指定することができます。

```
$ docker run --cap-add DAC_READ_SEARCH --cap-add SYS_RESOURCE \
--security-opt seccomp:deploy/docker/nfs-provisioner-seccomp.json \
-v $HOME/.kube:/.kube:Z \
quay.io/kubernetes_incubator/nfs-provisioner:latest \
-provisioner=example.com/nfs \
-kubeconfig=/.kube/config
```
または
```
$ docker run --cap-add DAC_READ_SEARCH --cap-add SYS_RESOURCE \
--security-opt seccomp:deploy/docker/nfs-provisioner-seccomp.json \
quay.io/kubernetes_incubator/nfs-provisioner:latest \
-provisioner=example.com/nfs \
-master=http://172.17.0.1:8080
```

コンテナの `/export` にDockerボリュームを作成してマウントすることができます。
`/export` ディレクトリは、プロビジョニング担当者がプロビジョニングされたPersistentVolumesのデータを格納する場所です。
そのため、そこにボリュームをマウントすることで、プロビジョニングされたPVのバッキングストレージとして指定します。
元のコンテナーが停止した場合、そのボリュームは別のコンテナーで再利用できます。
Kubernetesがなければ、ライフサイクルを自分で管理する必要があります。
あなたはそれがボリューム内の共有を提供し続けるために再起動を乗り切ることができるようにコンテナに安定したIPをどうにかして与えるべきです。

また、PV単位の割り当て制限を有効にすることもできます。
これは、xfsプロジェクトレベルのクォータに基づいているため、`/export` にマウントされたボリュームは`prjquota/pquota`オプションでマウントされたxfsである必要があります。
また、xfs_quotaを実行する特権があることも必要です。

上記の2つのオプションを指定すると、runコマンドは次のようになります。

```
$ docker run --privileged \
-v $HOME/.kube:/.kube:Z \
-v /xfs:/export:Z \
quay.io/kubernetes_incubator/nfs-provisioner:latest \
-provisioner=example.com/nfs \
-kubeconfig=/.kube/config \
-enable-xfs-quota=true
```

## Outside of Kubernetes - binary

このようにnfs-provisionerを実行すると、ホストマシン上で直接エクスポートを操作できます。
すべてのデータを作成して `/export` に保存するので、ディレクトリが存在し、使用可能であることを確認してください。
use-ganeshaフラグの設定方法に応じて、ホストがすでにNFS GaneshaまたはカーネルNFSサーバーのいずれかを実行していると想定して実行されます。
慎重に使用してください。

NFSサーバーがホスト上でどのように実行されているかに応じて、決定した名前、masterまたはkubeconfig、run-server、false、use-ganeshaのいずれかに設定した名前で、nfs-provisionerを実行します。
おそらくrootとして実行する必要があります。

server-hostnameフラグを設定することで、NFSサーバーがエクスポートするホスト名、つまりPVに置くサーバーIPを指定することができます。

```
$ sudo ./nfs-provisioner -provisioner=example.com/nfs \
-kubeconfig=$HOME/.kube/config \
-run-server=false \
-use-ganesha=false
```
または
```
$ sudo ./nfs-provisioner -provisioner=example.com/nfs \
-master=http://0.0.0.0:8080 \
-run-server=false \
-use-ganesha=false
```

PV単位の割り当て制限を有効にすることをお勧めします。
これは、xfsプロジェクトレベルのクォータに基づいているため、`/export` にマウントされたボリュームは`prjquota/pquota`オプションでマウントされたxfsである必要があります。 有効にするには、`-enable-xfs-quota = true`引数を追加します。

```
$ sudo ./nfs-provisioner -provisioner=example.com/nfs \
-kubeconfig=$HOME/.kube/config \
-run-server=false \
-use-ganesha=false \
-enable-xfs-quota=true
```

---
プロビジョニングツールのデプロイが完了したので、それを使用する方法については[Usage](0200usage.md)を参照してください。

---

## Arguments

- provisioner： provisionerの名前。 provisionerは、この名前に等しいprovisionerフィールドを設定してStorageClassを要求するクレームに対してのみボリュームをプロビジョニングします。
- master： クライアント設定を構築するためのマスターURL。
provisioner がクラスタを使い果たしている場合は、thisまたはkubeconfigのいずれかを設定する必要があります。
- kubeconfig： kubeconfigファイルへの絶対パス。
provisionerがクラスタを使い果たしている場合は、thisまたはmasterのいずれかを設定する必要があります。
- run-server： provisionerがNFSサーバーの実行、つまりNFS Ganeshaの起動と停止を担当している場合。
デフォルトはtrueです。
- use-ganesha： provisionerがカーネルNFSサーバー（ `exportfs`）を使用するのではなく、NFS Ganesha（D-Busメソッド呼び出し）を使用してボリュームを作成する場合。
run-serverがtrueの場合、これはtrueでなければなりません。
デフォルトはtrueです。
- grace-period： 0から180までの、秒単位で使うNFS Ganeshaの猶予期間。
サーバーが再起動後も存続しないと予想される場合、つまりポッドとして実行されていてエクスポートディレクトリが保持されない場合は、0に設定できます。
run-serverとuse-ganeshaの両方が真の場合にのみ設定できます。
デフォルトは90です。
- enable-xfs-quota： プロビジョニング担当者がプロビジョニングするボリュームごとにxfs割り当て制限を設定する場合。
ボリュームを作成するディレクトリ（ `/export`）がオプション `prjquota/pquota` でマウントされたxfsであり、xfs_quotaを実行する特権を持っている必要があります。
デフォルトはfalseです。
- failed-retry-threshold： プロビジョニング失敗時の再試行回数を設定された試行回数に制限する必要がある場合。 
デフォルト10。
- server-hostname： エクスポート元のNFSサーバーのホスト名。
クラスタ外で実行している場合にのみ適用できます。
つまり、masterまたはkubeconfigが設定されている場合にのみ設定できます。
設定しない場合は、hostname -iで最初に出力されたIPが使用されます。
