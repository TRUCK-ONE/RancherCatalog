# Add ConfigMap data to a Volume

ファイルからのConfigMapの作成で説明したように、--from-fileを使用してConfigMapを作成すると、ファイル名がConfigMapのデータセクションに格納されたキーになります。 ファイルの内容がキーの価値になります。

このセクションの例は、次に示すspecial-configという名前のConfigMapを参照しています。

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: special-config
  namespace: default
data:
  special.level: very
  special.type: charm
```

## ConfigMapに格納されているデータをボリュームに追加する

Pod仕様のボリュームセクションの下にConfigMap名を追加します。 これにより、ConfigMapデータがvolumeMounts.mountPathとして指定されたディレクトリ（この場合は /etc/config ）に追加されます。
コマンドセクションは、ConfigMapに格納されているspecial.level項目を参照します。

```
apiVersion: v1
kind: Pod
metadata:
  name: dapi-test-pod
spec:
  containers:
    - name: test-container
      image: k8s.gcr.io/busybox
      command: [ "/bin/sh", "-c", "ls /etc/config/" ]
      volumeMounts:
      - name: config-volume
        mountPath: /etc/config
  volumes:
    - name: config-volume
      configMap:
        # Provide the name of the ConfigMap containing the files you want
        # to add to the container
        name: special-config
  restartPolicy: Never
```

ポッドが実行されると、コマンド（ "ls /etc/config/"）は次のような出力を生成します。

```
special.level
special.type
```

**注意：** ディレクトリ(/etc/config/) にファイルがいくつかあると、それらは削除されます。

## ボリューム内の特定のパスにConfigMapデータを追加します。

pathフィールドを使用して、特定のConfigMap項目に必要なファイルパスを指定します。
この場合、special.level項目は /etc/config/keys のconfig-volumeボリュームにマウントされます。

```
apiVersion: v1
kind: Pod
metadata:
  name: dapi-test-pod
spec:
  containers:
    - name: test-container
      image: k8s.gcr.io/busybox
      command: [ "/bin/sh","-c","cat /etc/config/keys" ]
      volumeMounts:
      - name: config-volume
        mountPath: /etc/config
  volumes:
    - name: config-volume
      configMap:
        name: special-config
        items:
        - key: special.level
          path: keys
  restartPolicy: Never
```

ポッドが実行されると、コマンド（ "cat /etc/config/keys"）は以下の出力を生成します。

```
very
```

## 特定のパスとファイルパーミッションへのプロジェクトキー

ファイルごとに、特定のパスと特定の権限にキーを投影できます。
Secretsユーザーガイドに構文が説明されています。

## マウントされたConfigMapは自動的に更新されます

ボリューム内ですでに消費されているConfigMapが更新されると、予測キーも最終的に更新されます。
Kubeletは、マウントされたConfigMapが定期的に同期されるたびに新鮮であるかどうかを確認しています。
ただし、ConfigMapの現在の値を取得するためにローカルのttlベースのキャッシュを使用しています。
その結果、ConfigMapが更新されてから新しいキーがポッドに投影されるまでの合計遅延は、kubelet同期期間+ kubelet内のConfigMapsキャッシュのttlと同じくらい長くなる可能性があります。

**注：** サブパスボリュームとしてConfigMapを使用しているコンテナは、ConfigMapの更新を受信しません。
