---
id: DP.FM.274
name: "psql без ON_ERROR_STOP=1 в heredoc: SQL-ошибка не останавливает скрипт"
type: failure-mode
domain: DP
status: active
valid_from: 2026-07-13
source: "session-close 2026-07-09; payment-registry INCIDENT-2026-07-08 (commit dad0baa)"
related:
  see_also: ["DP.FM.265: catch-all exception masks errors (аналог на уровне Python)"]
tags: [psql, bash, heredoc, silent-fail, sql-error, exit-code]
---

# DP.FM.274 — psql без ON_ERROR_STOP=1: SQL-ошибка внутри heredoc проглатывается

## Паттерн

Bash-скрипт запускает `psql ... <<PSQL ... PSQL` без флага `-v ON_ERROR_STOP=1`.
psql по умолчанию продолжает выполнение после ошибки внутри heredoc и возвращает exit code 0.
Скрипт печатает «done.» / логирует успех, хотя SQL-транзакция не завершилась.

## Пример

```bash
# Антипаттерн: нет ON_ERROR_STOP
psql "$DATABASE_URL" <<PSQL
INSERT INTO subscription.contract (...) VALUES (...);
COMMIT;
PSQL
echo "done."   # печатается ДАЖЕ при SQL-ошибке

# Fix: явная остановка при первой ошибке
psql -v ON_ERROR_STOP=1 "$DATABASE_URL" <<PSQL
INSERT INTO subscription.contract (...) VALUES (...);
COMMIT;
PSQL
# exit code ≠ 0 при ошибке → if psql ...; then ...; else handle_error; fi
```

## Инцидент

INCIDENT-2026-07-08 (payment-registry, `scripts/sync-contracts.sh`, commit dad0baa):
скрипт логировал `fetched=N, resolved=M` при каждом запуске, хотя INSERT молча падал.
Обнаружено при ручной проверке вывода psql с флагом.

## Диагностика

**Тест:** «Bash-скрипт проверяет `$?` или использует `if psql` после heredoc?»
Если да — без `-v ON_ERROR_STOP=1` результат ненадёжен.

**Сигнал silent fail:** скрипт завершается с exit 0 и сообщением об успехе, но целевая таблица пуста или не обновлена.

## Применимость

Все bash-скрипты с `psql ... <<SQL`. Аналог: `mysql` без `--abort-source-on-error`.
