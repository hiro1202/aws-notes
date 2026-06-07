# Fargate 料金早見表

---

## 前提条件

- リージョン: 東京 (ap-northeast-1)
- 為替レート: 1 USD = {{ usd_jpy }} 円
- 月間稼働時間: {{ hours_per_month }} 時間

!!! note
    金額は 1 タスクを 1 か月（{{ hours_per_month }} 時間）連続稼働させた場合の概算です。月額（円）は丸め前の USD から計算しています。セルは月額（円）。

---

## Fargate 単価（2026年6月現在）

| アーキテクチャ | vCPU (USD/時) | メモリ (USD/GB/時) |
|---|---|---|
| x86 | {{ fargate.x86.vcpu_hr }} | {{ fargate.x86.gb_hr }} |
| ARM | {{ fargate.arm.vcpu_hr }} | {{ fargate.arm.gb_hr }} |

出典: [AWS Fargate 料金](https://aws.amazon.com/fargate/pricing/)（最新の単価はこちらで確認）

---

## 設定可能な vCPU / メモリの組み合わせ

| vCPU | メモリ | 刻み |
|---|---|---|
{% for s in fargate_specs -%}
{% if s.mem -%}
| {{ s.vcpu }} | {{ s.mem | join(", ") }} GB | 個別 |
{% else -%}
| {{ s.vcpu }} | {{ s.min }} 〜 {{ s.max }} GB | {{ s.step }} GB |
{% endif -%}
{% endfor %}

{# 行=vCPU, 列=メモリGB, セル=月額円 #}
{% macro matrix_table(price) -%}
{% set cols = [] -%}
{% for s in fargate_specs -%}
{% set mems = s.mem if s.mem else range(s.min, s.max + 1, s.step) | list -%}
{% for m in mems -%}{% if m not in cols %}{% set _ = cols.append(m) %}{% endif %}{% endfor -%}
{% endfor -%}
{% set cols = cols | sort -%}
| vCPU ＼ メモリGB |{% for c in cols %} {{ c }} |{% endfor %}
|---|{% for c in cols %}---:|{% endfor %}
{% for s in fargate_specs -%}
{% set mems = s.mem if s.mem else range(s.min, s.max + 1, s.step) | list -%}
| {{ s.vcpu }} |{% for c in cols %}{% if c in mems %}{% set usd = (s.vcpu * price.vcpu_hr + c * price.gb_hr) * hours_per_month %} {{ "{:,.0f}".format(usd * usd_jpy) }} |{% else %} — |{% endif %}{% endfor %}
{% endfor %}
{%- endmacro %}

---

## Linux / x86

{{ matrix_table(fargate.x86) }}

---

## Linux / ARM

{{ matrix_table(fargate.arm) }}
