# Define container environment variables using ConfigMap data
## Define container environment variables with data from multiple ConfigMaps

* 前の例と同様に、最初にConfigMapsを作成します。

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: special-config
  namespace: default
data:
  special.how: very
```

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: env-config
  namespace: default
data:
  log_level: INFO
```

* Pod仕様の環境変数を定義します。

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
        - name: SPECIAL_LEVEL_KEY
          valueFrom:
            configMapKeyRef:
              name: special-config
              key: special.how
        - name: LOG_LEVEL
          valueFrom:
            configMapKeyRef:
              name: env-config
              key: log_level
  restartPolicy: Never
```

* ポッド仕様への変更を保存します。 現在、Podの出力にはSPECIAL_LEVEL_KEY = veryとLOG_LEVEL = INFOが含まれています。
