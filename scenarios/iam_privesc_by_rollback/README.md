# シナリオ: iam_privesc_by_rollback

**サイズ:** 小

**難しさ:** 簡単

**コマンド:** `$ ./cloudgoat.py create iam_privesc_by_rollback`

## シナリオリソース

* 1 IAM User
  * 5 policy versions

## 最初の情報

1. IAMユーザー "Raynor"

## シナリオの目的

フルアドミン権限の取得

## 要約

アタッカーは厳しく制限されたIAMユーザーを使いますが、以前のIAMポリシーバージョンの確認やフル管理者権限をリストアすることで、特権エスカレーションが可能となります。

## 攻撃経路

![Scenario Route(s)](https://www.lucidchart.com/publicSegments/view/acef779c-51ce-4582-b4d2-19ae92b7f170/image.png)

## ガイド - IAMユーザ "Raynor"

1. IAMユーザーの"Raynor"としてのアタッカーはごく限られた、害のなさそうな権限のみがあります。
2. アタッカーはRaynorの権限を調査し、SetDefaultPolicyVersion権限があることを発見し、古いバージョンをデフォルトとすることで過去の4バージョンのポリシーにアクセスできるようになります。
3. 過去のポリシーバージョンを調査すると、あるバージョンにフルアドミン権限があることを発見します。
4. アタッカーはフルアドミンの権限があるポリシーを復元し、全てを可能にするフルアドミン権限を取得します。
5. 最後に、アタッカーはRaynorのポリシーバージョンを元に戻し、行動を隠します。

この方法のチートシートは[こちら](./cheat_sheet_raynor.md).