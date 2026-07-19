---
id: DP.D.203
name: "iCloud app container ≠ iCloud Drive — данные iOS-приложений доступны только через API"
type: distinction
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-07-10
created: 2026-07-19
source: "миграция из 01B-distinctions.md (РП170 Ф2, 2026-07-19)"
see_also: []
schema_version: 1
---

# DP.D.203: iCloud app container ≠ iCloud Drive — данные iOS-приложений доступны только через API

**iCloud app container** — приватное хранилище данных конкретного iOS/macOS-приложения (Health, Fitness и др.). Данные не доступны как обычные файлы без нативного API (HealthKit, CloudKit). При включённом «Optimize Mac Storage» файл существует по пути (`~/Library/Mobile Documents/...`), но локально занимает 0 KB — заглушка (содержимое только в облаке). Проверка `[ -f ... ]` проходит, а `du -k` показывает 0.

**iCloud Drive** — пользовательское файловое хранилище (`~/iCloud Drive/`, `~/Library/Mobile Documents/com~apple~CloudDocs/`). Читается стандартными POSIX-вызовами после материализации файла.

**Тест:** «Данные iOS-приложения синхронизируются через iCloud?» — не значит «можно прочитать как файл без API». Перед содержательными операциями — `du -k` для проверки локальной материализации.

**Следствие для data-pipeline:** iCloud Drive — не универсальный мост к данным iOS-приложений. Для каждого источника (Health, Garmin, Strava) нужен отдельный экспортёр на устройстве. Эталон архитектуры — трёхслойка IO/calc/store (WP-470, panel_wakatime.py).

**Связано:** облачная заглушка smart-sync проходит проверку существования, но рушит проверку бэкапа (WP-7 BAK1-F2).

**Источник:** session-close 2026-07-06, отчёт `extraction-reports/2026-07-08-inbox-check-4.md` (peer-session apply-captures 2026-07-10).
