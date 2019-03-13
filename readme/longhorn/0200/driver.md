# Kubernetes driver

## Background

LonghornをKubernetesで使用すると、Longhorn Container Storage Interface（CSI）ドライバまたはLonghorn FlexVolumeドライバのいずれかを介して永続的なストレージを提供できます。
Longhornは、Kubernetesクラスタ構成に応じて、いずれかのドライバを自動的にデプロイします。
ユーザーはデプロイメントyamlファイルでドライバを指定することもできます。
CSIが好ましい。

### Requirement for the CSI driver

1. Kubernetes v1.10+ 
    - CSIはこのバージョンのKubernetesのベータリリースで、デフォルトで有効になっています。
2. マウント伝搬機能ゲートが有効
    - Kubernetes v1.10ではデフォルトで有効になっています。
    しかし、RKEの初期のバージョンの中にはそれを有効にしないものもあります。
    - 環境確認スクリプトを使用して確認できます。
3. 上記の条件が満たされない場合、LonghornはFlexVolumeドライバにフォールバックします。

### Check if your setup satisfied CSI requirement

1. 次のコマンドを使用して、Kubernetesサーバのバージョンを確認してください。

    ```
    kubectl version
    ```
    結果：
    ```
    Client Version: version.Info{Major:"1", Minor:"10", GitVersion:"v1.10.3", GitCommit:"2bba0127d85d5a46ab4b778548be28623b32d0b0", GitTreeState:"clean", BuildDate:"2018-05-21T09:17:39Z", GoVersion:"go1.9.3", Compiler:"gc", Platform:"linux/amd64"}
    Server Version: version.Info{Major:"1", Minor:"10", GitVersion:"v1.10.1", GitCommit:"d4ab47518836c750f9949b9e0d387f20fb92260b", GitTreeState:"clean", BuildDate:"2018-04-12T14:14:26Z", GoVersion:"go1.9.3", Compiler:"gc", Platform:"linux/amd64"}
    ```

    サーバーバージョンはv1.10以上である必要があります。

2. 環境チェックスクリプトの結果にMountPropagationが含まれている必要があります。

### Requirement for the FlexVolume driver

1. Kubernetes v1.8+
2. curb、findmnt、grep、awk、blkidがKubernetesクラスタのすべてのノードにインストールされていることを確認します。

#### Flexvolume driver directory

LonghornはFlexvolumeディレクトリの場所を自動検出する機能を追加しました。

Flexvolumeドライバが正しくインストールされていない場合は、いくつかの理由が考えられます。

1. ホストOS上ではなく、kubeletがコンテナ内で実行されている場合、Flexvolumeドライバディレクトリのホストバインドマウントパス（--volume-plugin-dir）は、kubeletプロセスで使用されるパスと同じである必要があります。

    1-1. 例えば、kubeletがFlexvolumeドライバディレクトリとして /var/lib/kubelet/volumeplugins を使用している場合、そのディレクトリにはホストのbind-mountが存在する必要があります。
    /var/lib/kubelet/volumeplugins:/var/lib/kubelet/volumeplugins、または親ディレクトリに対する任意の理想的なバインドマウント。
    
    1-2. これは、Longhornがkubeletコマンドラインで使用されているディレクトリを検出して、ドライバをホストのどこにインストールするかを決定するためです。
2. Flexvolumeドライバディレクトリのkubelet設定は、すべてのノードで同じでなければなりません。
    
    2.1. Longhornは現時点では異種セットアップをサポートしていません。

### Environment check script

ユーザーが要因に関する十分な情報を集めるのを助けるためにスクリプトを書きました

インストールする前に、次のコマンドを実行してください。

```
curl -sSfL https://raw.githubusercontent.com/rancher/longhorn/master/scripts/environment_check.sh | bash
```

出力例：

```
daemonset.apps/longhorn-environment-check created
waiting for pods to become ready (0/3)
all pods ready (3/3)

  MountPropagation is enabled!

cleaning up...
daemonset.apps "longhorn-environment-check" deleted
clean up complete
```

