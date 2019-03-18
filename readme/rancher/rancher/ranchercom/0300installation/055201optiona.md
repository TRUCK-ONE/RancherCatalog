# Option A—Bring Your Own Certificate: Self-Signed

> **前提条件：**  
> 自己署名証明書を作成します。
> - 証明書ファイルは[PEM形式](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-7-lb/#pem)でなければなりません。
> - 証明書ファイルは[base64](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-7-lb/#base64)でエンコードされている必要があります。
> - 証明書ファイルで、すべての中間証明書をチェーンに含めます。 最初にあなたの証明書と一緒にあなたの証明書を注文し、続いて中間物を注文します。 例として、[SSL FAQ/Troubleshooting](https://rancher.com/docs/rancher/v2.x/en/installation/ha/rke-add-on/layer-7-lb/#cert-order)を見てください。

In `kind: Secret` with `name: cattle-keys-ingress`、`<BASE64_CA>`をCA証明書ファイルのbase64エンコード文字列（通常は `ca.pem` または `ca.crt` と呼ばれる）に置き換えます。

> **Note：**  
> base64でエンコードされた文字列は、`cacerts.pem` と同じ行にあります。
> 先頭、その間、最後に改行はありません。

値を置き換えた後、ファイルは次の例のようになります（base64でエンコードされた文字列は異なるはずです）。

```
    ---
    apiVersion: v1
    kind: Secret
    metadata:
        name: cattle-keys-server
        namespace: cattle-system
    type: Opaque
    data:
        cacerts.pem: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUNvRENDQVlnQ0NRRHVVWjZuMEZWeU16QU5CZ2txaGtpRzl3MEJBUXNGQURBU01SQXdEZ1lEVlFRRERBZDAKWlhOMExXTmhNQjRYRFRFNE1EVXdOakl4TURRd09Wb1hEVEU0TURjd05USXhNRFF3T1Zvd0VqRVFNQTRHQTFVRQpBd3dIZEdWemRDMWpZVENDQVNJd0RRWUpLb1pJaHZjTkFRRUJCUUFEZ2dFUEFEQ0NBUW9DZ2dFQkFNQmpBS3dQCndhRUhwQTdaRW1iWWczaTNYNlppVmtGZFJGckJlTmFYTHFPL2R0RUdmWktqYUF0Wm45R1VsckQxZUlUS3UzVHgKOWlGVlV4Mmo1Z0tyWmpwWitCUnFiZ1BNbk5hS1hocmRTdDRtUUN0VFFZdGRYMVFZS0pUbWF5NU45N3FoNTZtWQprMllKRkpOWVhHWlJabkdMUXJQNk04VHZramF0ZnZOdmJ0WmtkY2orYlY3aWhXanp2d2theHRUVjZlUGxuM2p5CnJUeXBBTDliYnlVcHlad3E2MWQvb0Q4VUtwZ2lZM1dOWmN1YnNvSjhxWlRsTnN6UjVadEFJV0tjSE5ZbE93d2oKaG41RE1tSFpwZ0ZGNW14TU52akxPRUc0S0ZRU3laYlV2QzlZRUhLZTUxbGVxa1lmQmtBZWpPY002TnlWQUh1dApuay9DMHpXcGdENkIwbkVDQXdFQUFUQU5CZ2txaGtpRzl3MEJBUXNGQUFPQ0FRRUFHTCtaNkRzK2R4WTZsU2VBClZHSkMvdzE1bHJ2ZXdia1YxN3hvcmlyNEMxVURJSXB6YXdCdFJRSGdSWXVtblVqOGo4T0hFWUFDUEthR3BTVUsKRDVuVWdzV0pMUUV0TDA2eTh6M3A0MDBrSlZFZW9xZlVnYjQrK1JLRVJrWmowWXR3NEN0WHhwOVMzVkd4NmNOQQozZVlqRnRQd2hoYWVEQmdma1hXQWtISXFDcEsrN3RYem9pRGpXbi8walI2VDcrSGlaNEZjZ1AzYnd3K3NjUDIyCjlDQVZ1ZFg4TWpEQ1hTcll0Y0ZINllBanlCSTJjbDhoSkJqa2E3aERpVC9DaFlEZlFFVFZDM3crQjBDYjF1NWcKdE03Z2NGcUw4OVdhMnp5UzdNdXk5bEthUDBvTXl1Ty82Tm1wNjNsVnRHeEZKSFh4WTN6M0lycGxlbTNZQThpTwpmbmlYZXc9PQotLS0tLUVORCBDRVJUSUZJQ0FURS0tLS0tCg==
```

