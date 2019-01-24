# Configure all key-value pairs in a ConfigMap as container environment variables(ConfigMap内のすべてのキーと値のペアをコンテナー環境変数としての構成)

**注：** この機能はKubernetes v1.6以降で利用できます。
* 複数のキーと値のペアを含むConfigMapを作成します。
```
apiVersion: v1
kind: ConfigMap
metadata:
  name: special-config
  namespace: default
data:
  SPECIAL_LEVEL: very
  SPECIAL_TYPE: charm
```
* envFromを使用して、ConfigMapのすべてのデータをコンテナ環境変数として定義します。 ConfigMapのキーがPodの環境変数名になります。
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
      envFrom:
      - configMapRef:
          name: special-config
  restartPolicy: Never
```
* ポッド仕様への変更を保存します。 現在、Podの出力にはSPECIAL_LEVEL = veryおよびSPECIAL_TYPE = charmが含まれています。
