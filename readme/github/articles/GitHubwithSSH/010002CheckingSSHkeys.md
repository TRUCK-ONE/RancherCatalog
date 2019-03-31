# Checking for existing SSH keys

SSHキーを生成する前に、既存のSSHキーがあるかどうかを確認できます。

> **Note：**
> DSA鍵はOpenSSH 7.0では推奨されませんでした。
> オペレーティングシステムがOpenSSHを使用している場合は、SSHを設定するときにRSAキーなどの別の種類のキーを使用する必要があります。
> たとえば、オペレーティングシステムがMacOS Sierraの場合、RSAキーを使用してSSHを設定できます。

## Windows



## Linux

1. Open Terminal

1. 既存のSSHキーが存在するかどうかを確認するには、`ls -al ~/.ssh` と入力します。

    ```
    $ ls -al ~/.ssh
    # Lists the files in your .ssh directory, if they exist
    ```

1. ディレクトリのリストを調べて、公開SSH鍵がすでにあるかどうかを確認してください。

デフォルトでは、公開鍵のファイル名は次のいずれかです。

- id_dsa.pub
- id_ecdsa.pub
- id_ed25519.pub
- id_rsa.pub

既存の公開鍵と秘密鍵のペアがない場合、またはGitHubに接続するために利用可能なものを使用したくない場合は、[新しいSSH鍵を生成](https://help.github.com/en/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)します。

GitHubへの接続に使用したい既存の公開鍵と秘密鍵のペア（例えば、id_rsa.pubとid_rsa）がリストに表示されているのであれば、[SSH鍵をssh-agentに追加](https://help.github.com/en/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent#adding-your-ssh-key-to-the-ssh-agent)することができます。

> **Tip：**  
> `~/.ssh` が存在しないというエラーが表示されても、心配しないでください。
> [新しいSSHキーを生成](https://help.github.com/en/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)したらそれを作成します。


