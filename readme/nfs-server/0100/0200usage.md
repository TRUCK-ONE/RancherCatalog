# Usage

[参考](https://github.com/kubernetes-incubator/external-storage/blob/master/nfs/docs/usage.md)

nfs-provisionerがデプロイされ、ボリュームをプロビジョニングする必要があるという主張を監視しています。
要求に対する要求に対して適切に構成されたStorageClassが作成されるまで、そのような要求は存在できません。

deploy / kubernetes / class.yamlのprovisionerフィールドを編集して、プロビジョニング担当者の名前にします。 パラメータを設定します。

## Parameters

- gid： "none"または "1001"のような補足グループ。
補助グループで実行されているポッドは共有を読み書きできますが、補助グループがないルート以外のポッドではできないようなパーミッションでNFS共有が作成されます。
rootとして実行されているポッドは、rootSquashパラメーターがtrueに設定されていない限り、ここでの設定に関係なく共有への読み書きができます。
"none"に設定すると、誰でもrootまたはroot以外のユーザーが共有に書き込むことができます。
デフォルト（省略した場合） "none"
- rootSquash： "true"または "false" 各エクスポートにNFS Ganesha root_id_squashまたはkernel root_squashオプションを追加して、rootユーザーを隠蔽するかどうか。
デフォルトは "false"です。
- **廃止予定です。 代わりにStorageClass.mountOptionsを使用してください。** mountOptions：このクラスのすべてのPVをマウントするためのマウントオプションのカンマ区切りリスト。
リストは、検証なしで、すべてのPVのマウントオプションの注釈/フィールドに直接挿入されます。
デフォルトの空白 ""。

StorageClassには好きな名前を付けてください。
名前はクレームがこのクラスを要求する方法です。
クラスを作成してください。

```
$ kubectl create -f deploy/kubernetes/class.yaml
storageclass "example-nfs" created
```

すべてが正しく機能している場合、作成したクラスを要求する要求を作成すると、プロビジョニング担当者が自動的にボリュームを作成します。

`deploy/kubernetes/claim.yaml` の `volume.beta.kubernetes.io/storage-class` アノテーションを編集してクラスの名前にします。
申し立てを作成します。

```
$ kubectl create -f deploy/kubernetes/claim.yaml
persistentvolumeclaim "nfs" created
```

nfs-provisionerは、作成したばかりのPVCにPVをプロビジョニングします。
その再生ポリシーはDeleteです。そのため、PVCが削除されると、それとそのバッキングストレージはプロビジョニング担当者によって削除されます。

```
$ kubectl get pv
NAME                                       CAPACITY   ACCESSMODES   RECLAIMPOLICY   STATUS      CLAIM         REASON    AGE
pvc-dce84888-7a9d-11e6-b1ee-5254001e0c1b   1Mi        RWX           Delete          Bound       default/nfs             23s
```

ポッドはPVCを消費し、バッキングNFS共有に書き込むことができます。
これをテストするためにポッドを作成します。

```
$ kubectl create -f deploy/kubernetes/write-pod.yaml
pod "write-pod" created
$ kubectl get pod --show-all
nfs-provisioner   1/1       Running     0          31s
write-pod         0/1       Completed   0          41s
```

PVCを使い終えたら、それを削除すると、プロビジョニング担当者がPVとそのバッキングストレージを削除します。

```
$ kubectl delete pod write-pod
pod "write-pod" deleted
$ kubectl delete pvc nfs
persistentvolumeclaim "nfs" deleted
$ kubectl get pv
```

プロビジョニングプログラムを削除または停止しても、作成したPersistentVolumeオブジェクトは削除されません。

いずれかの点で正しく機能しない場合は、kubectl logsを使用してプロビジョニング担当者のログを確認し、kubectl describeを使用してPVおよびPVC内のイベントを探します。


## Using as default

プロビジョニング担当者はデフォルトのストレージプロバイダーとして使用できます。
つまり、StorageClassを要求しない要求は、デフォルトでプロビジョニング担当者によってプロビジョニングされたボリュームを取得します。
プロビジョニング担当者を指定するStorageClassをデフォルトとして設定するには、DefaultStorageClass admission-pluginをオンにして、クラスに `storageclass.beta.kubernetes.io/is-default-class` アノテーションを追加します。
詳細については、[http://kubernetes.io/docs/user-guide/persistent-volumes/#class-1](http://kubernetes.io/docs/user-guide/persistent-volumes/#class-1)を参照してください。

