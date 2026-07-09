# コンピューティングサービス料金比較

「同じくらいのコンピューティングリソース（2 vCPU / 4 GB 相当）を 1 ヶ月動かしたらいくらか」に揃えて、主要コンピューティングサービスの価格差を比較する。

---

## 結論

常時稼働に換算すると、だいたいこの順で高くなる。

**EC2 （1.0）＜ Fargate（×1.1）＜ Lambda（×2.1）＜ CodeBuild（×2.8）**

ただしこれは「1 か月動かし続けた場合」の話。単価が高いサービスほど課金の粒度が細かく、止まっている時間は課金されないので、稼働率が低いほど順位は逆転する。

---

## ぱっと見比較（常時稼働換算）

倍率とバーは **EC2 = 1.00** 基準。

| サービス | 月額の目安（円） | EC2 比 | |
|---|---:|---:|:---|
| EC2 | 10,629 | 1.00 倍 | ██████ |
| Fargate (ECS) | 11,514 | 1.08 倍 | ██████ |
| Lambda | 22,426 | 2.11 倍 | █████████████ |
| CodeBuild | 29,784 | 2.80 倍 | █████████████████ |

---

## 詳細比較

| サービス | 比較対象 | スペック | 単価 (USD/時) |
|---|---|---|---:|
| EC2 | c7g.large | 2 vCPU / 4 GB | 0.0856 |
| Fargate (ECS) | 2 vCPU / 4 GB タスク | 2 vCPU / 4 GB | 0.09858 |
| Lambda | メモリ 4 GB 設定 | 4 GB（CPU は比例配分 ≒ 2.2 vCPU） | 0.19200 |
| CodeBuild | arm1.small | 2 vCPU / 3 GB | 0.255 |

---

## 前提条件

- リージョン: 東京 (ap-northeast-1)
- アーキテクチャ: ARM
- OS: Linux
- 購入オプション: オンデマンド
- 為替レート: 1 USD = 160 円、月間 730 時間
- スペックは 2 vCPU / 4 GB 相当
- 単価は 2026 年 7 月時点
- Lambda のリクエスト課金と無料枠、EC2 の EBS 代は含まない

---

## 出典

- [Amazon EC2 料金](https://aws.amazon.com/ec2/pricing/)
- [AWS Fargate 料金](https://aws.amazon.com/fargate/pricing/)
- [AWS Lambda 料金](https://aws.amazon.com/lambda/pricing/)
- [AWS CodeBuild 料金](https://aws.amazon.com/codebuild/pricing/)
