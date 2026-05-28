---
id: DP.FM.087
name: "Watchdog false-positive: молодой скрипт как overdue"
type: failure-mode
pack: digital-platform
status: active
valid_from: 2026-05-28
source: peer-session 2026-05-28-01 (monthly-maintenance 649ч overdue анализ)
---

# DP.FM.087: Watchdog false-positive — молодой скрипт «overdue»

## Симптом

Watchdog-система сигнализирует «overdue» для скрипта, который был создан позже ожидаемой даты первого запуска. Появляются alert'ы для только что установленных компонентов.

## Механизм

Watchdog начинает считать `next-expected-run` с фиксированной даты (epoch start программы или расписание до установки скрипта), не с момента первого фактического запуска. При наличии нового скрипта (файл создан после baseline-даты) watchdog немедленно видит его как «давно overdue».

**Пример:** `monthly-maintenance.sh` создан 26 мая 20:06; watchdog читал «process overdue since 2026-05-01» — 649ч overdue alert.

## Тест

`ls -la <script>` → дата создания позже следующего ожидаемого запуска → false-positive.

## Различение

«Не запускался никогда» (новый скрипт) ≠ «Не запускался дольше интервала» (реальный alert).

## Fix-паттерн: Smart-init

При первом обнаружении скрипта watchdog записывает `last_run: now()` вместо нулевого значения. Скрипт ставится в очередь на запуск «как можно скорее», но НЕ отмечается как критически overdue.

## Применимость

cron, launchd, systemd-timer, любые scheduled-агенты с watchdog-мониторингом. Особенно критично при автоматической установке новых компонентов (update.sh, install-launchd.sh).

## Контекст

Обнаружен при анализе алертов calendar-pipeline watchdog в peer-session 2026-05-28-01. Из 4 alerts: 2 ложных (новые скрипты), 2 реальных.
