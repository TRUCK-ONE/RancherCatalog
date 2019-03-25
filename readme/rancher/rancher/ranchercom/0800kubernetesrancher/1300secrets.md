# Secrets

[Secrets](https://kubernetes.io/docs/concepts/configuration/secret/#overview-of-secrets)には、パスワード、トークン、キーなどの機密データが格納されています。
1つ以上のキーと値のペアを含めることができます。
ワークロードを設定するときは、どの秘密を含めるかを選択できます。
構成マップと同様に、機密事項はワークロードでは環境変数またはボリューム・マウントとして参照できます。

> **Note：**  
> 秘密を更新しても、ポッドが再起動されるまで、ポッド内に自動的には反映されません。

[Secrets](https://kubernetes.io/docs/concepts/configuration/secret/#overview-of-secrets)には、パスワード、トークン、キーなどの機密データが格納されています。 1つ以上のキーと値のペアを含めることができます。

秘密を作成するときには、プロジェクト内の任意の配置で秘密を使用可能にすることも、単一の名前空間に制限することもできます。

1. **グローバル**ビューから、秘密を追加したいネームスペースを含むプロジェクトを選択します。

1. メインメニューから、**Resources > Secrets**を選択します。
**Secretを追加**をクリックします。

1. Secret の**名前**を入力してください。

    > **Note：**  
    > Kubernetesは秘密、証明書、ConfigMaps、およびレジストリをすべて[秘密](https://kubernetes.io/docs/concepts/configuration/secret/)として分類します。
    > プロジェクトまたはネームスペース内の2つの秘密が重複する名前を持つことはできません。
    > したがって、競合を防ぐために、あなたの秘密はあなたのワークスペース内のすべての秘密の中でユニークな名前を持たなければなりません。

1. 秘密の**スコープ**を選択してください。
プロジェクト全体または単一の[ネームスペース](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/projects-and-namespaces/#namespaces)でレジストリを使用可能にすることができます。

1. **Secret Values**から、**Add Secret Value**をクリックしてキーと値のペアを追加します。
必要なだけ多くの値を追加してください。

    > **Tip：**
    > コピーして貼り付けることで、秘密に複数のキーと値のペアを追加できます。
    > ![画像](../pictures/100013bulk-key-values.gif)

1. **保存**をクリックします。
    
**結果：** 選択したスコープに応じて、あなたの秘密がプロジェクトまたはネームスペースに追加されます。
Rancher UIでは、**Resources > Secrets** ビューから秘密を表示できます。

## What’s Next?

これで、プロジェクトまたはネームスペースに秘密が追加されたので、展開したワークロードにそれを追加できます。

これで、プロジェクトまたはネームスペースに秘密が追加されたので、展開したワークロードにそれを追加できます。

ワークロードに秘密を追加する方法の詳細については、[ワークロードのデプロイ](https://rancher.com/docs/rancher/v2.x/en/k8s-in-rancher/workloads/deploy-workloads/)を参照してください。


