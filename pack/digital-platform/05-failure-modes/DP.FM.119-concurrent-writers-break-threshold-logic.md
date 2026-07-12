---
id: DP.FM.119
type: failure-mode
domain: digital-platform
status: active
valid_from: 2026-05-30
source: peer-session 2026-05-30-05-watchdog-recurring-warnings; commit cc543303 (WP-7 WD)
---

# DP.FM.119: Concurrent writers ломают threshold-логику auto-commit

## Симптом

В скриптах auto-commit / batch-job / scheduled-flush с порогом «N минут с последнего коммита» используется `git log -1` без grep по собственному pattern. При наличии ≥2 независимых writers в репо (auto-commit + scheduler-report + drift-watcher + user manual commits) каждый чужой коммит резетит таймер автоматически → собственный writer навсегда блокирован, накапливается dirty state, watchdog кричит.

## Признаки

- Fix исправляет threshold (поднимает minimum), но через 1-2 недели рекуррирует — потому что добавился новый concurrent writer.
- Watchdog/мониторинг видит «dirty state >X дней» при формально работающем auto-commit.
- `MINUTES_SINCE_COMMIT < threshold` срабатывает на чужой коммит, не на свой.
- Safety net (daily squash, periodic flush) использует threshold того же класса — compounding defect.

## Механизм

Threshold-логика на time-based триггере неявно предполагает single-writer state machine. При concurrent writers условие «N минут с последнего коммита» становится зависимым от чужой активности, а не от собственного состояния. Каждый чужой коммит сбрасывает свой собственный таймер.

## Корректный паттерн

```bash
LAST_COMMIT_TS=$(git log -1 --grep='<own-commit-pattern>' --format=%ct)
```

Фильтрация по собственному commit-message pattern (`chore: auto-sync`, `report: weekly`) изолирует timer от чужих писателей.

## Профилактика (smoke перед коммитом infra-fix)

1. `git log --name-only --since=14d` — кто пишет в этот репо за последние 2 недели?
2. Список writers (auto-commit, scheduler, watchdog, manual) — какие commit-patterns у каждого?
3. Threshold-проверка фильтрует по моему pattern? Если нет → fix.
4. Safety net (daily squash, retry) не зависит от threshold того же класса? Если зависит → compounding defect.

## Применимость

Любые multi-writer state machines с time-based триггерами:
- auto-commit / auto-save batch jobs
- batch-flush с порогом «N минут с последней записи»
- retry-loop с back-off от «последнего успеха»
- watchdog с heartbeat-таймером

## Связи

- **Прецедент:** WP-7 WD (29 мая 2026), commit cc543303 — daily squash в 05:00 рекуррентно блокировал auto-commit.
- **Lesson (memory/):** `memory/lessons_infra_fix_coverage_smoke.md` — частный случай с smoke-чеклистом.
- **Соседний FM:** DP.FM.099 (notify subscription tied to connection) — другая ось multi-writer проблем.