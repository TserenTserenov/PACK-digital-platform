---
id: DP.M.201
name: Separate API Keys per Workload (изоляция квот по рабочим нагрузкам)
type: method
domain: digital-platform
status: active
trust: confirmed
epistemic_stage: 3
valid_from: 2026-05-28
sources:
  - "WP-358 Ф10 peer-сессия (2026-05-28), session-transcript 2026-05-28-08"
---

# DP.M.201: Separate API Keys per Workload

## Назначение

Изолировать rate-limit квоты между независимыми workload-ами через отдельные API credentials вместо reactive spend-alert мониторинга.

## Входы

- Несколько процессов, использующих один внешний rate-limited API
- Симптом: фоновый runner истощает квоту интерактивных сессий (или наоборот)

## Шаги

1. Идентифицировать workload-ы с конкурирующим потреблением квоты
2. Создать отдельный workspace/проект у провайдера для каждого workload
3. Ввести переменные `API_KEY_{WORKLOAD}` (напр. `ANTHROPIC_API_KEY_MARATHON`, `ANTHROPIC_API_KEY_SESSION`) с fallback к дефолтному ключу
4. Spend-мониторинг оставить как вторичный инструмент (не замена изоляции)

## Выходы

- Квоты изолированы: один workload не блокирует другой
- Раздельный billing и мониторинг по workload

## Применимость

Любые rate-limited API: Anthropic, OpenAI, Google, Stripe, Twilio и др.

**Тест:** «Могут ли два независимых процесса исчерпать квоту друг друга?» Да → разделить ключи.

## Антипаттерн

Spend-alert monitoring как замена изоляции: алертит постфактум (квота уже исчерпана), не изолирует заранее. Парсинг dashboard = хрупкое решение.
