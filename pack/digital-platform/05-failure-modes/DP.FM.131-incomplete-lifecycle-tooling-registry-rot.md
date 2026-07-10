---
id: DP.FM.131
title: Неполный lifecycle-инструментарий реестра артефактов (есть create, нет close)
type: failure-mode
pack: DP
tags: [registry, lifecycle, artifact, create-wp, close-wp, tooling-gap]
status: active
valid_from: 2026-06-04
schema_version: 1
---

# DP.FM.131 — Неполный lifecycle-инструментарий: registry rot

## Симптом

Реестр артефактов (WP-REGISTRY, packages, issues) накапливает записи в неверных статусах: неактивные записи не закрыты, переходы не задокументированы, линтер не помогает.

## Причина

**Двойной корень:**
1. `create-wp.sh` создаёт запись, но не создаёт структуру под финальные артефакты (`archive/wp-contexts/`) — при закрытии некуда класть итоговые заметки.
2. Нет `close-wp.sh` — агенты не знают правильной последовательности закрытия (статус, перемещение, обновление реестра).

**Дополнительный усилитель:** если разные агенты работают с одним реестром (Claude + Kimi), расхождение в правилах (CLAUDE.md vs AGENTS.md) гарантирует drift.

## Генерализация

> **Любой реестр гниёт при неполном lifecycle-инструментарии:**
> инструмент создания без инструмента закрытия = накопление мусора.

Паттерн применим к: WP-REGISTRY, issue trackers, package registries, service catalogs.

## Fix

1. Создать `close-wp.sh`: статус → done, перемещение в archive, обновление REGISTRY строки.
2. Доработать `create-wp.sh`: создавать `archive/wp-contexts/WP-NNN.md` сразу при создании РП.
3. Канонизировать правило закрытия в точке входа для всех агентов (AGENTS.md, не только CLAUDE.md).

## Связи

- WP-7 peer-session 33 (2026-06-04): диагностика + реализация close-wp.sh
- DP.M.269: bidirectional registry drift guard (охраняет реестр от drift)

## Источник

session-transcript 2026-06-04 (WP-7 peer-session 33); git diff DS-my-strategy (5053e7834, close-wp.sh, check-wp-format.py)
