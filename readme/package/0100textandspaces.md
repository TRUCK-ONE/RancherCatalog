# Text and spaces

```
type Inventory struct {
	Material string
	Count    uint
}
sweaters := Inventory{"wool", 17}
tmpl, err := template.New("test").Parse("{{.Count}} items are made of {{.Material}}")
if err != nil { panic(err) }
err = tmpl.Execute(os.Stdout, sweaters)
if err != nil { panic(err) }
```
デフォルトでは、テンプレートの実行時にアクション間のすべてのテキストが逐語的にコピーされます。 たとえば、上記の例の文字列 "items are made of "は、プログラムの実行時に標準出力に表示されます。

ただし、テンプレートのソースコードの書式設定を容易にするために、アクションの左側の区切り文字（デフォルトでは "{{"）の直後にマイナス記号とASCIIスペース文字（ "{{ - "）が続く場合、末尾の空白はすべて切り捨てられます。 直前のテキスト 同様に、右側の区切り文字（ "}}"）の前にスペースとマイナス記号（ " - }}"）がある場合、先行するすべての空白は直後のテキストから切り捨てられます。 これらのトリムマーカーでは、ASCIIスペースが存在する必要があります。 "{{-3}}"は数値-3を含むアクションとして解析します。

たとえば、ソースが
```
"{{23 -}} < {{- 45}}"
```
生成される出力は、
```
"23<45"
```
このトリミングの場合、空白文字の定義はGoの場合と同じです。スペース、水平タブ、キャリッジリターン、および改行です。