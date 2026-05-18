---
id: DP.FM.046
name: Render-queue timeout — отсутствующий deadline на вызов подзадачи
name_en: Render-queue timeout — missing callsite deadline in async worker
type: failure-mode
status: active
summary: "Задание зависает в очереди навсегда, потому что воркер ждёт ответа от подзадачи без явного timeout. Диагностика: open-sessions log. Признак: задание в статусе «выполняется» дольше expected_max."
created: 2026-05-18
valid_from: 2026-05-18
trust:
  F: 3
  G: domain
  R: 0.80
epistemic_stage: evidence
related:
  see_also: [feedback_sequential_worker_throughput_ceiling]
tags: [async, worker, queue, timeout, render, pipeline, hang]
wp: WP-309
---

# DP.FM.046: Render-queue timeout — отсутствующий deadline на вызов подзадачи

## Симптом

Задание в очереди находится в статусе «выполняется» значительно дольше ожидаемого максимального времени. Очередь не продвигается.

## Корневая причина

Воркер вызывает подзадачу (subprocess, HTTP-запрос, LLM completion) без явного `timeout` параметра. Подзадача не отвечает → воркер блокируется навсегда → задание не завершается → следующие задания в очереди не обрабатываются.

**Ошибочная интерпретация:** «задание зависло внутри себя» → ищут бесконечный цикл в логике задачи.
**Реальная причина:** воркер ждёт внешнего upstream без deadline.

## Диагностика

1. Открыть open-sessions log (или аналог): сколько времени задание в статусе «выполняется»?
2. Если > `expected_max` → timeout на уровне callsite (не на уровне задачи).
3. Найти все внешние вызовы воркера → проверить наличие explicit `timeout` в каждом.

## Fix

Явный `timeout` параметр на каждый внешний вызов внутри воркера:
- subprocess: `timeout=N`
- HTTP: `timeout=(connect, read)`
- LLM completion: `timeout=N` или `max_tokens` + `stop`

## Отличие от DP.FM.041 (throughput ceiling)

- Throughput ceiling: последовательный воркер ограничен физическим потолком скорости (~50-60 ev/min).
- DP.FM.045: один заблокированный вызов останавливает очередь независимо от потолка.
