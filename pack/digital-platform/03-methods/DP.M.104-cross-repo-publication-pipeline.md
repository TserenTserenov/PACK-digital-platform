---
id: DP.M.104
name: Cross-repo publication pipeline via workflow_dispatch + PR gate
name_ru: Кросс-репо pipeline публикации через workflow_dispatch + PR-гейт
name_en: Cross-Repo Publication Pipeline via workflow_dispatch + PR Gate
type: method
status: emerging
summary: "Человеко-инициируемый кросс-репо pipeline: content-repo → publication-repo через параметризованный workflow_dispatch (guide_id, version) → генерация артефактов по шаблону → gh pr create в целевом репо. PR-гейт обеспечивает editorial review перед слиянием в публичное дерево. Применим для любого паттерна «источник контента → публичная витрина»."
created: 2026-05-19
trust:
  F: 2
  G: domain
  R: 0.80
epistemic_stage: emerging
related:
  uses: []
  complements: [DP.M.079]
  realized_by: [build-skeleton.yml, DS-principles-curriculum]
tags: [cross-repo, pipeline, publication, pr-gate, editorial, workflow_dispatch, github-actions]
wp: WP-322
sources:
  - session-transcript 2026-05-19
  - git diff DS-principles-curriculum commit e1af009
---

# DP.M.104 — Кросс-репо pipeline публикации через workflow_dispatch + PR-гейт

## Контекст

Когда контент создаётся в одном репо (source-of-content), а публикуется в другом (publication-repo), прямой push нарушает editorial control: ошибки попадают напрямую в публичное дерево. Нужен механизм с промежуточной проверкой.

## Суть метода

1. **Триггер:** `workflow_dispatch` с явными входными параметрами (guide_id, version, ...) — человек явно инициирует публикацию.
2. **Генерация:** workflow создаёт артефакты (skeleton-файлы, переводы, шаблоны) в workspace.
3. **PR-гейт:** `gh pr create --repo <publication-repo>` создаёт Pull Request в publication-репо через GITHUB_TOKEN.
4. **Editorial review:** человек проверяет PR в publication-репо и мёрджит явно.

## Инварианты

- Ничего не попадает в publication-репо без явного approve PR.
- Параметры workflow фиксируют версионирование: каждый PR трассируем.
- GITHUB_TOKEN с правами `contents: write, pull-requests: write` на publication-репо.

## Применение

| Контекст | source-repo | publication-repo |
|---------|-------------|------------------|
| WP-322 Ф3 | DS-principles-curriculum | aisystant/docs |
| Любой Content → Vitrine | Pack-репо / DS-content | docs.example.org |

## Отличие от DP.M.079

DP.M.079 (Pack-watcher) — автоматическая синхронизация: изменение SoT → `repository_dispatch` → rebuild downstream без ревью. Настоящий метод — ручной триггер + обязательный PR-гейт. Выбор: автоматика (DP.M.079) для внутренней синхронизации, PR-гейт (DP.M.104) для публичных выпусков.

## Связи

- Дополняет: [DP.M.079](DP.M.079-pack-watcher-cross-repo-trigger.md) — комплементарный паттерн
