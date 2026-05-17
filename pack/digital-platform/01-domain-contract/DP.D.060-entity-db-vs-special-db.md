---
id: DP.D.060
name: "Entity-БД vs Special-БД: изолированный threat model и независимый lifecycle"
type: distinction
status: active
valid_from: 2026-05-14
source: "ADR-004 v2.5 (DP.ARCH.004), WP-253-C (отделение secrets от persona)"
---

# DP.D.060: Entity-БД vs Special-БД

## Различение

| Ось | Entity-БД | Special-БД |
|-----|-----------|------------|
| **Что хранит** | Domain-объекты (пользователи, контракты, события) | Инфраструктурные артефакты с отдельным threat model |
| **Lifecycle** | Живёт вместе с доменом | Ротируется по независимому расписанию |
| **OPS-доступ** | Требует понимания business-логики | OPS работает без понимания домена |
| **Компрометация** | Последствия в рамках одного домена | Последствия выходят за пределы одного домена |

## Три критерия выделения в Special-БД

1. **Security** — credentials с последствиями вне одного домена (OAuth-токены дают доступ к внешним сервисам)
2. **Evolvability** — содержимое ротируется независимо от основной БД (ory_tokens TTL 1h; github_connections stable; google_calendar раз в месяц)
3. **Maintainability** — OPS работает с БД без понимания business-логики домена

При выполнении всех трёх — выносить в Special-БД. При одном-двух — рассматривать отдельную таблицу в existing БД.

## Примеры (IWE)

**Special-БД `secrets`:** ory_tokens, github_connections, dt_tokens, google_calendar, oauth_pending_state. Реализует §10.12 vault-паттерн DP.ARCH.004 — lightweight vault без HashiCorp зависимости. Отделена от `persona` (WP-253-C, 4 мая 2026).

**Entity-БД:** learning (события обучения), persona (профили пилотов), rewards (вознаграждения).

## Тест

«Если завтра сменится провайдер OAuth — нужно ли трогать persona-БД?»
- Нет → они независимы → secrets выделена правильно.

## Связи

- DP.ARCH.004 v2.5 — ArchGate с ЭМОГССБ-анализом данного решения
- DP.D.059 — три класса credentials storage при ротации (другой разрез: где хранятся физически)
