---
id: DP.D.140
name: "Наблюдатель-сторож (observability) ≠ Сервис доставки"
type: distinction
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-06-14
created: 2026-07-19
source: "миграция из 01B-distinctions.md (РП170 Ф2, 2026-07-19)"
see_also: []
schema_version: 1
---

# DP.D.140: Наблюдатель-сторож (observability) ≠ Сервис доставки

| Наблюдатель-сторож | Сервис доставки |
|--------------------|-----------------|
| Read-only: только читает состояние | Write-side: мутирует историю (commit, push, sync) |
| Генерирует алерт или сигнал при отклонении | Выполняет доставку с явным scope операции |
| Не трогает репозиторий или систему | Имеет contract «что именно доставляет» |
| Пример: iwe-pull-repos (pull + alert) | Пример: iwe-push-ahead (push clean+ahead коммитов) |

**Инвариант:** observability-компонент остаётся read-only; side effects (доставка, восстановление, merge) — отдельные сервисы с явным scope.

**Тест:** «Делает ли компонент `git commit` / `git push` / мутирует ли историю?» Да → это не чистый наблюдатель, это delivery-сервис.

**Применимо:** к watchdog/monitor-компонентам в CI/CD, cron, Git automation, серверных агентах.

**Связи:**
- DP.FM.146 — failure mode «unconditional helper» (смежный класс неправильной ответственности)
- DP.D.048 Скрипт ≠ Агент (родственное различение по ответственности)

**Источник:** session-transcript 2026-06-11, git diff iwe-server-config (484821f), WP-411
