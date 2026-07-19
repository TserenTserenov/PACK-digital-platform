---
id: DP.D.087
name: "OAuth pending state in-memory ≠ OAuth pending state externalized (БД)"
type: distinction
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-05-24
created: 2026-07-19
source: "миграция из 01B-distinctions.md (РП170 Ф2, 2026-07-19)"
see_also: []
schema_version: 1
---

# DP.D.087: OAuth pending state in-memory ≠ OAuth pending state externalized (БД)

| Аспект | In-memory state | Externalized state (БД) |
|--------|-----------------|-------------------------|
| **Хранение** | Живой объект на инстансе сервера | Запись в общей БД (`oauth_pending_state`) |
| **Инициатор authorize** | Только authorize-сервер | Любой клиент с доступом к БД |
| **Масштабирование** | Требует sticky sessions | Горизонтальное без ограничений |
| **Выживаемость** | Теряется при рестарте | Персистентен (TTL/cron cleanup) |
| **Риск** | Нет race condition на state | Нужна транзакция на callback-match |
| **Пример** | Session cookie на одном инстансе | `oauth_pending_state` в Neon + bot-generated link |

**Тест выбора:** «Может ли OAuth-flow пережить рестарт authorize-сервера между шагами authorize и callback?»
- Да → externalized (БД)
- Нет → in-memory

**Почему важно:** Externalized state позволяет боту инициировать OAuth-flow за пользователя (генерирует запись в БД, отдаёт ссылку в чат) и поддерживает горизонтальное масштабирование authorize-handler. Цена — race conditions при параллельных запросах и требование транзакционности при callback-match.

**Контекст:** Выявлено при реализации LLM Proxy auth (WP-200 Ф7, 2026-05-22). Применимо ко всем multi-server OAuth-провайдерам и serverless-deployments authorize-эндпоинта.
