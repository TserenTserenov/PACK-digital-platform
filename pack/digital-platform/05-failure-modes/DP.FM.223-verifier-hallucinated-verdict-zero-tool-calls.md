---
id: DP.FM.223
type: failure-mode
name: Verifier Hallucinated Verdict with Zero Tool Calls
name_ru: Верификатор-субагент возвращает вердикт без единого вызова инструмента
pack: PACK-digital-platform
domain: digital-platform
status: active
epistemic_stage: established
valid_from: 2026-07-09
source: session-transcript 2026-07-06 (WP-149, commit 25172f415)
related:
  see_also: [DP.FM.015]
tags: [agent, verifier, hallucination, false-negative, tool-calls, zero-trace]
wp: WP-149
---

# DP.FM.223 — Верификатор-субагент возвращает вердикт без единого вызова инструмента

## Описание

Субагент-верификатор (R23 / Haiku / любой LLM-верификатор) завершает работу с чётким вердиктом (PASS/FAIL/structured result), но трейс задачи показывает нуль вызовов инструментов (Read, Grep, Bash, API).

**Опасность:** процесс успешно завершился, вернул структурированный результат — но вердикт фиктивный. Верификатор не наблюдал реального состояния системы.

## Отличие от «верификатор упал»

- **Верификатор упал** → процесс завершился с ошибкой → очевидный сигнал.
- **Верификатор галлюцинирует** → процесс успешно завершился, вернул вердикт → незаметный сигнал.

## Диагностический тест

«Sub-agent вернул вердикт — сколько tool calls было в его трейсе?»
- Ноль → вердикт недействителен, не доверять.
- N > 0 → вердикт может быть достоверным (проверить N ≥ минимально необходимому).

## Контрмера

Обязательный минимум вызовов инструментов (N ≥ threshold) как precondition для доверия вердикту верификатора. Логировать количество tool calls при каждом запуске верификатора. При нуле — пометить вердикт как «invalid: no observations» и повторить или эскалировать.

## Инцидент

WP-149 Quick Close: R23-верификатор (Haiku, context isolation) трижды возвращал вердикт FAIL без единого Read/grep — hallucinated на основе весов, без наблюдения файловой системы.

## Применимо

Любой pipeline с LLM-судьей или верификатором: code review bot, checklist checker, acceptance tester, compliance auditor.

## Связано

- DP.FM.015 (false-positive detection) — про ложно-положительные в другом контексте
