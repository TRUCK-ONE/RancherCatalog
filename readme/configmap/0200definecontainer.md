# ConfigMapデータを使用してコンテナー環境変数を定義

単一のConfigMapからのデータでコンテナー環境変数を定義します。

* 環境変数をConfigMapのキーと値のペアとして定義します。

```
kubectl create configmap special-config --from-literal=special.how=very
```

* ConfigMapで定義されているspecial.how値を、Pod仕様のSPECIAL_LEVEL_KEY環境変数に割り当てます。

```
kubectl edit pod dapi-test-pod
```

```
apiVersion: v1
kind: Pod
metadata:
  name: dapi-test-pod
spec:
  containers:
    - name: test-container
      image: k8s.gcr.io/busybox
      command: [ "/bin/sh", "-c", "env" ]
      env:
        # Define the environment variable
        - name: SPECIAL_LEVEL_KEY
          valueFrom:
            configMapKeyRef:
              # The ConfigMap containing the value you want to assign to SPECIAL_LEVEL_KEY
              name: special-config
              # Specify the key associated with the value
              key: special.how
  restartPolicy: Never
```

* ポッド仕様への変更を保存します。 これで、Podの出力にSPECIAL_LEVEL_KEY = veryが含まれます。
