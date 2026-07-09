# コンピューティングサービス料金比較

「同じくらいのコンピューティングリソース（2 vCPU / 4 GB 相当）を 1 ヶ月動かしたらいくらか」に揃えて、主要コンピューティングサービスの価格差を比較する。

---

## 1ヶ月あたりの料金比較（常時稼働換算）

倍率とバーは **EC2 = 1.00** 基準。

| サービス | 月額の目安（円） | EC2 比 | |
|---|---:|---:|:---|
| EC2 | 9,998 | 1.00 倍 | ██████ |
| Fargate (ECS) | 11,514 | 1.15 倍 | ███████ |
| Lambda | 22,412 | 2.24 倍 | █████████████ |
| CodeBuild | 29,784 | 2.98 倍 | ██████████████████ |

---

## 詳細比較

| サービス | 比較対象 | スペック | 単価 (USD/時) |
|---|---|---|---:|
| EC2 | c7g.large | 2 vCPU / 4 GiB | 0.0856 |
| Fargate (ECS) | - | 2 vCPU / 4 GB | 0.09858 |
| Lambda | メモリ 4 GB 設定 | 4 GB（CPU はメモリに比例して割り当てられる） | 0.19188 |
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
