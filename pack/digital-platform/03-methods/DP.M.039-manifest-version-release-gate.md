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

## Применимость

Паттерн «manifest version == CHANGELOG version» работает для любого продукта с:
- Версионированным manifest-файлом (package.json, manifest.json, pyproject.toml)
- Отдельным CHANGELOG.md

## Связи

- **DP.FM.007** (View Drift): детектор предотвращает drift между CHANGELOG (view) и manifest (model).
- Интегрируется в `integration-contract-validator.sh` как detector 9.
