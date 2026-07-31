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

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Автоматическая классификация bump ↔ точность версионирования | Паттерны conventional commits (feat→minor, fix→patch, BREAKING→major) снимают ручной changelog, но коммиты без prefix молча получают patch, и реальная значимость изменения для читателей может не совпасть с механической классификацией |
| Частота релизов ↔ шум уведомлений | Триггер на каждый merged PR с разрешёнными labels держит changelog актуальным, но каждый релиз — это tag + GitHub Release + Telegram/Slack-уведомление; порог «≥N коммитов» и пустой диапазон без релиза удерживают баланс |

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

## Bias-Annotation

_Куда систематически съезжает внимание практикующего. Epistemic stage карточки — `validated`._

| Bias | Direction of distortion |
|------|--------------------------|
| Механика паттернов вместо смысла изменения для читателя | Внимание съезжает к тому, какой prefix у коммитов, и конфликт паттернов (feat + fix в одном PR) принимается как решённый max bump'ом — хотя для читателей changelog значимость изменения механической классификацией не исчерпывается |
| Дисциплина conventional commits недооценивается как вход | Внимание инвестируется в настройку `semver-bot.py` и `release.yml`, а качество классификации целиком зависит от того, пишут ли контрибьюторы prefix'ы; дефолт «patch» для коммитов без prefix тихо занижает версии значимых изменений |

## 5. Связи

- Смежный: DP.M.102 (Conditional Auto-Merge) — часто срабатывает после merge в этом методе
- Реализация: `semver-bot.py` + `release.yml` в DS-principles-curriculum (WP-322 Ф6, commit 0017b2a)

---

> 2026-07-31 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 4). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
