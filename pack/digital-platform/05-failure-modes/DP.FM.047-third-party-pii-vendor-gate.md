---
id: DP.FM.047
title: Third-Party LLM PII Vendor Gate
kind: FM
status: draft
trust: empirical
epistemic_stage: pattern
created: 2026-05-18
valid_from: 2026-05-18
sources:
  - session: 2026-05-18
  - wp: WP-316
  - commit: 95a0613d
related:
  - DP.FM.038  # PII-related patterns
---

# DP.FM.047 — Third-Party LLM PII Vendor Gate

## Описание

При интеграции third-party LLM-сервиса (memory provider, inference API) для обработки PII-контента пользователя — production deployment блокируется требованием vendor-level подтверждения, которое не виден на этапе планирования.

## Симптом

Исследовательская и инженерная фазы (Ф1–Ф8.2) завершены на синтетике/локально, но production-фазы заморожены: вендор не предоставил публичную Privacy Policy, data processing agreement или явный ответ об обработке пользовательских данных.

**Прецедент:** WP-316 (Honcho memory provider) — Ф8.3+Ф9 заблокированы до закрытия issue #699.

## Механизм

B7.3 PII Gate требует двух независимых подтверждений:
1. **Внутренний ArchGate §Б** — оценка угроз со стороны команды (может быть пройден до production)
2. **Vendor-level confirmation** — публичная PP / DPA / явный ответ на вопрос о данных пользователей

Research-фаза обходит пункт 2 через синтетические данные. Production-фаза не обходится.

## Тест применимости

«Third-party SaaS/API обрабатывает тексты, содержащие или способные содержать PII пользователя?» → Да → зафиксировать vendor-confirmation как явную dependency в плане фаз ДО начала Ф-prod.

## Профилактика

При WP Gate на интеграцию third-party LLM-компонента:
- Добавить шаг «Vendor PII confirmation» как блокирующую зависимость production-фазы
- Оценить публичность PP вендора ДО начала инженерных фаз
- Если PP отсутствует → завести issue в репо вендора и зафиксировать как dependency в WP-context

## Отличие от смежных паттернов

- **Synthetic data gate (2026-05-17)** — как пройти research-фазу; этот FM — блокер production-фазы, не обходимый синтетикой
- **B7.3 PII Gate internal** — оценивается командой; vendor gate — внешняя зависимость
