---
id: DP.FM.199
title: "REVOKE на уровне роли недостаточен: владелец схемы обходит без аудита"
type: failure_mode
pack: PACK-digital-platform
domain: digital-platform / data-platform
epistemic_stage: confirmed
valid_from: 2026-07-05
source: "session-close 2026-07-03 (WP-457 Ф12 К1, ход 3)"
related:
  see_also: [DP.METHOD.121]
tags: [postgresql, rbac, schema-owner, revoke, trigger, immutable, security, audit]
wp: WP-457
---

# DP.FM.199 — REVOKE на уровне роли недостаточен: владелец схемы обходит без аудита

## Описание

Защита immutable-хранилища ограничена `REVOKE UPDATE/DELETE` у всех ролей, кроме владельца схемы. Failure mode: schema owner не ограничен REVOKE — может выполнить прямые UPDATE/DELETE в обход, без аудита и без исключения. Запись в «защищённом» хранилище молча изменена или удалена.

## Пример

WP-457 Ф12: при проектировании append-only immutable журнала первый слой защиты — REVOKE UPDATE/DELETE у всех ролей, кроме schema owner. Проверочный вопрос «может ли DBA или schema owner обойти без специальной административной функции?» — да, молча.

## Тест обнаружения

«Может ли DBA или schema owner выполнить прямой DELETE/UPDATE в обход всех ограничений, не оставив следа в audit log?» Да → один слой недостаточен.

## Инвариант

REVOKE защищает от непривилегированных ролей. Schema owner не ограничен REVOKE — ownership обходит любые REVOKE. Безусловный триггер — единственный слой, который срабатывает независимо от роли.

## Митигация

1. Второй слой: безусловный `BEFORE UPDATE OR DELETE` триггер на каждой таблице → всегда бросает EXCEPTION независимо от роли.
2. Исключение только через явную административную функцию с SESSION_LOCAL bypass (DP.METHOD.121).
3. Проверочный вопрос при проектировании: «schema owner может обойти без специальной функции?»

## Связи

- DP.METHOD.121 (admin-delete SESSION_LOCAL) — правильный escape hatch для легитимного удаления
- WP-457 Ф12 — первый прецедент
