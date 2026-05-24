---
id: DP.M.158
title: ArchGate Defer-Pattern — управление ⚠️ через defer-map
kind: method
domain: architecture-governance
epistemic_stage: empirical
trust: empirical
created: 2026-05-22
valid_from: 2026-05-22
sources:
  - DS-my-strategy commit 5fdbf61f (archgate(WP-224): Ф1 ПРОХОДИТ)
  - WP-224 Ф1 ArchGate прогон 2026-05-22
---

# DP.M.158 — ArchGate Defer-Pattern: управление ⚠️ через defer-map

## Принцип

ArchGate conjunctive screening по ЭМОГССБ: PASS если ни одна критическая характеристика не ❌. Предупреждения (⚠️) не блокируют — фиксируются в defer-map с явным target.

## Два типа defer при ⚠️

| Тип | Описание | Пример |
|-----|---------|--------|
| **defer-internal** | Исправляется в текущем РП | ⚠️О: Owner Role External → строка distinctions.md при Ф5 |
| **defer-external** | Выносится в отдельный РП | ⚠️С: BC Map VR → WP-225 |

## Алгоритм

1. Прогнать ArchGate → профиль ЭМОГССБ
2. Для каждой ⚠️ определить тип: internal или external
3. Для каждого defer указать явный target (`phase=Ф5` или `wp=WP-NNN`)
4. Commit message: `archgate(WP-N): Ф1 ПРОХОДИТ + разблокированные артефакты`

## Условие PASS

Для всех критических характеристик: не ❌ → blocker = 0 → PASS (с defer-map для всех ⚠️)

## Формат записи результата
