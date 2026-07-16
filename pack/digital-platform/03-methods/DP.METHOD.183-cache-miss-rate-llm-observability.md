---
id: DP.METHOD.183
name: "Cache miss rate как первичная метрика наблюдаемости LLM-системы с prompt caching"
type: method
pack: PACK-digital-platform
domain: digital-platform / observability
kind: Method
status: active
created: 2026-07-14
trust: confirmed
epistemic_stage: observed
valid_from: 2026-07-13
sources:
  - "session-close 2026-07-13, WP-7 GTW6 (token cache miss metric + Grafana alert)"
related:
  see_also: [DP.FM.236]
schema_version: 1
---

# DP.METHOD.183 — Cache miss rate как метрика наблюдаемости LLM с prompt caching

## Определение

Для LLM-систем с prompt caching cache miss rate — первичный индикатор деградации кеша.
Без этой метрики система деградирует незаметно: работает, но токены и стоимость растут.

## IPO

- **Вход:** usage-данные LLM API (Anthropic: `cache_read_input_tokens`, `input_tokens`)
- **Процесс:** вычисление miss rate; alert при превышении порога
- **Выход:** сигнал о деградации кеша до заметного роста стоимости

## Формула
