---
id: DP.M.075
name: No-op heartbeat для детекции silent-fail в scheduled workflow
kind: Method
status: active
created: 2026-05-17
sources:
  - WP-322 «Pack-drift-watcher» Ф1.3 H2+H3 (DS-principles-curriculum/890b15b — pack-drift-watcher.yml)
  - session-transcript 2026-05-17
related:
  complements: [DP.M.028]  # Stateless Worker Cursor Pattern — там negative signal (exit 1 на errors>0), здесь positive signal на no-op
  applies_to: [scheduled GH Actions, cron, drift-watcher, security-scan, low-rate watcher]
---

# DP.M.075: No-op heartbeat для детекции silent-fail в scheduled workflow

## Определение

Метод обнаружения silent-fail в scheduled CI/cron pipeline'ах с low event rate: при «no-op» исходе (drift=0, нет событий, нечего делать) workflow эмитит **positive heartbeat** — структурированную запись в лог + step-summary с метаданными последнего запуска. Отсутствие heartbeat за > expected interval = детектор silent failure.

## Проблема

Scheduled workflow (cron, drift-watcher, scheduled audit) при штатном «нечего делать» проходит без notification — на radar'е не отличается от «workflow не запускался вовсе» (cron broken, schedule disabled, branch protection ругается). Класс ошибок симметричен «errors>0 → exit 1 в systemd» — там детект через negative-signal, здесь через positive.

## Триггер применимости

> «Есть ли scheduled job, у которого "всё нормально" выглядит как "не запускался"?»

- **Да** → добавить heartbeat в no-op ветку.
- **Нет** (workflow реагирует на events и любой запуск что-то меняет) → не нужен.

## Компоненты heartbeat-сигнала

При no-op исходе:

1. **Лог-маркер** — `::notice::` (GitHub Actions) или structured log line, парсимый watcher'ом.
2. **Step Summary** — строка в `GITHUB_STEP_SUMMARY` с метаданными:
   - `timestamp` (когда сработал)
   - `source_hash` / `pack_commit` (на каком состоянии)
   - `trigger` (schedule / manual / push)
   - `run_url` (ссылка на запуск)
   - `result: no-op` (явный маркер)

## Различение

**Heartbeat ≠ positive-result logging.** Heartbeat нужен именно в «no-op» ветке, не вместо normal output. Если есть события — обычный лог. Если событий 0 — heartbeat.

## Применимость

- Scheduled GH Actions, GitLab CI cron jobs.
- Cron-jobs без external monitoring (uptime watcher не помогает).
- Watcher-воркеры с low event rate (drift, security scan, dependency audit, license audit).
- ETL-pipeline'ы, которые «штатно ничего не нашли».

## Связи

- **Дополняет:** feedback_silent_projection_fail.md (silent UPSERT fail в projection-worker).
- **Дополняет:** feedback_alerter_writer_sampling_drift.md (idle ≠ stuck без backlog-проверки).
- **Дополняет:** DP.M.028 (cursor pattern) — там exit-code на errors>0; здесь positive signal на drift=0.
