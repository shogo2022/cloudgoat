# シナリオ: iam_privesc_by_attachment

**サイズ:** 中

**難しさ:** 普通

**コマンド:** `$ ./cloudgoat.py create iam_privesc_by_attachment`

## シナリオリソース

* 1 VPC with:
  * EC2 x 1
* 1 IAM User

## 最初の情報

1. IAMユーザー "Kerrigan"

## シナリオの目的

EC2インスタンス"cg-super-critical-security-server."の削除

## 要約

アタッカーにはごく限られた権限が与えられますが、instance-profile-attachment権限を使い、自身の権限以上の権限を持つEC2インスタンスを作成します。このEC2インスタンスにアクセスすることでアタッカーはこのアカウントの中でのフル管理者権限を持つことになり、このシナリオのゴールである、cg-super-critical-security-serverの削除とさらなる攻撃が可能となります。

Note: このシナリオでは自身でAWSリソースを作ることが想定されますが、CloudGoatはCloudGoat自身がさくせしたリソースのみが管理対象となるため、`./cloudgoat destroy`コマンドを実行する前に自身のリソースは削除してください。

## 攻撃経路

![Scenario Route(s)](https://www.lucidchart.com/publicSegments/view/17beef30-c547-4d58-912c-9b9250ea6c82/image.png)

## ガイド - IAMユーザー "Kerrigan"

1. IAMユーザ"Kerrigan"として、アタッカーは限られた権限の中で環境を調査します。
2. アタッカーはまず、EC2インスタンスを一覧し、ターゲットである"cg-super-critical-security-server"を確認します。しかし直接変更がでd気ないので、アタッカーは別の方法を探します...
3. アタッカーはアカウント内にあるインスタンスプロファイルやロールを探し、使えそうなインスタンスプロファイルやロールを識別します。
4. 計画を練り、アタッカーは攻撃を開始します。まず、インスタンスプロファイルにフルアドミンロールをつけます。
5. 次に、アタッカーは新しいEC2キーペアを作成します。
6. そして、このアタr椎キーペアでEC2インスタンスを作り、シェルアクセスを手に入れます。
7. 攻撃の最終ステップとして、アタッカーはフルアドミン権限をもつインスタンスプロファイルをEC2インスタンスにアタッチします。
8. 新しいEC2インスタンスを踏み台とし、アタッカーはアタッチされたプロファイルロールのおかげでAWS CLIコマンドをフルアドミン権限で実行することができます。
9. 最終的にアタッカーはEC2インスタンス"cg-super-critical-security-server"をterminateさせ、シナリオの完了です。

この方法のチートシートは[こちら](./cheat_sheet_kerrigan.md).