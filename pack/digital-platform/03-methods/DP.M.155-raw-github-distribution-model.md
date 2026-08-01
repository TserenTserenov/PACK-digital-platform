---
id: DP.M.155
name: Raw GitHub Distribution Model (raw-main delivery — коммит в main = production, version — info label не gate)
type: method
status: draft
valid_from: 2026-05-22
summary: "Модель доставки template-системы через raw.githubusercontent.com/<owner>/<repo>/main/<path>. Любой коммит в main немедленно доступен пользователям при следующем update.sh. Версия в manifest — информационная метка, не gate. Цена: pre-merge CI становится единственным защитным барьером."
related:
  see_also: [DP.M.039, DP.M.101]
tags: [distribution, release-model, update-mechanism, raw-main, template-delivery]
source: "FMT-exocortex-template commit 75d15f0 — docs/RELEASE-PROCESS.md (вступление, 2026-05-22)"
schema_version: 1
---

# DP.M.155 — Raw GitHub Distribution Model

## Суть метода

Template/dotfiles/framework-система доставляет файлы пользователям через прямые GET-запросы к `raw.githubusercontent.com/<owner>/<repo>/main/<path>` (или эквивалент GitLab/Gitea). Скрипт-обновлятор (`update.sh`) скачивает текущий HEAD ветки `main`.

### Следствия (часто неочевидные при первом проектировании)

1. **Нет staging-ветки и нет release-tags** — пользователь всегда получает HEAD main при запуске `bash update.sh`.
2. **Version bump в manifest НЕ является gate'ом** — он не блокирует доставку и не контролирует видимость файлов. Это **информационная метка** для пользователя («стабилизированный набор»).
3. **Любая поломка попадает в main → к пользователям** при следующем запуске update.sh. Pre-merge CI становится **единственным защитным барьером**.
4. **Откат** требует revert-коммита в main, не «переключения tag» — все пользователи увидят новое состояние сразу.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Скорость доставки ↔ защитный барьер | Raw-main отдаёт любой коммит пользователям через минуту после merge — единственным барьером остаётся pre-merge CI, и вся стоимость безопасности концентрируется в нём одном, без staging и tag'ов |
| Простота инфраструктуры ↔ контролируемость rollout и отката | Отказ от staging-ветки, tag releases и версионной навигации в `update.sh` убирает целый пласт release-engineering, но откат возможен только revert-коммитом, который все пользователи увидят сразу, — нельзя ни выкатить частично, ни «показать старый tag» |

## Альтернатива: Release-Branch Model

- Требует staging-ветки + tag releases + версионную навигацию в `update.sh` (через `raw.githubusercontent.com/<owner>/<repo>/<tag>/...`).
- Цена: версия становится **барьером**, обновление откладывается до bump'а.
- Цена: версионная инфраструктура (changelog → tag → release notes → migration scripts).
- Выгода: контролируемый rollout, легко откатить (показывать старый tag), staging-environment.

## Trade-off

| Модель | Скорость доставки | Защитный барьер | Откат |
|--------|-------------------|-----------------|-------|
| Raw-main | Высокая (минута после merge) | Только pre-merge CI | Revert-коммит, все пользователи сразу |
| Release-branch | Низкая (до bump'а) | CI + ручной bump + tag | Переключение tag |

## Когда выбирать raw-main

- Сильный pre-merge CI (integration tests, lint, type-checks).
- Высокий темп изменений и доверие к pipeline'у.
- Малая команда без отдельной release-engineering функции.
- Цена ошибки — обозримая (исправление в течение часа).

## Когда выбирать release-branch

- Большие группы пользователей, для кого rollback значим.
- Регулируемая среда (compliance, audit-требования к версионности).
- Хрупкость интеграций (миграции БД, изменения публичного API).
- Цена ошибки — высокая (репутация, юридические последствия).

## Тест применимости

«Может ли коммит в main без bump'а попасть к пользователю?» Да → raw-main model; Нет → release-branch model.

## Известные примеры

- Oh-My-Zsh (раньше — raw-main; сейчас гибрид).
- IWE / FMT-exocortex-template (raw-main).
- Большинство dotfiles-frameworks с `curl | bash`.

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| Привычка к version-bump как к релизу затмевает реальный gate | Практик переносит ментальную модель release-branch и считает bump манифеста моментом публикации, хотя в raw-main это информационная метка — внимание к церемонии версии оттягивает ресурс от укрепления pre-merge CI, который и есть единственный барьер |
| Локальность merge затмевает мгновенность доставки | Коммит в main ощущается как действие «у себя в репо», а не как публикация всем пользователям при следующем `update.sh` — «поправлю следом» игнорирует окно, в котором сломанный HEAD уже скачивается |

## Ссылки

- Источник: FMT-exocortex-template commit 75d15f0 (docs/RELEASE-PROCESS.md, вступление)
- See also: DP.M.039 (Manifest Version Release Gate) — детектор, который ловит несинхронизированный version-bump внутри raw-main модели; DP.M.101 (Semver Automation Docs-as-Code) — про автоматизацию version-bump'ов

---

> 2026-08-01 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 5). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
