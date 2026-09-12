# コンピューティングサービス料金比較

「同じくらいのコンピューティングリソース（2 vCPU / 4 GB 相当）を 1 ヶ月動かしたらいくらか」に揃えて、主要コンピューティングサービスの価格差を比較する。

---

## 前提条件

- リージョン: 東京 (ap-northeast-1)
- アーキテクチャ: ARM
- OS: Linux
- 購入オプション: オンデマンド
- 為替レート: 1 USD = 160 円、月間 730 時間
- スペックは 2 vCPU / 4 GB 相当
- 単価は 2026 年 9 月時点
- Lambda のリクエスト課金と無料枠、EC2 の EBS 代、AgentCore Runtime のストレージ代は含まない

---

## 1ヶ月あたりの料金比較（常時稼働換算）

| サービス | 月額の目安（円） | | EC2 比 | Fargate 比 |
|---|---:|:---|---:|---:|
| EC2 | 9,998 | ██████ | 1.00 倍 | 0.87 倍 |
| Fargate (ECS) | 11,514 | ███████ | 1.15 倍 | 1.00 倍 |
| Lambda | 22,412 | █████████████ | 2.24 倍 | 1.95 倍 |
| Bedrock AgentCore Runtime | 25,322 | ███████████████ | 2.53 倍 | 2.20 倍 |
| CodeBuild | 29,784 | ██████████████████ | 2.98 倍 | 2.59 倍 |

※ 常時稼働換算は比較用で、実際には Lambda・CodeBuild・AgentCore Runtime にはタイムアウト制限がある（詳細比較を参照）。

※ AgentCore Runtime は 2 vCPU / 4 GB を常時消費した場合の上限値。CPU は実消費分のみの課金で I/O 待ち中は課金されないため、待ち時間の割合が大きいエージェント処理では大幅に下がる。

---

## 詳細比較

| サービス | 比較対象 | スペック | 単価 (USD/時) | 課金単位 | タイムアウト |
|---|---|---|---:|---|---|
| EC2 | c6g.large | 2 vCPU / 4 GiB | 0.0856 | 秒（最低 60 秒） | なし |
| Fargate (ECS) | - | 2 vCPU / 4 GB | 0.09858 | 秒（最低 1 分） | なし |
| Lambda | メモリ 4 GB 設定 | 4 GB（CPU はメモリに比例して割当） | 0.19188 | 1 ミリ秒 | 最大 15 分 / 実行 |
| Bedrock AgentCore Runtime | - | 2 vCPU / 4 GB | 0.2168 | 秒（最低 1 秒） | 同期 15 分 / ストリーミング 60 分 / 非同期 8 時間 |
| CodeBuild | arm1.small | 2 vCPU / 3 GB | 0.255 | 分 | 最大 36 時間 / ビルド |

※ CodeBuild の `arm1.small` のメモリ容量は、[ビルド環境のコンピューティングモードおよびタイプ](https://docs.aws.amazon.com/ja_jp/codebuild/latest/userguide/build-env-ref-compute-types.html)およびマネジメントコンソールでは 4 GiB と記載されている。一方、[AWS CodeBuild の料金ページ](https://aws.amazon.com/codebuild/pricing/)では、OS などが使用する領域を除いた、実際にビルドで利用可能なメモリ容量として 3 GB と記載されている。

※ AgentCore Runtime はリソースを事前に選択せず、CPU は実消費分、メモリはその秒までのピーク消費分で課金される。単価は $0.0895 / vCPU 時間 + $0.00945 / GB 時間 なので、2 vCPU / 4 GB 相当の消費は `2 × 0.0895 + 4 × 0.00945 = 0.2168 USD/時`。

---

## 出典

- [Amazon EC2 料金](https://aws.amazon.com/ec2/pricing/)
- [AWS Fargate 料金](https://aws.amazon.com/fargate/pricing/)
- [AWS Lambda 料金](https://aws.amazon.com/lambda/pricing/)
- [AWS CodeBuild 料金](https://aws.amazon.com/codebuild/pricing/)
- [Amazon Bedrock AgentCore 料金](https://aws.amazon.com/bedrock/agentcore/pricing/)
- [Amazon Bedrock AgentCore のクォータ](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/bedrock-agentcore-limits.html)
