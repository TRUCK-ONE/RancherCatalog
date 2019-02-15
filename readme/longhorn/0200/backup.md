# Backup

ユーザーは、Longhornボリュームのバックアップを保存するためにS3またはNFSタイプのバックアップストアをセットアップできます。

ユーザーがAWS S3にアクセスできない場合、または最初に試してみる場合は、Minioを使用してローカルのS3テスト用バックアップストアをセットアップする方法も提供しています。

### Setup AWS S3 backupstore

1. AWS S3に新しいバケットを作成します。
2. ガイドに従って、次の権限を設定して新しいAWS IAMユーザーを作成します。

    ```
    {
        "Version": "2012-10-17",
        "Statement": [
            {
                "Sid": "GrantLonghornBackupstoreAccess0",
                "Effect": "Allow",
                "Action": [
                    "s3:PutObject",
                    "s3:GetObject",
                    "s3:ListBucket",
                    "s3:DeleteObject"
                ],
                "Resource": [
                    "arn:aws:s3:::<your-bucket-name>",
                    "arn:aws:s3:::<your-bucket-name>/*"
                ]
            }
        ]
    }
    ```

3. Longhornが配置されているネームスペースに、Kubernetesシークレットをaws-secretなどの名前で作成します（デフォルトではlonghorn-system）。
次の鍵を秘密にしてください。

```
AWS_ACCESS_KEY_ID: <your_aws_access_key_id>
AWS_SECRET_ACCESS_KEY: <your_aws_secret_access_key>
```

4. Longhorn UIに移動し、Settings/General/BackupTargetをに設定します。

    ```
    s3://<your-bucket-name>@<your-aws-region>/
    ```

    あなたが/最後に持っているべきであることに注意を払う、そうでなければあなたはエラーを受けるでしょう。

5. Settings/General/BackupTargetSecret を設定します。

    ```
    aws-secret
    ```

3番目のポイントからのAWSキーを持つあなたの秘密の名前。

### Setup a local testing backupstore

./deploy/backupstoresに、テスト用にNFSサーバーとMinio S3サーバーに基づく2つのテスト目的のバックアップストアを提供します。

次のコマンドを使用して、longhorn-systemが作成された後にBackupStore用にMinio S3サーバーをセットアップします。

```
kubectl create -f https://raw.githubusercontent.com/rancher/longhorn/master/deploy/backupstores/minio-backupstore.yaml
```

Settings/General/BackupTarget を設定します。

```
s3://backupbucket@us-east-1/backupstore
```

Setttings/General/BackupTargetSecret 

```
minio-secret
```

UIの[バックアップ]タブをクリックすると、エラーなしで空のリストが表示されます。

minio-secret.yaml このようになります：

```
apiVersion: v1
kind: Secret
metadata:
  name: minio-secret
  namespace: longhorn-system
type: Opaque
data:
  AWS_ACCESS_KEY_ID: bG9uZ2hvcm4tdGVzdC1hY2Nlc3Mta2V5 # longhorn-test-access-key
  AWS_SECRET_ACCESS_KEY: bG9uZ2hvcm4tdGVzdC1zZWNyZXQta2V5 # longhorn-test-secret-key
  AWS_ENDPOINTS: aHR0cDovL21pbmlvLXNlcnZpY2UuZGVmYXVsdDo5MDAw # http://minio-service.default:9000
```

Longhornがアクセスするには、秘密がlonghorn-system名前空間に作成されている必要があります。

### NFS backupstore

バックアップストアとしてNFSサーバーを使用するには、NFSサーバーがNFSv4をサポートしている必要があります。

ターゲットURLは次のようになります。

```
nfs://longhorn-test-nfs-svc.default:/opt/backupstore
```

テスト目的のNFSバックアップストアの例は[こちら](https://github.com/rancher/longhorn/blob/master/deploy/backupstores/nfs-backupstore.yaml)にあります。
