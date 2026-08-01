---
id: DP.M.173
name: "Artifact-first контракт agentic-роли с confidence-полем"
type: method
pack: PACK-digital-platform
domain: agentic-roles
trust: 0.80
epistemic_stage: established
valid_from: 2026-05-25
source: session-close-feed 2026-05-25 (WP-350 commit 82e9244 — DP.ROLE.058 + DP.SC.160)
---

# DP.M.173 — Artifact-first контракт agentic-роли с confidence-полем

## Суть

Контракт agentic-роли с LLM-вызовом должен возвращать **structured artifact** (не свободный текст) + **confidence-поле** + **short-circuit guards** перед LLM. Вызывающая сторона использует confidence для решения «принимать автоматически или эскалировать пилоту». Не подменяет gate-логику пилота — структурирует приём.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Автоматический passthrough (экономия внимания пилота) ↔ риск необнаруженной ошибки классификации | Confidence-поле удерживает границу автоматизации: `high` → passthrough к исполнителю, `low` → эскалация пилоту с показом structured-вывода; без него выбор «доверять или проверять» ложился бы на человека для каждого ответа, сводя выгоду роли к нулю |
| Строгость схемы (валидатор отвергает несоответствие) ↔ устойчивость к невалидному выходу LLM | Фиксированная schema даёт downstream-обработку, но ломается о свободный текст; метод удерживает баланс через retry один раз → fallback на `confidence=low` + raw text — сбой парсера не роняет pipeline, а понижает доверие к конкретному ответу |

## IPO

| | |
|---|---|
| **Вход** | Сырой запрос пилота (текст, command, voice-transcript) |
| **Процесс** | Pre-LLM guards → LLM-классификация → structured-output validation |
| **Выход** | `{task_type, class, artifact, budget_estimate, confidence}` ИЛИ маркер short-circuit (`INSUFFICIENT_INPUT`, `OUT_OF_SCOPE`) |

## Три обязательные составляющие

1. **Structured output, не свободный текст.** Schema фиксирована, валидатор отвергает несоответствие. Свободный текст ломает downstream-обработку.
2. **Confidence-поле** (low/medium/high или [0.0; 1.0]). Считается LLM-ом или derived из правил (например: ввод <10 слов → low). Вызывающая сторона принимает решение на его основе.
3. **Short-circuit guards перед LLM-вызовом.** Тривиальные кейсы (пустой ввод, <N слов, явные стоп-слова) → return marker без LLM. Экономия токенов + детерминизм.

## Алгоритм вызова

1. **Guard-фаза** (детерминистическая): длина, формат, blacklist → если match → return short-circuit marker.
2. **LLM-фаза:** структурированный prompt, output-schema enforced (function calling / JSON mode / response_format).
3. **Validation:** parser проверяет схему. Сбой → retry один раз → fallback на confidence=low + raw text.
4. **Return** к вызывающей роли. **Не** вызывать исполнителя самостоятельно (см. DP.D.097 — loop control).

## Решение вызывающей стороны по confidence

| Confidence | Действие |
|------------|----------|
| `high` | Автоматический passthrough к исполнителю |
| `medium` | Логирование + passthrough (или review-flag в очередь) |
| `low` | Эскалация пилоту с показом structured-вывода |
| `INSUFFICIENT_INPUT` | Запрос уточнения у пилота, без LLM-pipeline |

## Тест применимости

«Может ли вызывающая роль автоматически решить — доверять ответу или эскалировать?» Нет confidence → не паттерн. Если ответ всегда требует ручной проверки → confidence-поле избыточно, паттерн не применим.

## Примеры применения

- DP.ROLE.058 (Артефактор-Постановщик) — `INSUFFICIENT_INPUT` <5 слов + confidence для эскалации к Маршрутизатору.
- Любой LLM-классификатор сырого ввода в очередь обработки.
- Knowledge-Extractor R2 — `accept/reject/defer` + confidence.

## Связи

- **Применяется в:** DP.ROLE.058 (Артефактор-Постановщик) как specific instance.
- **Соблюдает:** DP.D.097 (Loop control у вызывающей роли) — Артефактор возвращается в Маршрутизатор, не вызывает исполнителя сам.
- **Дополняет:** DP.SC.160 (Service Clause Артефактора) — контракт обещания.

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| Значение confidence читается как факт, а не как оценка | Внимание фиксируется на пороге passthrough (`high` → автоматически), а происхождение самого значения — LLM-самооценка или derived-правило вроде «ввод <10 слов → low» — и его калибровка остаются вне поля зрения: вызывающая сторона автоматизирует решение на непроверенном сигнале |
| LLM-фаза затмевает guard-фазу | Практик отлаживает prompt и output-schema, а детерминистические short-circuit guards (пустой ввод, <N слов, стоп-слова) дописываются «потом» — хотя именно они дают экономию токенов и детерминизм на тривиальных кейсах и стоят в алгоритме первым шагом |

---

> 2026-08-01 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 5). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
