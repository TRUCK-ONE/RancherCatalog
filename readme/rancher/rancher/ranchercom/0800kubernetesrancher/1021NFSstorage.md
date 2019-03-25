# NFS Storage

Rancher展開でNFSストレージボリュームプラグインを使用する前に、NFSサーバーを準備する必要があります。

> **Note：**
> - すでにNFS共有を持っている場合は、Rancher内でNFSボリュームプラグインを使用するために新しいNFSサーバーをプロビジョニングする必要はありません。
> 代わりに、この手順の残りをスキップして[ストレージの追加](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/volumes-and-storage/)を完了してください。
> - この手順では、Ubuntuを使ってNFSサーバーを設定する方法を説明しますが、他のLinuxディストリビューション（Debian、RHEL、Arch Linuxなど）にもこれらの指示を使用できるはずです。
> 他のLinuxディストリビューションを使用してNFSサーバーを作成する方法についての公式な指示については、ディストリビューションのドキュメントを参照してください。
> 
> **お勧め：**  
>ファイアウォールルールを管理するプロセスを簡素化するために、NFSv4を使用してください。

1. リモートターミナル接続を使用して、NFSストレージに使用する予定のUbuntuサーバーにログインします。

1. 次のコマンドを入力してください。
    ```
    sudo apt-get install nfs-kernel-server
    ```

1. 以下のコマンドを入力します。
これは、ストレージに使用されるディレクトリを、ユーザーアクセス権と一緒に設定します。
ストレージを別のディレクトリに保存したい場合は、コマンドを変更してください。
    ```
    mkdir -p /nfs && chown nobody:nogroup /nfs
    ```
    - `-p /nfs` パラメーターは、ルートに `nfs` という名前のディレクトリーを作成します。
    - `chown nobody:nogroup /nfs` パラメータは、ストレージディレクトリへのすべてのアクセスを許可します。

1. NFSエクスポートテーブルを作成します。
このテーブルは、サーバーをストレージとして使用するノードに公開されているNFSサーバー上のディレクトリパスを設定します。

    1. 選択したテキストエディタを使用して `/etc/exports` を開きます。

    1. クラスタノードのIPアドレスと共に、手順3で作成した `/nfs` フォルダのパスを追加します。
    クラスタ内の各IPアドレスのエントリを追加します。
    各アドレスとそれに付随するパラメーターの後に、区切り文字である単一のスペースを入れます。
        ```
        /nfs <IP_ADDRESS1>(rw,sync,no_subtree_check) <IP_ADDRESS2>(rw,sync,no_subtree_check) <IP_ADDRESS3>(rw,sync,no_subtree_check)
        ```
        **Tip：** IPアドレスをサブネットに置き換えることができます。 例えば、`10.212.50.12/24`です。

    1. 次のコマンドを入力してNFSテーブルを更新します。
        ```
        exportfs -ra
        ```

1. NFSで使用されているポートを開きます。

    1. NFSが使用しているポートを調べるには、次のコマンドを入力します。
        ```
        rpcinfo -p | grep nfs
        ```

    1. 前のコマンドが出力した[ポートを開きます](https://help.ubuntu.com/lts/serverguide/firewall.html.en)。
    たとえば、次のコマンドはポート2049を開きます。
        ```
        sudo ufw allow 2049
        ```

**結果：** お使いのNFSサーバは、Rancherノードとのストレージに使用されるように設定されています。

## What’s Next?

Rancher内で、NFSサーバーを[ストレージボリューム](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/volumes-and-storage/#adding-a-persistent-volume)または[ストレージクラス](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/volumes-and-storage/#adding-storage-classes)、あるいはその両方として追加します。
サーバーを追加したら、展開用のストレージとして使用できます。

