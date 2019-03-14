# User-Defined Add-Ons

[ネットワークプラグイン](0910networkplug-ins.md)と[入力コントローラ](0920ingresscontrollers.md)の他に、Kubernetesクラスタのデプロイ後にデプロイしたいアドオンを定義できます。

アドオンを指定する方法は2つあります。

- [In-line Add-ons](https://rancher.com/docs/rke/v0.1.x/en/config-options/add-ons/user-defined-add-ons/#in-line-add-ons)
- [Referencing YAML Files for Add-ons](https://rancher.com/docs/rke/v0.1.x/en/config-options/add-ons/user-defined-add-ons/#referencing-yaml-files-for-add-ons)

> **Note：**  
> ユーザー定義のアドオンを使用する場合は、すべてのリソースに対してネームスペースを定義する必要があります。
> そうしないと、それらは `kube-system` ネームスペースになります。

RKEはYAMLマニフェストを設定マップとしてKubernetesクラスタにアップロードします。 次に、configmapをマウントし、`kubectl apply -f` を使用してアドオンをデプロイするKubernetesジョブを実行します。

RKEは、`rke up` を複数回使用するときに追加のアドオンを追加するだけです。
RKEは、異なるアドオンのリストを使って `rke up` ときに、クラスタアドオンの削除を**サポートしません**。

v0.1.8の時点で、RKEはそれが同じ名前であればアドオンを更新します。

v0.1.8より前のバージョンでは、`kubectl edit` を使用してアドオンを更新してください。

## In-line Add-ons

アドオンをYAMLファイルで直接定義するには、`addons` ディレクティブが複数行の文字列オプションであるため、必ずYAMLのブロックインジケーター `|-` を使用してください。

`---` ディレクティブを使用してそれらを区切ることで複数のYAMLリソース定義を指定することが可能です。

```
addons: |-
    ---
    apiVersion: v1
    kind: Pod
    metadata:
      name: my-nginx
      namespace: default
    spec:
      containers:
      - name: my-nginx
        image: nginx
        ports:
        - containerPort: 80
```

## Referencing YAML files for Add-ons

`addons_include` ディレクティブを使用して、ローカルファイルまたはユーザー定義のアドオンのURLを参照します。

```
addons_include:
    - https://raw.githubusercontent.com/rook/rook/master/cluster/examples/kubernetes/ceph/operator.yaml
    - https://raw.githubusercontent.com/rook/rook/master/cluster/examples/kubernetes/ceph/cluster.yaml
    - /opt/manifests/example.yaml
    - ./nginx.yaml
```

