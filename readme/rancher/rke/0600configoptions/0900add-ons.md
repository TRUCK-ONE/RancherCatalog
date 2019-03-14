# Add-Ons

RKEはプラガブルアドオンをサポートしています。
アドオンは、次のようないくつかのクラスタコンポーネントをデプロイするために使用されます。

- [Network plug-ins](https://rancher.com/docs/rke/v0.1.x/en/config-options/add-ons/network-plugins/)
- [Ingress controller](https://rancher.com/docs/rke/v0.1.x/en/config-options/add-ons/ingress-controllers/)
- KubeDNS

`system_images` [ディレクティブ](https://rancher.com/docs/rke/v0.1.x/en/config-options/system-images/)の下でこれらのアドオンに使用されるイメージ。
Kubernetesのバージョンごとに、各アドオンに関連付けられているデフォルトのイメージがありますが、これらは `system_images` のimageタグを変更することで上書きできます。

これらのプラガブルアドオンに加えて、クラスタの配置が完了した後に配置したいアドオンを指定できます。

RKEは、`rke up` を複数回使用するときに追加のアドオンを追加するだけです。
RKEは、異なるアドオンのリストを使って `rke up` ときに、クラスタアドオンの削除を**サポートしません**。

v0.1.8の時点で、RKEはそれが同じ名前であればアドオンを更新します。

v0.1.8より前のバージョンでは、`kubectl edit` を使用してアドオンを更新してください。

## Critical and Non-Critical Add-ons

バージョンv0.1.7から、アドオンは2つのカテゴリに分けられます。

- **重要なアドオン：** これらのアドオンが何らかの理由でデプロイに失敗した場合、RKEはエラーになります。
- **クリティカルではないアドオン：** これらのアドオンがデプロイに失敗した場合、RKEは警告をログに記録するだけで他のアドオンのデプロイを続けます。

現在、[network plug-in](https://rancher.com/docs/rke/v0.1.x/en/config-options/add-ons/network-plugins/)だけが重要と考えられています。 KubeDNS、[ingress controllers](https://rancher.com/docs/rke/v0.1.x/en/config-options/add-ons/ingress-controllers/)、そして[ユーザー定義のアドオン](https://rancher.com/docs/rke/v0.1.x/en/config-options/add-ons/user-defined-add-ons/)は重要ではありません。

## Add-on deployment jobs

RKEはKubernetesジョブを使用してアドオンを展開します。
場合によっては、アドオンの展開に予想以上に時間がかかることがあります。
バージョンv0.1.7から、RKEはジョブチェックのタイムアウトを秒単位で制御するオプションを提供します。
このタイムアウトはクラスタレベルで設定されます。

```
addon_job_timeout: 30
```

