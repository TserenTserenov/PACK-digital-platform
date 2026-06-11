---
id: DP.M.301
name: "Sync source-of-truth → derived: edit-commit-push в SoT, derived read-only"
name_ru: "Синхронизация источник-истины → производная копия"
name_en: "Sync source-of-truth to derived workflow"
summary: "Две копии одного файла, синхронизируемые односторонне: правки только в источнике через commit перед sync, производная read-only — иначе sync затирает правки незакоммиченным состоянием."
type: method
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: formalized
category: sync-architecture
valid_from: 2026-06-09
related:
  see_also: [DP.M.104, DP.SC.035]
tags: [sync, source-of-truth, derived, read-only, content-sync, marathon-content, drift-prevention, owner-integrity]
source: "session 2026-06-09, commit 138a760 vs db243c0 (marathon IWE→ИИ-помощник sync) + memory/feedback_marathon_content_sync_workflow.md"
schema_version: 1
---

# DP.M.301 — Sync workflow для derived-файлов

## Описание

Архитектурный паттерн для двух копий одного контентного файла, синхронизируемых односторонне (sync-скрипт автор → потребитель): explicit «direction of truth», READ-ONLY маркер в derived, обязательный commit перед sync.

## Failure mode

Если правка сделана в обеих копиях одновременно (автор не закоммитил, кто-то правит копию-потребитель), последующий sync перезатирает правки потребителя незакоммиченным состоянием источника.

## Конкретный инцидент

9 июня 2026 sync (commit `138a760`) перезаписал правильные правки бота (commit `db243c0`: IWE→ИИ-помощник) незакоммиченным авторским файлом. Авторский файл без коммита = undefined state при sync.

## Правила

1. **Source-of-truth (авторский файл)** — единственное место правок.
2. **Derived (бот-копия)** — read-only, помечена маркером READ-ONLY в CLAUDE.md потребителя.
3. **Workflow:** edit → commit → push в авторском репо → sync-скрипт → commit+push derived. Sync без коммита источника блокируется pre-condition в скрипте или варнингом.

## Архитектурный маркер

В CLAUDE.md обеих сторон — explicit «direction of truth»:
- автор: «source-of-truth для X — этот файл; sync пушит в потребитель»
- потребитель: «READ-ONLY копия X из автора; правки сюда — потеряются при следующем sync»

## Применимость

Любые derived-файлы: marathon-content.json, config.json, generated-data, pre-rendered шаблоны, скопированные части документации.

## Отличие от соседей

- Различение из distinctions.md «Source-of-truth ≠ Производный» — про переиспользование ID, не про workflow sync.
- Capture 4654 (manifest coverage CI check) — про доставку новых файлов, не про синхронизацию изменений в уже доставленных derived.
- DP.M.104 (cross-repo-publication-pipeline) — про инициальную доставку, не про continuous sync.
