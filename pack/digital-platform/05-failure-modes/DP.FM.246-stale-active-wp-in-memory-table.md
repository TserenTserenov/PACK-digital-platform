---
id: DP.FM.246
title: "Закрытый РП в таблице «Текущая работа» до явной синхронизации"
name_ru: "Stale active WP persists in MEMORY.md active table"
name_en: "Closed work product hangs in active work table until explicit sync"
summary: "Quick Close не включает шага обновления MEMORY.md — закрытый РП остаётся в таблице «Текущая работа» как активный до следующего явного протокола."
type: failure-mode
pack: PACK-digital-platform
domain: digital-platform / agent-memory
severity: minor
trust: confirmed
epistemic_stage: confirmed
valid_from: 2026-07-02
related:
  see_also: [DP.FM.073]
source: "WP-450 handoff de08c646a, sub-agent обнаружил WP-403, session-transcript 2026-07-02"
---

# DP.FM.246 — Закрытый РП зависает в таблице «Текущая работа» до явной синхронизации

## Суть паттерна

Закрытие РП в середине сессии (Quick Close) не включает обязательного шага обновления
MEMORY.md. Протоколы Day Close и Week Close обновляют MEMORY.md, но Quick Close — нет.
В результате закрытый РП остаётся в таблице «Текущая работа» со статусом 🔄 до:
- следующего Day Close / Week Close
- явного ручного обновления
- обнаружения sub-agent'ом при синхронизации контекста

## Механизм

1. РП закрывается в середине сессии (Quick Close: коммит + push)
2. MEMORY.md не обновляется — нет этого шага в Quick Close
3. В начале следующей сессии агент читает MEMORY.md → воспринимает закрытый РП как активный
4. Sub-agent или пилот замечает расхождение при явной синхронизации контекста

## Последствие

Агент может принимать закрытый РП за активный контекст → риск ненужной работы по уже
закрытому РП; неточная картина WIP.

## Митигация

Quick Close добавить явный шаг: «проверить MEMORY.md таблицу "Текущая работа" — убрать
строки закрытых РП (статус done + сессия закрыта)».

## Связи

- [DP.FM.073](DP.FM.073-protocol-coverage-gap-mentioned-not-enforced.md) — аналогичный паттерн: артефакт зависает в состоянии из-за неполного протокола
