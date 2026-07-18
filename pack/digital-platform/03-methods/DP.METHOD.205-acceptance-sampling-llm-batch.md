---
id: DP.METHOD.205
name: Acceptance Sampling для LLM Batch Output (30 проб, порог 90%)
type: method
status: draft
domain: llm-quality-gates
created: 2026-07-18
valid_from: 2026-06-27
tags: [llm, batch, quality-gate, sampling, acceptance-testing]
source: "session-transcript 2026-06-26 WP-439 migration+review + report + commit 2e8bc2aaf; extraction-report 2026-06-27-inbox-check-5 #2"
schema_version: 1
---

# [DP.METHOD.205] Acceptance Sampling для LLM Batch Output

## Суть метода

При LLM-генерации большого батча (N > 100) проверять качество через acceptance sampling (30 случайных элементов), а не через полный ревью или слепой bulk-accept.

## Алгоритм (IPO)

**Вход:** батч из N LLM-сгенерированных элементов (переводы, классификации, документы).

**Процесс:**
1. Выбрать 30 случайных элементов из N (без замены).
2. Оценить каждый через внешний LLM (Sonnet или human review).
3. Подсчитать acceptance rate = accepted / 30.
4. Если acceptance rate ≥ 90% → bulk-accept остаток.
5. Если acceptance rate < 90% → ручная правка или переделать весь батч.

**Выход:** решение bulk-accept / manual-fix + список проблемных элементов из sample.

## Обоснование выбора

| Альтернатива | Недостаток |
|---|---|
| Self-review | Смещение: агент одобряет собственный вывод |
| Blind bulk-accept | Нет gate, ошибки уходят в production |
| Full manual review | O(N) — нецелесообразно при N > 100 |
| **Acceptance sampling** | O(30) — дешевле, статистически представителен |

## Пример применения

WP-439: 446 LLM-переводов названий понятий графа на EN. Sampling 30 → 26/30 приняты (86.7% < порога 90%). Три исправлены вручную, остаток bulk-accepted.

## Ограничения

- N < 30 → sampling нецелесообразен, проверяй все элементы.
- Если ошибки кластеризованы (не случайны) → увеличить sample до 60+.
- Порог 90% эмпирический — калибровать под конкретную задачу.

## Применимость

LLM batch translation, auto-generated docs, classification labels — любой batch output, где полный ручной ревью нецелесообразен.
