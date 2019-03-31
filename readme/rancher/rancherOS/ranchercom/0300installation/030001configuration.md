# Configuration

RancherOSを構成する方法は2つあります。

1. 最初にRancherOSを起動するときにcloud-configファイルを使用して設定を提供できます。
1. `ros config` コマンドを使用して手動で設定を変更します。

通常、最初にサーバーを起動するときに、cloud-configファイルを渡してサーバーの初期化を設定します。
初回起動後、設定に変更がある場合は、`ros config` を使用して必要な設定プロパティを設定することをお勧めします。
変更はすべてディスクに保存され、変更を適用するには再起動が必要になります。

## Cloud-Config

Cloud-configは、多くのLinuxディストリビューションでサポートされている宣言型の設定ファイルフォーマットで、RancherOSの主要な設定メカニズムです。

cloud-configをサポートするLinux OSは、起動中にcloud-initプロセスを呼び出してcloud-configファイルを解析し、オペレーティングシステムを設定します。
RancherOSは、システムコンテナ内で独自のcloud-initプロセスを実行します。
cloud-initプロセスは、さまざまなデータソースからcloud-configファイルを取得しようとします。
cloud-initがcloud-configファイルを取得すると、cloud-configファイルの内容に従ってLinux OSを設定します。





---


以下略