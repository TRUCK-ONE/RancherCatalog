# Extra Args, Extra Binds, and Extra Environment Variables

RKEは追加のサービス引数、ボリュームバインド、および環境変数をサポートします。

## Extra Args

どのKubernetesサービスでも、`extra_args` を更新して既存のデフォルトを変更できます。

`v0.1.3` 以降、`extra_args` を使用すると新しい引数が追加され、既存のデフォルト値が**上書き**されます。
たとえば、デフォルトのアドミッションコントローラリストを変更する必要がある場合は、すべての変更が含まれるように、デフォルトリストを含めて変更内容を編集します。

`v0.1.3` より前のバージョンでは、`extra_args` を使用しても新しい引数がリストに追加されるだけで、デフォルトのリストを変更することはできませんでした。

```
services:
    kube-controller:
      extra_args:
        cluster-name: "mycluster"
```

## Extra Binds

`extra_binds` 引数を使用して、追加のボリュームバインドをサービスに追加できます。

```
services:
    kubelet:
      extra_binds:
        - "/host/dev:/dev"
        - "/usr/libexec/kubernetes/kubelet-plugins:/usr/libexec/kubernetes/kubelet-plugins:z"
```

## Extra Environment Variables

extra_env引数を使用して、追加の環境変数をサービスに追加できます。

```
services:
    kubelet:
      extra_env:
        - "HTTP_PROXY=http://your_proxy"
```

