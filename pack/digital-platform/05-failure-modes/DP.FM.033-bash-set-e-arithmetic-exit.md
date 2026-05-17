---
id: DP.FM.033
name: Bash arithmetic increment под set -e (Bash Arithmetic Increment Under set -e)
category: shell-scripting
severity: minor
status: active
summary: "Конструкция `((var++))` возвращает exit code 1 при var=0 (post-increment) — под `set -e` вызывает тихий abort скрипта без сообщения об ошибке."
created: 2026-05-15
valid_from: 2026-05-15
related:
  see_also: [DP.FM.018, DP.FM.029]
tags: [bash, shell-scripting, set-e, arithmetic, silent-fail, smoke-test]
source: "git diff FMT-exocortex-template (98956df, 323f4fa — WP-315 Ф7 E2E fixes scripts/iwe-grep-secret.sh), 2026-05-15"
schema_version: 1
---

# [DP.FM.033] Bash arithmetic increment под set -e

## Суть паттерна

Конструкция `((var++))` использует **post-increment**: возвращает старое значение `var` как «истинность» арифметического выражения. Если `var = 0` на момент инкремента, exit code = 1 (false). Под `set -e` это интерпретируется как ошибка → скрипт abort'ится молча, без сообщения.

**Скрытость:** скрипт работает в одном окружении (где счётчик уже > 0 на первом инкременте) и падает в другом (чистый запуск, счётчик = 0). Failure mode выявляется только на E2E smoke с чистой инициализацией.

## Корень

`((expression))` оценивает арифметическое выражение и возвращает:
- `0` (success) если результат ≠ 0
- `1` (failure) если результат = 0

`var++` — post-increment: возвращает старое значение `var` ДО инкремента. При `var=0`:
- Возвращаемое значение выражения = 0
- Exit code = 1
- `set -e` → abort

## Где проявляется

| Контекст | Проявление |
|---|---|
| Счётчики ошибок в init=0 | `INFRA_ERRORS=0; ((INFRA_ERRORS++))` → abort на первом инкременте |
| Аккумуляторы статистики | `TOTAL_HITS=0; ((TOTAL_HITS++))` → abort при первом совпадении |
| Циклы по пустым множествам | первая итерация с counter=0 |
| CI/E2E на чистой среде | проявляется ВСЕГДА (vs локально, где state накоплен) |

## Решение

Любая из трёх форм:

```bash
((counter++)) || true              # явное подавление
: $((counter++))                   # `:` — no-op команда, exit code = 0
((counter+=1))                     # pre-increment эквивалент (но и оно может срабатывать при counter=-1)
counter=$((counter + 1))           # самая безопасная форма
```

**Правило для шаблона bash-скриптов под `set -e`:** все арифметические инкременты счётчиков, инициализированных нулём, оборачивать `|| true` либо использовать `counter=$((counter + 1))`.

## Профилактика

- **E2E smoke на чистой среде** (CI без накопленного state) обязателен для любого bash-скрипта со счётчиками
- **shellcheck SC2219:** не покрывает этот случай (warning только про style)
- **Code review:** grep по проектным скриптам на `((.*++))` без `|| true`

## Прецеденты

- **WP-315 Ф7 (2026-05-15):** `scripts/iwe-grep-secret.sh` — два независимых счётчика (`TOTAL_HITS`, `INFRA_ERRORS`) → потребовалось 2 коммита (`98956df`, `323f4fa`) для покрытия обоих путей. Падение проявилось только на tsekh-1 при чистом запуске (на macOS state был накоплен).

## Связи

- **DP.FM.018** Markdown Display-маркеры в data-полях — родственный shell-pipeline FM
- **DP.FM.029** Cross-platform path leak — тоже проявляется только в одной среде
- **CLAUDE.md §2 Pull-on-Touch** — паттерн «работает локально, падает в проде» из той же категории среда-зависимых багов
