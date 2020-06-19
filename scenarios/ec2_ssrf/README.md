# シナリオ: ec2_ssrf

**サイズ:** 中

**難しさ:** 普通

**コマンド:** `$ ./cloudgoat.py create ec2_ssrf`

## シナリオリソース

- 1 VPC with:
	- EC2 x 1
- 1 Lambda Function
- 1 S3 Bucket

## 最初の情報

1. IAM User "Solus"

## シナリオの目的

"cg-lambda-[ CloudGoat ID ]"というLambdaの実行

## 要約

アタッカーはIAMユーザーのSolusとなり、LambdaにReadOnly権限でアクセスできることを見つけ、そこにハードコードされたシークレットを元に見つけたEC2インスタンスが、サーバーサイドリクエストフォージェリ(SSRF)に脆弱性があるwebアプリケーションを使用していることを発見します。脆弱性をつき、EC2メタデータサービスからキーを入手したら、プライベートなS3バケットへアクセスをして、Lambdaを実行するためのキーを手に入れシナリオ完了を目指します。

## 攻撃経路

![Scenario Route(s)](https://www.lucidchart.com/publicSegments/view/3117f737-3290-48c6-b0bf-e122a305858d/image.png)

## ガイド - IAMユーザー "Solus"

1. IAMユーザーのSolusとしてAWS環境の調査を進めると、アタッカーはアカウント内のLambdaを一覧できることを発見します。
2. Lambdaの中でアタッカーは他のユーザー、IAMユーザーWrexのAWSアクセスキーを発見します。
3. Wrexとして調査を続けると、アタッカーはSSRFに脆弱性があるwebアプリケーションが稼働しているEC2インスタンスを見つけます。
4. SSRFの脆弱性を`?url=...`パラメータを使用して調査をしていくと、EC2メタデータサービスからAWSキーを取得することができます。
5. EC2からキーを使うことで、アタッカーはさらに上の権限をもつユーザー、Shepardの
AWSユーザ情報が入ったプライベートなS3バケットを見つけます。
6. Shephardとして操作をすることで、フルアドミン権限を丁に入れたら、シナリオの目的であるLambdaを実行することができます。

この方法のチートシートは[こちら](./cheat_sheet_solus.md).