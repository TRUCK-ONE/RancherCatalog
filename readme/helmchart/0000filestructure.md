## Chart の構造

Chart は、ディレクトリ内のファイルの集まりとして構成されています。 ディレクトリ名は Chart の名前です（バージョン管理情報なし）。 したがって、WordPressを記述するチャートはディレクトリ(wordpress/) に格納されます。

このディレクトリの中で、Helmはこれにマッチする構造を期待するでしょう：
``` Chart 構造
wordpress/
  Chart.yaml          # Chart に関する情報を含む YAML ファイル
  LICENSE             # (オプション) Chart のライセンスを含む text ファイル
  README.md           # (オプション) 人用 README ファイル
  requirements.yaml   # (オプション) Chart の依存関係をリストした YAML ファイル
  values.yaml         # Chart の初期設定値
  charts/             # Chart が依存する Chart を含むディレクトリ
  templates/          # 値と組み合わせると有効な Kubernetes マニフェストファイルを生成するテンプレートのディレクトリ
  templates/NOTES.txt # (オプション) 使用上の注意を含む text ファイル
```
Helmは ディレクトリ(chart/ および templates/)、およびリストされているファイル名の使用を予約しています。 他のファイルはそのまま残ります。