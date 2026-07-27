---
id: DP.M.039
name: Manifest Version Release Gate (Проверка версии manifest перед релизом)
type: method
status: draft
valid_from: 2026-05-13
summary: "Pre-release детектор: версия в manifest.json должна совпадать с версией в CHANGELOG.md. Ловит забытый запуск generate-manifest.sh перед релизом."
related:
  prevents: [DP.FM.007]
  integrates_with: [integration-contract-validator.sh]
tags: [release, manifest, changelog, ci, validation, template]
source: "git diff FMT-exocortex-template commit cba5c3a, detector 9, WP-7"
created: 2026-05-13
---

# DP.M.039 — Manifest Version Release Gate

## Суть метода

При выпуске новой версии платформенного шаблона разработчик может:
1. Обновить CHANGELOG.md с новой версией
2. Забыть запустить `generate-manifest.sh` для обновления `manifest.json`

Результат: пользователи видят устаревшую версию в диагностике, `installed-version` в health-check неверна.

**Детектор 9** в `integration-contract-validator.sh` решает эту проблему: сравнивает `version` в `manifest.json` с первой строкой `CHANGELOG.md`.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Автоматизация Детектора 9 (объективная проверка совпадения версий) ↔ ручная дисциплина фактически запустить `generate-manifest.sh` в момент обновления CHANGELOG.md | Детектор ловит уже случившийся drift, но ничто не гарантирует, что разработчик вообще вызовет проверку до тегирования — сам человеческий шаг «забыл запустить скрипт» остаётся вне автоматизации |
| Простота единственного сравнения (`manifest.version == CHANGELOG` первая строка) ↔ переносимость паттерна на другие форматы (package.json, pyproject.toml из «Применимость») | Жёстко закодированный `jq`/`grep`/`sed` под конкретный формат манифеста экономит время сейчас, но каждый новый формат требует своей адаптации логики парсинга |

## Алгоритм

```bash
# Детектор 9: manifest version == CHANGELOG version
MANIFEST_VER=$(jq -r '.version' update-manifest.json)
CHANGELOG_VER=$(grep -m1 '^## \[' CHANGELOG.md | sed 's/## \[\(.*\)\].*/\1/')

if [ "$MANIFEST_VER" != "$CHANGELOG_VER" ]; then
    echo "ERROR: manifest.json version ($MANIFEST_VER) != CHANGELOG.md version ($CHANGELOG_VER)"
    echo "Run: bash scripts/generate-manifest.sh"
    exit 1
fi
```

## Точка применения

**Pre-release gate** — запускается в CI или вручную перед тегированием:

```
CHANGELOG.md обновлён → generate-manifest.sh → detector 9 PASS → git tag → publish
```

## Bias-Annotation

_Куда систематически съезжает внимание практикующего. Статус карточки — `draft`: пометка `tentative` по прецеденту SA.METHOD.001 (WP-448 Ф12)._

| Bias | Direction of distortion |
|------|--------------------------|
| _(tentative)_ Доверие к «зелёному» detector 9 как полной гарантии релиза | Внимание смещается на факт прохождения одной проверки версий как достаточное условие готовности к релизу, хотя «Точка применения» описывает detector 9 лишь как один шаг в цепочке CHANGELOG → generate-manifest → detector 9 → tag → publish — остальные шаги цепочки выпадают из фокуса |
| _(tentative)_ Недооценка ручного запуска `generate-manifest.sh` при переносе паттерна | При адаптации метода к другому формату (package.json/pyproject.toml, «Применимость») внимание уходит на копирование логики сравнения версий (Алгоритм), недооценивая, что для нового формата разработчик по-прежнему может забыть сам шаг генерации манифеста — детектор ловит рассинхронизацию постфактум, а не сам забытый запуск |

## Применимость

Паттерн «manifest version == CHANGELOG version» работает для любого продукта с:
- Версионированным manifest-файлом (package.json, manifest.json, pyproject.toml)
- Отдельным CHANGELOG.md

## Связи

- **DP.FM.007** (View Drift): детектор предотвращает drift между CHANGELOG (view) и manifest (model).
- Интегрируется в `integration-contract-validator.sh` как detector 9.

---

> 2026-07-27 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 2). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
