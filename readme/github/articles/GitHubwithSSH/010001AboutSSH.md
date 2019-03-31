# About SSH

参考：[About SSH](https://help.github.com/en/articles/about-ssh)

SSHプロトコルを使用すると、リモートサーバーおよびサービスに接続して認証することができます。
SSHキーを使用すると、毎回アクセスするときにユーザー名やパスワードを入力しなくてもGitHubに接続できます。

SSHを設定したら、[SSHキーを生成してそれをssh-agentに追加](https://help.github.com/en/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)してから、[そのキーをGitHubアカウントに追加](https://help.github.com/en/articles/adding-a-new-ssh-key-to-your-github-account)します。
SSHキーをssh-agentに追加すると、パスフレーズを使用してSSHキーのセキュリティがさらに強化されます。 詳しくは、[SSHキーパスフレーズの操作](https://help.github.com/en/articles/working-with-ssh-key-passphrases)を参照してください。

SAMLシングルサインオンを使用する組織が所有するリポジトリでSSHキーを使用するには、まずそれを承認する必要があります。
詳細については、[SAMLシングルサインオン組織で使用するためのSSHキーの認証](https://help.github.com/en/articles/authorizing-an-ssh-key-for-use-with-a-saml-single-sign-on-organization)を参照してください。

[SSHキーリストを定期的に確認](https://help.github.com/en/articles/reviewing-your-ssh-keys)し、無効なものや侵害されたものはすべて無効にすることをお勧めします。

SSHキーを1年間使用していない場合、GitHubはセキュリティ上の予防措置として、無効なSSHキーを自動的に削除します。 詳しくは、[SSHキーの削除または欠落](https://help.github.com/en/articles/deleted-or-missing-ssh-keys)を参照してください。

## 参考文献

- "Checking for existing SSH keys"
- "Testing your SSH connection"
- "Working with SSH key passphrases"
- "Troubleshooting SSH"
- "Authorizing an SSH key for use with a SAML single sign-on organization"

