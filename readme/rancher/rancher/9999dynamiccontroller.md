# Rancher 2.0 Dynamic DNS Controller

[参考](https://github.com/rancher/rancher/wiki/Rancher-2.0-Dynamic-DNS-Controller)

動的DNSコントローラーは、以下のエンドポイントURLを機能させるために必要なDNSエントリーを作成します。

```
<http/https>://<$ingress_name>.<$namespace>.<$cluster_id>.<rancher_root_domain>/<$path>
```

## Dynamic DNS Controller

動的DNSコントローラは、クラスタ内のすべての入力リソースを監視し、`<rancher_root_domain>`に次のDNSエントリを作成します。

```
<$ingress_name>.<$namespace>.<$cluster_id>.<rancher_root_domain> => [ingress IPs]
```

入力リソースに10個を超えるIPがある場合、DNSから返されるのは10個のIPだけです。

RKEクラスタでは、ノードが異常になり、対応するnginx入力リソースが利用できなくなると、動的DNSコントローラはDNSマッピングを更新してそのノードIPをリストから削除します。

時々（デフォルト5m）、ダイナミックDNSコントローラはnginx入力リソースを再同期します。

時々（デフォルトは24時間）、動的DNSコントローラーはドメインを更新して有効期限を更新します。

Default Settings

| Nmae | Value |
| --- | --- |
| Default Ingress Resync Duration | 5分 |
| Default Domain Renew Duration | 24時間 |

## Rancher Dynamic DNS Service

Rancher DNSサーバーは以下のAPIを実装する必要があります。

| Operation | URL | Method/JSON Payload |
| --- | --- | --- |
| Create Domain | /v1/domain | POST<br><br>Payload: {"hosts": ["1.1.1.1", "2.2.2.2"]}<br><br> Returns: {"status":200,"msg":"", "token": "abcfdssadasd", "data":{"fqdn":"11111.lb.rancher.cloud","hosts":["1.1.1.1","2.2.2.2"],"expiration":"2018-04-29T10:08:12.906557355Z"}} |
| Get Domain | /v1/domain/<FQDN> | GET<br><br>[Header] Token: Bearer XXX<br><br>Returns: {"status":200,"msg":"","data":{"fqdn":"12345678.lb.rancher.cloud","hosts":["1.1.1.1","2.2.2.2"],"expiration":"2018-04-29T09:46:18.078663181Z"}} |
| Renew Domain | /v1/domain/<FQDN>/renew | PUT<br><br>[Header] Token: Bearer XXX<br><br>Returns: {"status":200,"msg":"","data":{"fqdn":"12345678.lb.rancher.cloud","hosts":["1.1.1.1","2.2.2.2"],"expiration":"2018-05-29T09:46:18.078663181Z"}
| Update Domain | /v1/domain/<FQDN> | PUT<br><br>[Header] Token: Bearer XXX<br><br>Payload: {"hosts": ["3.3.3.3", "4.4.4.4"]}<br><br>Returns {"status":200,"msg":"","data":"fqdn":"12345678.lb.rancher.cloud","hosts":["3.3.3.3","4.4.4.4"],"expiration":"2018-04-30T01:03:40.870506511Z"}}|
| Delete Domain | /v1/domain/<FQDN> | DELETE<br><br>[Header] Token: Bearer XXX |

create domain呼び出しで受信したトークンは、それ以降のupdateおよびdeleteの呼び出しでベアラトークンとして渡す必要があります。
DNSサービスの実装は、CoreDNSやPowerDNSのようなものを活用するべきです。

DNS Settings
| Nmae | Value |
| --- | --- |
| <rancher_root_domain> | lb.rancher.cloud |
| TTL | 1 minute |

## Configuring Dynamic DNS Service

グローバル変数設定で以下のパラメーターを構成します。

| Name | Value |
| --- | --- |
| `<ingress-ip-domain>` | lb.rancher.cloud |

1. <$ ingress-ip-domain>を指定したドメインに変更します。lb.rancher.cloud
1. 入力リソースを作成して、以下を選択します。

## TLS Support

TLSの場合、クラスタ設定は、`* .clusterid.lb.rancher.cloud` を要求し、RancherダイナミックDNSサービスにドメインに追加するためのチャレンジテキストレコードを送信します。

