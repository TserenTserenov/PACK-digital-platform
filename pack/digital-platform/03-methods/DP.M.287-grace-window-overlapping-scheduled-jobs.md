---
id: DP.M.287
type: method
title: Grace-window для перекрывающихся scheduled jobs с разным временем запуска
trust: confirmed
epistemic_stage: confirmed
domains: [scheduling, watchdog, cron, batch-processing]
source_session: 2026-06-06 session-close (WP-7 BFA Волна 1+2)
source_commit: 8d84bda (day-open-preflight.sh)
valid_from: 2026-06-06
schema_version: 1
---

# DP.M.287 — Grace-window для перекрывающихся scheduled jobs

## Контекст

Два scheduled job'а с разными временами запуска:
- **Generator** — создаёт файл/запись (00:01 EEST или при catch-up до 06:03)
- **Auditor** — проверяет наличие артефакта (04:00 EEST)

Auditor может сработать ДО Generator на нормальном рабочем дне — не потому что Generator упал, а потому что catch-up ещё не завершился.

## Метод

Ввести **grace window**: период, в течение которого Auditor возвращает статус `pending` вместо `overdue`.

```bash
GRACE_END_HOUR=6  # 06:30 EEST
current_hour=$(date +%H)

if [ "$current_hour" -lt "$GRACE_END_HOUR" ]; then
    echo "status: pending (within grace window)"
    exit 0
fi

# Только за пределами grace window проверяем наличие артефакта
if [ ! -f "$EXPECTED_ARTIFACT" ]; then
    echo "status: overdue"
    exit 1
fi
```

**Размер grace window** = `max(generator_catch_up_deadline, auditor_fire_time) + buffer`.

## Тест применимости

«Может ли Auditor сработать ДО Generator на нормальном рабочем дне?» Да → grace window обязателен.

Дополнительные сигналы:
- Catch-up / retry логика у Generator с неопределённым временем завершения
- Auditor запускается в пределах 2 часов от Generator

## Антипаттерн

Hardcode «артефакт должен существовать к HH:MM» без учёта retry/catch-up сценариев → ложные алерты при первом запуске или в дни с задержкой.

## Применимо к

- Day Open pipeline с overnight-auditor
- CI/CD с pre-flight checks
- Watchdog + generator пары в launchd/cron
- Любой «checker + producer» с разными временами старта

## Связано

- WP-7 BFA Волна 1+2 — источник
- DP.FM.137 (asymmetric alert suppression) — смежный класс ошибок мониторинга
