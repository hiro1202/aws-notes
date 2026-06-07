# Fargate 料金早見表

リージョン: **{{ region }}** / 条件: Linux

基準値（変更は `mkdocs.yml` の `extra:` で一括）:

- 為替レート: **1 USD = {{ usd_jpy }} 円**
- 月間稼働時間: **{{ hours_per_month }} 時間**
- x86: vCPU **{{ fargate.x86.vcpu_hr }}** USD/時、メモリ **{{ fargate.x86.gb_hr }}** USD/GB/時
- ARM: vCPU **{{ fargate.arm.vcpu_hr }}** USD/時、メモリ **{{ fargate.arm.gb_hr }}** USD/GB/時

!!! note
    金額は **1 タスクを 1 か月（{{ hours_per_month }} 時間）連続稼働**させた場合の概算です。CPU/メモリ列の USD は表示上 2 桁に丸めていますが、円換算は丸め前の値で計算しています。

{% macro fargate_table(price) -%}
| vCPU (ユニット) | メモリ (GB) | vCPU 料金 (USD) | メモリ料金 (USD) | 合計 (USD) | 月額 (円) |
|---|---|---|---|---|---|
{% for cpu, mem in fargate_sizes -%}
{% set vcpu = cpu / 1024 -%}
{% set gb = mem / 1024 -%}
{% set cpu_usd = vcpu * price.vcpu_hr * hours_per_month -%}
{% set mem_usd = gb * price.gb_hr * hours_per_month -%}
{% set total = cpu_usd + mem_usd -%}
| {{ vcpu }} ({{ cpu }}) | {{ gb }} ({{ mem }} MB) | {{ "%.2f"|format(cpu_usd) }} | {{ "%.2f"|format(mem_usd) }} | {{ "%.2f"|format(total) }} | {{ "{:,.0f}".format(total * usd_jpy) }} |
{% endfor %}
{%- endmacro %}

## Linux / x86

{{ fargate_table(fargate.x86) }}

## Linux / ARM

{{ fargate_table(fargate.arm) }}
