# Installation

このセクションでは、開発環境および実稼働環境にRancherをインストールする方法について説明します。

## Installation Options

- Single Node Installation  
    Rancherを単一のLinuxホストにインストールします。
    セットアップが簡単で、サーバーをユーザーベースですぐに使用できるようにする必要がないため、開発およびテスト環境にはシングルノードインストールをお勧めします。
    開発者またはテスターのみです。
- High Availability Installation  
    このインストールシナリオでは、Rancher Serverを高可用性（HA）構成で実行するための専用の新しいKubernetesクラスタを作成します。
    このクラスタは、1つのクラスタノードが実行されていれば常にアクセス可能です。
    ユーザーベースでアプリケーションに24時間365日アクセスする必要がある実稼働環境での高可用性インストールをお勧めします。

## Reference(参照)

- Requirements(必要条件)
    Rancherをホストしているサーバーのハードウェア要件とソフトウェア要件の参照。

- Port Requirements(ポート要件)
    Rancherを操作するために開く必要がある必須ポートのリスト。
