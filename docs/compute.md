# コンピューティングサービス料金比較

「同じくらいの計算リソース（2 vCPU / 4 GB 相当）を 1 時間動かしたらいくらか」に揃えて、主要コンピューティングサービスの価格差を比較する。

{% set ec2 = compute_services | selectattr("name", "equalto", "EC2") | first %}
{% set lambda_ = compute_services | selectattr("name", "equalto", "Lambda") | first %}

---

## 結論

常時稼働に換算すると、だいたいこの順で高くなる。

**EC2 （1.0）＜ Fargate（×{{ "{:.1f}".format(compute_services[1].x86_hr / ec2.x86_hr) }}）＜ Lambda（×{{ "{:.1f}".format(lambda_.x86_hr / ec2.x86_hr) }}）＜ CodeBuild（×{{ "{:.1f}".format(compute_services[3].x86_hr / ec2.x86_hr) }}）**

ただしこれは「1 か月動かし続けた場合」の話。単価が高いサービスほど課金の粒度が細かく、止まっている時間はタダになるので、稼働率が低いほど順位は逆転する（後述）。

---

## ぱっと見比較（x86・常時稼働換算）

| サービス | 月額の目安（円） | EC2 比 | |
|---|---:|---:|:---|
{% for s in compute_services -%}
{% set ratio = s.x86_hr / ec2.x86_hr -%}
| {{ s.name }} | {{ "{:,.0f}".format(s.x86_hr * hours_per_month * usd_jpy) }} | {{ "{:.2f}".format(ratio) }} 倍 | {{ "█" * ((ratio * 6) | round | int) }} |
{% endfor %}

---

## 詳細比較

| サービス | 比較対象 | スペック | x86 (USD/時) | ARM (USD/時) | 課金単位 | アイドル時 |
|---|---|---|---:|---:|---|---|
{% for s in compute_services -%}
| {{ s.name }} | {{ s.target }} | {{ s.spec }} | {{ "{:.5f}".format(s.x86_hr) }} | {{ "{:.5f}".format(s.arm_hr) }} | {{ s.billing }} | {{ s.idle }} |
{% endfor %}

!!! tip "ARM (Graviton) にすると"
    どのサービスでも同じ傾向で、**x86 より 15〜25% 安い**。相対的な順位は変わらない。

---

## 稼働率で順位が逆転する

{% set be_hours = ec2.x86_hr * hours_per_month / lambda_.x86_hr %}
単価の高さは「使った分しか払わない」ことの対価なので、常時動かさないなら話が変わる。

- Lambda の実行単価は EC2 の約 {{ "{:.1f}".format(lambda_.x86_hr / ec2.x86_hr) }} 倍。逆に言うと、**実行時間が月 {{ "{:,.0f}".format(be_hours) }} 時間（稼働率 {{ "{:.0f}".format(be_hours / hours_per_month * 100) }}%、1 日あたり約 {{ "{:.0f}".format(be_hours / 30.4) }} 時間）を下回るなら Lambda のほうが安い**。
- EC2 / Fargate はアイドル中も課金される。Lambda / CodeBuild は動いた分だけ。
- CodeBuild は常時稼働の選択肢ではなく「ビルドジョブの実行時間だけ」払うサービス。1 日に数十分しか回らない CI にサーバーを 1 台占有させるより安い、という文脈で見る。

---

## 単価差は何の値段か

上に行くほど、AWS に任せる範囲（＝自分でやらなくていい運用）が広い。

| サービス | 自分でやらなくていいこと |
|---|---|
| EC2 | ハードウェアの調達だけ |
| Fargate | ＋ OS・ホストのパッチ、AMI 管理、キャパシティ管理 |
| Lambda | ＋ スケーリング、リクエストルーティング、イベント統合 |
| CodeBuild | ＋ ビルド環境のプロビジョニングと破棄 |

つまり単価差 ≒ 運用の外注費。EC2 が一番安いのは「一番自分でやることが多い」から。

---

## 前提条件

- リージョン: 東京 (ap-northeast-1)、Linux、オンデマンド
- 為替レート: 1 USD = {{ usd_jpy }} 円、月間 {{ hours_per_month }} 時間
- スペックは 2 vCPU / 4 GB 相当に正規化（CodeBuild small は 2 vCPU / 3 GB、Lambda はメモリ 4 GB 設定 ≒ 2.2 vCPU 相当）
- 単価は 2026 年 7 月時点、AWS Price List API から取得
- Lambda のリクエスト課金（$0.20 / 100 万件）と無料枠、EC2 の EBS 代は含まない

!!! note "さらに安くする手段は別枠"
    ここは全部「定価（オンデマンド）」の比較。EC2 / Fargate はスポットで最大 7 割引、Savings Plans で 2〜5 割引になる。また EC2 には t3.medium（2 vCPU / 4 GB で $0.0544/時、ただしバースト型で CPU 常用は不可）のような変則枠もある。

---

## 出典

- [Amazon EC2 料金](https://aws.amazon.com/ec2/pricing/)
- [AWS Fargate 料金](https://aws.amazon.com/fargate/pricing/)
- [AWS Lambda 料金](https://aws.amazon.com/lambda/pricing/)
- [AWS CodeBuild 料金](https://aws.amazon.com/codebuild/pricing/)
