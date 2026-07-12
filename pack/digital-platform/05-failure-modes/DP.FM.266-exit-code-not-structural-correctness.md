---
id: DP.FM.266
type: failure-mode
title: exit-code-not-structural-correctness — инструмент возвращает 0, но структурная корректность результата не подтверждена
trust: observed
epistemic_stage: confirmed
domains: [tooling, ci, merge-operations, verification, plistbuddy]
source_session: 2026-07-07 session-close (WP-454, fix install-launchd.sh)
valid_from: 2026-07-07
schema_version: 1
related:
  see_also: [DP.FM.038, DP.FM.192]
---

# DP.FM.266 — Exit-код инструмента ≠ структурная корректность результата

## Симптом

Скрипт проверяет только `$?` после merge/patch-операции. Инструмент (PlistBuddy `-c Merge`, patch, jq, xmlmerge и т.п.) возвращает `exit 0`, сигнализируя «операция выполнена». Однако результирующий файл содержит структурные дефекты (конфликты, недопустимые ключи, коллизии) в секциях за пределами явного scope операции. Скрипт считает работу успешной.

## Корень

Инструмент не несёт ответственности за структурную корректность **всего** результата — он гарантирует только успех **своей** операции. Проверка `$? == 0` подтверждает первое, не второе.

## Профилактика

**Правило:** для merge/patch-операций exit-код инструмента — необходимое, но не достаточное условие. После операции обязательно читать результирующий файл и верифицировать ожидаемые ключи/структуру явно.

Тест: «exit 0 получен — достаточно ли это для структурных изменений?» Нет → верифицировать output state.

```bash
# Неверно: полагаться только на exit-код
PlistBuddy -c "Merge $source" "$target" && echo "OK"

# Верно: проверить ожидаемые ключи явно
PlistBuddy -c "Merge $source" "$target" || exit 1
expected_val=$(PlistBuddy -c "Print :LaunchDaemon:Label" "$target" 2>/dev/null)
[ -z "$expected_val" ] && { echo "ERROR: merge incomplete"; exit 1; }
```

## Применимо к

- PlistBuddy `-c Merge` (macOS)
- patch / git apply / xmlmerge — любые patch-инструменты с selective-scope
- jq merge, yq merge — слияние структурированных данных
- Любой CI-шаг, проверяющий только exit-код составной операции

## Связано

- DP.FM.038 — silent-pass validator on missing input (инструмент зеленеет на отсутствующем входе; этот FM — успех при дефектном выходе)
- DP.FM.192 — subshell redirect silences exit code (bash-механизм маскировки; этот FM — семантика exit-кода не покрывает структурную корректность)
