# シナリオ: rce_web_app

**サイズ:** 中

**難しさ:** 難しい

**コマンド:** `$ ./cloudgoat.py create rce_web_app`

## シナリオリソース

* 1 VPC with:
  * ELB x 1
  * EC2 x 1
  * S3 x 3
  * RDS x 1
* 2 IAM Users

## 最初の情報

1. IAMユーザー "Lara"
2. IAMユーザー "McDuck"

## シナリオの目的

RDSデータベースに保存されたシークレットの発見

## 要約

アタッカーはIAMユーザーのLaraとなり、脆弱性を探すためにロードバランサーとS3バケットを調査し、webアプリにRCEの脆弱性があることを発見し、機密情報の取得およびシナリオの目的であるセキュアなRDSデータベースインスタンスへアクセスします。

もしくは、アタッカーはIAMユーザーのMcDuckとなり、S3バケットを調査し、EC2サーバへの直接アクセスができるSSHキーを手に入れ、データベースへアクセスすることもできます。

## 攻撃位経路

![Scenario Route(s)](https://www.lucidchart.com/publicSegments/view/1b75f181-4d6e-4ad7-b3fb-56dd54efab66/image.png)

## ガイド - IAMユーザー “Lara”

1. アタッカーはIAMユーザーのLaraとしてAWS環境を探していると、セキュアなロードバランサーの後ろにあるwebアプリケーションを発見しました。
2. アタッカーはS3バケットを一覧し、ロードバランサーのログが入ったバケットを発見します。
3. ロードバランサーのログを見ていると、アタッカーはwebアプリには隠されたアドミンページがあることを発見します。
4. アドミンURLにアクセスすると、アタッカーはフォームに埋め込まれたパラメータ経由でリモートでコードが実行される(RCE)脆弱性を発見します。
5. アタッカーはこの脆弱性を利用してwebアプリが稼働しているEC2インスタンスへのシェルアクセスを手に入れます。

**分岐 A**

1. Now working through the EC2 instance (and therefore operating with its role's more expansive permissions), the attacker is able to access a private S3 bucket.
2. Inside the private S3 bucket, the attacker finds a text file left behind by an irresponsible developer which contains the login credentials for an RDS database.
3. The attacker then uses the EC2 instance to list and discover the RDS database referenced in the credentials file.
4. Finally, the attacker is able to access the RDS database using the credentials they found and acquires the scenario's goal: the secret text stored in the RDS database!

**分岐 B**

1. Struck by sudden inspiration, the attacker queries the EC2 metadata service and discovers the RDS database credentials and address.
2. The attacker is then able to access the RDS database using the credentials they found and acquires the scenario's goal: the secret text stored in the RDS database!

A cheat sheet for this route is available [here](./cheat_sheet_lara.md).

## ガイド - IAMユーザー “McDuck”

1. The attacker explores the AWS environment and discovers they are able to list S3 buckets using their starting keys.
2. The attacker discovers several S3 buckets, but they are only able to access one of them. Inside that one S3 bucket they find a pair of SSH keys.
3. The attacker lists EC2 instances and finds the EC2 instance behind the Load Balancer.
4. The attacker discovers that the SSH keys found in the S3 bucket enable the attacker to log into the EC2 instance.
5. Now working through the EC2 instance (and therefore operating with its role instead of McDuck's), the attacker is able to discover and access a private S3 bucket.
7. Inside the private S3 bucket, the attacker finds a text file left behind by an irresponsible developer which contains the login credentials for an RDS database.
7. The attacker is able to list and discover the RDS database referenced in the credentials file.
8. The attacker is finally able to access the RDB database using the credentials they found in step 6 and acquire the scenario's goal: the secret text stored in the RDS database.

A cheat sheet for this route is available [here](./cheat_sheet_mcduck.md).