---
id: DP.METHOD.143
name: "Явная tolerate нефатального exit code в CI-скрипте"
type: method
pack: PACK-digital-platform
domain: digital-platform / ci-pipeline
trust: confirmed
epistemic_stage: observed
valid_from: 2026-07-10
source: "session-close 2026-07-06, FMT-exocortex-template translate-sync CI (commit e8c6d2e)"
schema_version: 1
---

# DP.METHOD.143 — Явная tolerate нефатального exit code в CI

**Суть:** Нефатальные exit codes (предупреждения, частичный успех) документируются явно и допускаются через именованный паттерн — не глушатся через `|| true`.

## Паттерн

```bash
set +e
cmd
rc=$?
set -e
if [ "$rc" -ne 0 ] && [ "$rc" -ne 2 ]; then
  exit "$rc"  # реальная ошибка
fi
# rc=2 — только предупреждения ASCII-guard, продолжаем
```

## Антипаттерн

```bash
cmd || true  # глушит ВСЕ ошибки, включая реальные сбои
```

## Документирование

Inline-комментарий обязателен: «rc=2 означает <что именно> — нефатально потому что <причина>». Без комментария — следующий maintainer не знает, ожидаем ли этот rc.

## Применимость

CI-скрипты, где инструмент различает warning (rc=2) от error (rc≠0,2). Без явной tolerate: либо fail при предупреждении, либо пропуск реальных ошибок.
