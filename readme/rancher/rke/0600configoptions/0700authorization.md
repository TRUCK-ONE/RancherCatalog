# Authorization

Kubernetesは複数の[認証モジュール](https://kubernetes.io/docs/reference/access-authn-authz/authorization/#authorization-modules)をサポートしています。
現在、RKEは[RBACモジュール](https://kubernetes.io/docs/reference/access-authn-authz/rbac/)のみをサポートしています。

デフォルトでは、RBACはすでに有効になっています。
RBACのサポートを無効にしたい場合（推奨されません）は、認証モードを `none` に設定します。

```
authorization:
    # Use `mode: none` to disable authorization
    mode: rbac
```

