# RancherOS 利用方法

[参考URL](https://cloudgarage.jp/support/?action=artikel&cat=51&id=79&artlang=ja)

RancherOSはISOによる提供となるためご利用いただくにはインストール作業が必要です。

コントロールパネルにてRancherOSを選択してインスタンスを起動した後、VNCコンソールに接続してください。
（対象のインスタンスを選択し、「コンソール表示」から接続可能です）

1. VNCコンソールではrancherユーザーでログインした状態となっております。
passwdコマンドにてrancherのパスワードを変更してください（root権限があるためsudoを利用します）。

1. 以後の作業もVNCコンソールでも作業可能ですがCopyANDPasteが行えなかったり不便な点もあるため、
普段お使いのSSHクライアントで設定したパスワードを用いてrancherユーザーにログインします。
（以降の手順例は $sudo -s にてroot昇格後としています）

1. cloud-config.ymlを作成する（ここでは事前にローカルPCや他サーバー上で作成した鍵ファイルを公開鍵として設定しています）

    `#vi cloud-config.yml`　 と入力し以下を記述

    ```
    #cloud-config
    ssh_authorized_keys:
    - ssh-rsa **************
    ```

1. `# mkfs.ext4 -L B2D_STATE /dev/vda` を実行

1. `# ros install -c cloud-config.yml -d /dev/vda` を実行
手順の過程でインストールや再起動の確認が行われますが、ｙを選択してください。

1. 再起動後、コントロールパネルにてISOのアンマウントとインスタンスの再起動（強制）を行ってください。

1. 再度、SSHにて接続後、`# docker run -d -p 8080:8080 rancher/server` にてrancher/serverを起動します。

1. ブラウザでhttp://＜IPアドレス＞:8080 にアクセスしウェブページが表示されればrancherが起動しています。

---

以下、整理前

参考URL　その2

[【Docker】RancherOSで本格的な自宅サーバーを構築しよう(1) ~ 要件と構成 ~](https://qiita.com/okamu_/items/da52b4dc12221feca55c)

[【Docker】RancherOSで本格的な自宅サーバーを構築しよう(2) ~ rancher-serverをインストール ~](https://qiita.com/okamu_/items/305e0af4545901d44a94)

[【Docker】RancherOSで本格的な自宅サーバーを構築しよう(3) ~ rancher-agentを構築しよう ~](https://qiita.com/okamu_/items/500c3705d2f4610cebe0)

[【Docker】RancherOSで本格的な自宅サーバーを構築しよう(4) ~ GlusterFSを使ってデータを共有＋永続化しよう ~](https://qiita.com/okamu_/items/2620da1b2e93437e8a74)

[【Docker】RancherOSで本格的な自宅サーバーを構築しよう(5) ~private registry でdocker imageを管理~](https://qiita.com/okamu_/items/cf0a9569638d00a9e451)

[Rancherで提供されているDockerのライブラリ一覧](https://qiita.com/okamu_/items/f6875d5bce2ac6752758)



