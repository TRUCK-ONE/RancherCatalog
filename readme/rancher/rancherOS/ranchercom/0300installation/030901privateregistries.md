# Private Registries

[cloud-config](https://rancher.com/docs/os/v1.x/en/installation/configuration/#cloud-config)を通ってサービスを起動するとき、DockerHubまたはプライベートレジストリから取得する必要がある場合があります。
これらの認証はあなたのクラウドと同じです。

たとえば、DockerHubの認証を追加するには

```
#cloud-config
rancher:
  registry_auths:
    https://index.docker.io/v1/:
      auth: dXNlcm5hbWU6cGFzc3dvcmQ=
```

`認証`キーは、`username:password` という形式の文字列をbase64でエンコードすることによって生成されます。
`docker login` コマンドを使用して`認証`キーを生成できます。
コマンドを実行して認証に成功したら、キーは `$HOME/.docker/config.json` ファイルにあります。

```
{
	"auths": {
		"https://index.docker.io/v1/": {
			"auth": "dXNlcm5hbWU6cGFzc3dvcmQ="
		}
	}
}
```

あるいは、ユーザー名とパスワードを直接指定することもできます。

```
#cloud-config
rancher:
  registry_auths:
    https://index.docker.io/v1/:
      username: username
      password: password
```

## Docker Client Authentication(Dockerクライアント認証)

Dockerクライアントの認証を構成することは、`registry_auth` キーによって処理されません。
代わりに、`write_files` ディレクティブを使用して、標準のDocker構成場所に資格情報を書き込むことができます。

```
#cloud-config
write_files:
  - path: /home/rancher/.docker/config.json
    permissions: "0755"
    owner: rancher
    content: |
      {
        "auths": {
          "https://index.docker.io/v1/": {
            "auth": "asdf=",
            "email": "not@val.id"
          }
        }
      }
```

## Certificates for Private Registries(個人用レジストリの証明書)

証明書は[Dockerのドキュメント](https://docs.docker.com/registry/insecure/)に従って、標準の場所（`/etc/docker/certs.d`）に保存できます。
[cloud-config](https://rancher.com/docs/os/v1.x/en/installation/configuration/#cloud-config) の `write_files` ディレクティブを使用して、証明書を `/etc/docker/certs.d` に直接書き込むことができます。

```
#cloud-config
write_files:
  - path: /etc/docker/certs.d/myregistrydomain.com:5000/ca.crt
    permissions: "0644"
    owner: root
    content: |
      -----BEGIN CERTIFICATE-----
      MIIDJjCCAg4CCQDLCSjwGXM72TANBgkqhkiG9w0BAQUFADBVMQswCQYDVQQGEwJB
      VTETMBEGA1UECBMKU29tZS1TdGF0ZTEhMB8GA1UEChMYSW50ZXJuZXQgV2lkZ2l0
      cyBQdHkgTHRkMQ4wDAYDVQQDEwVhbGVuYTAeFw0xNTA3MjMwMzUzMDdaFw0xNjA3
      MjIwMzUzMDdaMFUxCzAJBgNVBAYTAkFVMRMwEQYDVQQIEwpTb21lLVN0YXRlMSEw
      HwYDVQQKExhJbnRlcm5ldCBXaWRnaXRzIFB0eSBMdGQxDjAMBgNVBAMTBWFsZW5h
      MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAxdVIDGlAySQmighbfNqb
      TtqetENPXjNNq1JasIjGGZdOsmFvNciroNBgCps/HPJphICQwtHpNeKv4+ZuL0Yg
      1FECgW7oo6DOET74swUywtq/2IOeik+i+7skmpu1o9uNC+Fo+twpgHnGAaGk8IFm
      fP5gDgthrWBWlEPTPY1tmPjI2Hepu2hJ28SzdXi1CpjfFYOiWL8cUlvFBdyNqzqT
      uo6M2QCgSX3E1kXLnipRT6jUh0HokhFK4htAQ3hTBmzcxRkgTVZ/D0hA5lAocMKX
      EVP1Tlw0y1ext2ppS1NR9Sg46GP4+ATgT1m3ae7rWjQGuBEB6DyDgyxdEAvmAEH4
      LQIDAQABMA0GCSqGSIb3DQEBBQUAA4IBAQA45V0bnGPhIIkb54Gzjt9jyPJxPVTW
      mwTCP+0jtfLxAor5tFuCERVs8+cLw1wASfu4vH/yHJ/N/CW92yYmtqoGLuTsywJt
      u1+amECJaLyq0pZ5EjHqLjeys9yW728IifDxbQDX0cj7bBjYYzzUXp0DB/dtWb/U
      KdBmT1zYeKWmSxkXDFFSpL/SGKoqx3YLTdcIbgNHwKNMfTgD+wTZ/fvk0CLxye4P
      n/1ZWdSeZPAgjkha5MTUw3o1hjo/0H0ekI4erZFrZnG2N3lDaqDPR8djR+x7Gv6E
      vloANkUoc1pvzvxKoz2HIHUKf+xFT50xppx6wsQZ01pNMSNF0qgc1vvH
      -----END CERTIFICATE-----
```

