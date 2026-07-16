---
id: DP.FM.282
name: "github.event.commits Null on workflow_dispatch (Отсутствие commits при ручном триггере)"
category: ci-cd
severity: moderate
status: active
summary: "При запуске GitHub Actions через workflow_dispatch поле github.event.commits отсутствует (null), а не пустой массив. toJSON(null) возвращает строку «null», и jq .[] завершается ошибкой вместо пустого результата."
created: 2026-07-13
valid_from: 2026-07-13
related:
  see_also: [DP.FM.273]
tags: [github-actions, workflow_dispatch, jq, null, ci-cd, quirk]
source: "session-transcript 2026-07-09, WP-415 live shakedown (commit 040012c)"
---

# [DP.FM.282] github.event.commits Null on workflow_dispatch

## Суть паттерна

GitHub Actions: поле `github.event.commits` существует только для push-событий. При `workflow_dispatch` это поле **отсутствует** — не пустой массив `[]`, а именно `null`. При использовании `toJSON(github.event.commits)` и последующем `jq '.[]'` — `jq` получает строку `"null"` и завершается ошибкой `null is not iterable` вместо пустого вывода.

## Инцидент

WP-415 shakedown: workflow читал `github.event.commits` и пробрасывал через `jq '.[]'`. При первом ручном запуске (workflow_dispatch) — workflow упал на jq, несмотря на то что вся остальная логика была верна.

## Механизм

```yaml
# Проблема:
- id: commits
  run: echo "${{ toJSON(github.event.commits) }}" | jq '.[]'
  # При workflow_dispatch: toJSON(null) -> строка "null"
  # jq '.[]' на строке "null" -> error: null is not iterable

# Фикс:
- id: commits
  run: |
    COMMITS="${{ toJSON(github.event.commits) }}"
    echo "$COMMITS" | jq 'if . == null then empty else .[] end'
```

## Детектор

Любой workflow, читающий `github.event.commits`, должен проверять на null перед итерацией — особенно если workflow поддерживает и `push`, и `workflow_dispatch` триггеры.

## Тест на провал

«Запустить workflow вручную (workflow_dispatch) — завершается ли он с ошибкой в шаге, работающем с `github.event.commits`?» Да — FM.282.

## Защита

1. **Null-guard в jq:** `if . == null then empty else .[] end`.
2. **Bash-guard:** `[ "$COMMITS" = "null" ] && exit 0`.
3. **E2E тест:** запускать workflow через `workflow_dispatch` явно при каждом shakedown.

## Связи

- **DP.FM.273** (github-list-api-silent-truncation) — другой GitHub API quirk: молчаливое обрезание списков при превышении лимита.
