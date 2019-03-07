# Use ConfigMap-defined environment variables in Pod commands

$(VAR_NAME) Kubernetes置換構文を使用して、Pod仕様のコマンドセクションでConfigMap定義の環境変数を使用できます。

例：

次のポッド仕様
```
apiVersion: v1
kind: Pod
metadata:
  name: dapi-test-pod
spec:
  containers:
    - name: test-container
      image: k8s.gcr.io/busybox
      command: [ "/bin/sh", "-c", "echo $(SPECIAL_LEVEL_KEY) $(SPECIAL_TYPE_KEY)" ]
      env:
        - name: SPECIAL_LEVEL_KEY
          valueFrom:
            configMapKeyRef:
              name: special-config
              key: SPECIAL_LEVEL
        - name: SPECIAL_TYPE_KEY
          valueFrom:
            configMapKeyRef:
              name: special-config
              key: SPECIAL_TYPE
  restartPolicy: Never
```
テストコンテナに次の出力を生成します。
```
very charm
```