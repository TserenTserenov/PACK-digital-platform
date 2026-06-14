---
id: DP.FM.157
title: "Cloud scheduler backs up wrong CLAUDE.md in layered topology"
name_ru: "Облачный планировщик бэкапирует не тот уровень CLAUDE.md в многоуровневой топологии"
name_en: "Cloud backup overwrites workspace-root CLAUDE.md slot with governance layer"
summary: "В многоуровневой топологии IWE облачный бэкап имеет доступ только к одному уровню (governance), но пишет в слот другого уровня (workspace-root) — каждый день правильный локальный бэкап перезаписывается неправильным."
type: failure-mode
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: 3
valid_from: 2026-06-13
related:
  see_also: [DP.D.137]
source: "git diff FMT-exocortex-template/.github/workflows/cloud-scheduler.yml, 2026-06-13 (commit 158b31b, PR #172)"
---

# DP.FM.157 — Cloud backup overwrites wrong CLAUDE.md layer

## Краткое описание

Облачный планировщик (GitHub Actions) бэкапирует тот CLAUDE.md, к которому имеет доступ из своей среды (governance-репо), в слот другого семантического уровня (workspace-root). Ежесуточный облачный бэкап отменяет правильный локальный бэкап.

## Симптомы

- Восстановление на новой машине (iwe-restore) разворачивает неожиданное поведение агента.
- `exocortex/CLAUDE.md` содержит governance-контент вместо workspace-root инструкций.
- Локальный day-close.sh делает правильный бэкап, но к следующему утру он перезаписан облачным.

## Контекст

В топологии IWE два CLAUDE.md:
1. **Workspace-root** (`~/IWE/CLAUDE.md`) — инструкции агента для сессии; бэкапируется в `exocortex/CLAUDE.md` через day-close.sh.
2. **Governance** (`DS-my-strategy/CLAUDE.md`) — инструкции для стратегического репо; версионируется в git.

Облачный планировщик имеет доступ только к governance-репо → бэкапирует governance CLAUDE.md в `exocortex/CLAUDE.md`.

## Механика

Среда GitHub Actions клонирует только DS-my-strategy (governance). workspace-root CLAUDE.md (~IWE/CLAUDE.md) недоступен. Скрипт бэкапа не знает о разнице уровней → копирует доступный файл в «правильный» слот.

## Решение

Скрипты бэкапа обязаны явно объявлять: (1) какой уровень топологии доступен из среды выполнения; (2) какой слот они заполняют; (3) несоответствие = блок. «Копировать CLAUDE.md» — неоднозначно; нужно «копировать workspace-root CLAUDE.md в слот X».

## Связи

- DP.D.137 (exocortex/CLAUDE.md slot semantics) — определение семантики слота
- FMT-exocortex-template commit 158b31b — fix
