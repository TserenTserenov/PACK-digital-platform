---
id: DP.METHOD.202
name: "Manifest-only version check"
type: method
status: active
valid_from: 2026-05-13
source: "FMT-exocortex-template; DS-my-strategy/inbox/captures.md:1732-1738"
summary: "Pre-release gate: сравнить version в манифесте с первой строкой CHANGELOG, чтобы поймать забытый generate-manifest.sh до публикации."
tags: [manifest, release-gate, changelog, pre-release, drift]
---

# DP.METHOD.202 — Manifest-Only Version Check

## §0 Проблема

Разработчик обновляет CHANGELOG, но забывает запустить `generate-manifest.sh`. В результате `manifest.json` содержит устаревшую версию, а пользователи видят неверный `installed-version` при диагностике.

## §1 Метод

Добавить в `integration-contract-validator.sh` детерминированный шаг:

```bash
manifest_version=$(jq -r '.version' manifest.json)
changelog_version=$(head -n1 CHANGELOG.md | grep -oE '[0-9]+\.[0-9]+\.[0-9]+')
[ "$manifest_version" = "$changelog_version" ] || exit 1
```

## §2 Инварианты

- Проверка только читает файлы, не меняет их.
- Падает на CI/pre-release этапе, не в production.
- Применим к любой системе с версионированным манифестом и CHANGELOG.

## §3 Связи

- DP.METHOD.201 (gate-consistency + deterministic rerunner) — соседний класс pre-flight проверок.
- captures.md:1732-1738.
