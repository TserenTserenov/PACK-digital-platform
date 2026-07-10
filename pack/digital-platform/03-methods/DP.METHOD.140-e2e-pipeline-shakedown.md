---
id: DP.METHOD.140
type: method
pack: PACK-digital-platform
domain: digital-platform / delivery-testing
trust: draft
epistemic_stage: empirical
valid_from: 2026-07-09
source: "session-close 2026-07-09 (WP-415 pipeline shakedown, commit 3fc337790)"
schema_version: 1
related:
  see_also: [DP.M.104, DP.METHOD.139]
---

# DP.METHOD.140 — E2E Pipeline Shakedown на живом репо

## Описание

Перед объявлением pipeline «рабочим» — выполнить один полный E2E прогон с реальными данными и реальными remote'ами. Shakedown выявляет классы ошибок, которые unit-тесты не обнаруживают, поскольку CI работает без реального git-remote и без cross-repo checks.

Отличие от smoke-test: smoke = «запускается без exception»; shakedown = «производит правильный артефакт с правильными атрибутами на всех downstream».

## IPO

**Input:** pipeline, unit-тесты прошли, staging подготовлен

**Process:**
1. Выполнить один живой E2E прогон с реальными данными и реальными remote'ами
2. Проверить downstream-атрибуты: git log публичного репо, авторство коммитов, статус в DS-репо
3. Зафиксировать все обнаруженные баги
4. Починить баги, повторить прогон
5. Только после «чистого» shakedown — объявить pipeline рабочим

**Output:** pipeline с подтверждёнными downstream-атрибутами + список исправленных багов

## Тест готовности

«Можно проверить git log публичного репо глазами и убедиться: правильный автор, правильный контент, правильный статус?» Да → shakedown пройден.

## Антипаттерны

- Считать CI-зелёный подтверждением E2E корректности — CI без реального remote не ловит cross-repo баги
- Запускать shakedown после анонса «pipeline готов» — shakedown должен быть gate, не post-factum

## Пример

WP-415: первый E2E прогон iwesys-конвейера выявил 2 бага: attribution bug (коммиты атрибутировались боту) и publication bug (контент публиковался, но статус не фиксировался в DS-Tseren-Brand). Оба прошли бы незамеченными без живого прогона.
