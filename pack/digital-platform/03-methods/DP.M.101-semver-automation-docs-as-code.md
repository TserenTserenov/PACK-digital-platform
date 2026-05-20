---
id: DP.M.101
name: Семантическое версионирование для Docs-as-Code
name_ru: Semver Automation для документационных репо
name_en: Semver Automation for Docs-as-Code
type: method
status: active
summary: "Алгоритм автоматической классификации bump'ов для docs-as-code: git log от последнего тега → классификация коммитов по паттернам (feat→minor, fix→patch, BREAKING→major) → changelog entry + релиз. Применимо к любому документационному репо с conventional commits."
created: 2026-05-19
trust:
  F: 3
  G: domain
  R: 0.80
epistemic_stage: validated
related:
  uses: []
  references: [DP.M.102]
tags: [versioning, docs-as-code, changelog, semver, automation, release]
wp: WP-322
---

# Semver Automation для Docs-as-Code (DP.M.101)

## 1. Контекст

Документационный репо с несколькими контрибьюторами требует версионирования: читатели должны знать, когда выходят значимые изменения. Ручной changelog → забывается или дублируется усилия.

## 2. Алгоритм

1. `git log <last_tag>..HEAD` — собрать коммиты от последнего тега
2. Классифицировать bump по паттернам в сообщениях:
   - `feat:` → minor bump
   - `fix:`, `hotfix`, `docs:` → patch bump
   - `BREAKING CHANGE` / `!` в типе → major bump
3. Сгенерировать changelog entry (дата, версия, список изменений)
4. Создать git tag и GitHub Release
5. Уведомить через Telegram/Slack о новом релизе

## 3. Триггеры запуска

- Merged PR с разрешёнными labels (pilot-approved, new-concept, hotfix) + ≥N коммитов
- Ручной запуск с параметрами (`workflow_dispatch`: guide_id, version)

## 4. Ограничения

- Конфликт паттернов в одном PR (feat + fix) → берётся max bump
- Коммиты без conventional commits prefix → по умолчанию patch
- Пустой диапазон (нет коммитов от тега) → релиз не создаётся

## 5. Связи

- Смежный: DP.M.102 (Conditional Auto-Merge) — часто срабатывает после merge в этом методе
- Реализация: `semver-bot.py` + `release.yml` в DS-principles-curriculum (WP-322 Ф6, commit 0017b2a)
