# Container Storage Interface (CSI)

Kubernetesからコンテナ用ストレージを制御するためのAPI

参考URL： [Container Storage Interface](https://github.com/container-storage-interface/spec/blob/master/spec.md)

## 用語

|      | 定義 |
| ---- | ---- |
| Volume | CSIを介してCO管理のコンテナ内で利用可能になるストレージの単位 |
| Block Volume | コンテナ内でブロックデバイスとして表示されるボリューム |
| Mounted Volume | 指定されたファイルシステムを使用してマウントされ、コンテナ内のディレクトリとして表示されるボリューム |
| CO | Container Orchestrationシステムは、CSIサービスRPCを使用してプラグインと通信する |
| SP | CSIプラグイン実装のベンダーであるStorage Provider |
| RPC | リモートプロシージャコール([英語](https://en.wikipedia.org/wiki/Remote_procedure_call)／[日本語](https://ja.wikipedia.org/wiki/%E9%81%A0%E9%9A%94%E6%89%8B%E7%B6%9A%E3%81%8D%E5%91%BC%E5%87%BA%E3%81%97)) |
| Node | ユーザーワークロードが実行されるホスト。プラグインの観点からノードIDによって一意に識別できます。 |
| Plugin | CSIサービスを実装するgRPCエンドポイント、別名「プラグイン実装」 |
| Plugin Supervisor | プラグインのライフサイクルを管理するプロセス、COがあります。 |
| Workload | COによってスケジュールされた「作業」の原子単位。これは、コンテナまたはコンテナの集合です。 |

## 目的

ストレージベンダー（SP）が一度プラグインを開発し、それを多数のコンテナーオーケストレーション（CO）システムで機能させることを可能にする業界標準の「コンテナーストレージインターフェース」（CSI）を定義すること。

## 目標

コンテナ保管インターフェース（CSI）は
- SP作成者が、CSIを実装しているすべてのCOで「機能する」CSI準拠のプラグインを1つ作成できるようにします。
- 有効にするAPI（RPC）を定義します。
    - ボリュームの動的プロビジョニングとプロビジョニング解除
    - ノードへのボリュームの接続または切断
    - ノードへのボリュームのマウント/マウント解除
    - ブロックボリュームとマウント可能ボリュームの両方の消費
    - ローカルストレージプロバイダ（デバイスマッパー、lvmなど）
    - スナップショットの作成と削除（スナップショットのソースはボリュームです）
    - スナップショットから新しいボリュームをプロビジョニングする（元のボリュームのデータが消去されてスナップショットのデータに置き換えられるスナップショットを元に戻す）は、範囲外です。
- プラグインプロトコル勧告を定義する。
    - スーパーバイザがプラグインを設定するプロセスを説明します。
    - コンテナの配置に関する考慮事項（CAP_SYS_ADMIN、mount namespace など）。

## 非目標

Container Storage Interface（CSI）は、明示的に定義、提供、または指示を行いません。
- プラグインスーパーバイザがプラグインのライフサイクルを管理するための特定のメカニズム
    - 状態を維持する方法（取り付けられているもの、取り付けられているものなど）
    - 展開、インストール、アップグレード、アンインストール、監視、または再生成の方法（予期せぬ終了の場合）プラグイン
- 「ストレージの等級」（別名「ストレージクラス」）を表すファーストクラスのメッセージ構造/フィールド
- プロトコルレベルの認証と承認
- プラグインのパッケージ化
- POSIX準拠：CSIは提供されたボリュームがPOSIX準拠のファイルシステムであることを保証しません。 準拠は、プラグインの実装（およびそれが依存しているバックエンドストレージシステム）によって決定されます。 CSIは、POSIX準拠の方法でプラグイン管理ボリュームまたはプラグイン管理ボリュームと対話することをプラグインスーパーバイザーまたはCOに妨害してはなりません。

## 概要

この仕様は、CSI互換プラグインを実装するための、ストレージプロバイダ（SP）のための最低限の運用上およびパッケージ上の推奨事項とともにインタフェースを定義します。 インタフェースはプラグインが公開しなければならないRPCを宣言します。これがCSI仕様の主な焦点です。 運用上およびパッケージ上の推奨事項には、CO間の互換性を促進するための追加のガイダンスがあります。

### Architecture
---
(省略)

---

## Container Storage Interface

このセクションではCOとプラグインの間のインターフェースについて説明します。

### RPC Interface

COはRPCを通じてプラグインと対話します。 各SPは以下を提供しなければならない：
- **Node Plugin：** SPプロビジョニングされたボリュームが公開されるノード上で実行されなければならないCSI RPCを提供するgRPCエンドポイント
- **Controller Plugin：** CSI RPCにサービスを提供するgRPCエンドポイント
- 状況によっては、単一のgRPCエンドポイントがすべてのCSI RPCにサービスを提供してもよい（アーキテクチャの図3を参照）。

```
syntax = "proto3";
package csi.v1;

import "google/protobuf/descriptor.proto";
import "google/protobuf/timestamp.proto";
import "google/protobuf/wrappers.proto";

option go_package = "csi";

extend google.protobuf.FieldOptions {
  // Indicates that a field MAY contain information that is sensitive
  // and MUST be treated as such (e.g. not logged).
  bool csi_secret = 1059;
}
```
RPCには3つのセットがあります。

- **Identity Service：** Node PluginとController PluginはどちらもこのRPCのセットを実装しなければなりません。
- **Controller Service：** コントローラプラグインはこのRPCのセットを実装しなければなりません。
- **Node Service：** Node PluginはこのRPCのセットを実装しなければなりません。

---
以下略

---

