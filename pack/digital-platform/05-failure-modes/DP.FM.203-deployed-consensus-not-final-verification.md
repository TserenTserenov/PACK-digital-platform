---
id: DP.FM.203
title: "Задеплоенный консенсус ≠ финал проверки: peer-review не заменяет эмпирическую проверку живых данных"
type: failure_mode
pack: PACK-digital-platform
domain: digital-platform / deployment-verification
epistemic_stage: confirmed
valid_from: 2026-07-06
source: "session-close 2026-07-03 (WP-455/457, sessions/2026-07/2026-07-03-17-wp455-critical-review-and-urgent-deploy.md §2)"
related:
  see_also: [DP.FM.186, DP.METHOD.101]
---

# DP.FM.203 — Задеплоенный консенсус ≠ финал проверки: peer-review не заменяет эмпирическую проверку живых данных

## Описание

Код прошёл peer-review, consensus достигнут, деплой выполнен успешно — агент считает задачу закрытой. Но ошибка, скрытая в edge-case (batch-режим vs единичная запись), не обнаруживается ни тестами, ни peer-review. Обнаруживается только при точечной проверке живых данных после деплоя.

## Пример

WP-455/457: peer-сессия разработала и согласовала миграции 275+276 с trigger-based immutable chain. Peer-review прошёл. Деплой в прод прошёл чисто. Живая проверка данных после деплоя обнаружила: бэкфилл (8 строк, `INSERT...VALUES` с несколькими строками) получил `prev_hash=NULL` — форк цепочки. Тест в peer-review был на одиночный INSERT, не на multi-row batch.

## Тест обнаружения

«После деплоя критичного механизма (immutable chain, consent enforcement, audit trail) — проверяли ли реальные данные в базе, а не только вывод COMMIT?» Нет → уязвимость к FM.203 присутствует.

## Инвариант

Peer-review + зелёные тесты + успешный COMMIT не гарантируют отсутствие data-corruption на живых данных при edge-case batch-операциях. Эмпирическая точечная проверка после деплоя — обязательный шаг, не опциональный.

## Митигация

1. После деплоя критичного механизма: выполнить точечный SQL-запрос на реальных данных (`SELECT ... WHERE prev_hash IS NULL LIMIT 10`)
2. При наличии batch-операций в бэкфилле: добавить explicit assertion (`check_no_forks()`) как post-deploy smoke
3. Чеклист: «тест был на одиночную запись? → дополнить тестом на batch»

## Связи

- DP.FM.186 (Append-only phantom early-writer) — смежный FM в той же системе
- DP.METHOD.101 (Append-only audit journal integrity) — методология, нарушение которой выявил этот FM
