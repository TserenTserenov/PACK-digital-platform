---
id: DP.M.216
name: "DNS A-record cutover — zero-downtime переезд домена"
type: method
domain: digital-platform
pack_refs: []
trust: high
epistemic_stage: empirical
status: active
valid_from: 2026-05-29
schema_version: 1
source: "session-transcript 2026-05-29, peer-session 05 production-deploy-iwe-vps"
last_updated: 2026-08-01
---

# DP.M.216 DNS A-record cutover — zero-downtime переезд домена

## Описание

Паттерн переноса домена на новый IP с минимальным downtime и встроенным rollback.

## IPO

**Вход:** домен на старом хосте (A-запись → старый IP); новый хост готов к production
**Процесс:**
1. За 24ч до cutover: снизить TTL до 300s — старые DNS-кэши вытекут быстро
2. Держать старый хост «тёплым» (deployed, работающим) до подтверждения smoke на новом
3. Cutover: изменить A-запись на новый IP
4. Smoke-test на новом хосте
5. При сбое: A-запись обратно на старый IP (~5 мин downtime)

**Выход:** домен указывает на новый хост; старый — ready for rollback в течение нескольких часов

## Правила

**A-запись vs CNAME:**
- A-запись → статический IP: прямое разрешение, нет overhead lookup
- CNAME → hostname: добавляет lookup latency; запрещена на zone apex (naked domain)
- При переезде на VPS со статическим IP → A-запись

## Тест

«Нужен rollback быстрее 5 минут?» Нет → этот паттерн достаточен. Да → blue-green с load balancer.
~~~

**Вердикт:** accept
**Обоснование:** Стандартный DNS-паттерн, не привязан к конкретному провайдеру. TTL-снижение, A vs CNAME — основы DNS. Применим при любом переезде (Heroku→VPS, Railway→Fly.io, AWS→Hetzner). Bounded context PACK-digital-platform (infrastructure patterns).

---

## Кандидат #5

**Источник capture:** `### Completion sync под retry-безопасность: UPSERT → sync_state → notify [feed:session-close 2026-05-29]`
**Сырой текст:** «Критический порядок для completion: `update_progress(completed)` → `update_intern(marathon_status='completed')` → `send_message`. Если внешний Telegram API упадёт до синхронизации статуса — при ретрае поздравление придёт повторно.»
**Классификация:** method
**source_file:** captures.md

**Куда записать:**
- **Репо:** PACK-digital-platform
- **Файл:** `pack/digital-platform/03-methods/DP.M.217-idempotent-last-notify-order.md`
- **Действие:** создать файл

**Совместимость:**
- **Результат:** совместим — resilience/retry паттерн в distributed systems
- **Проверено:** DP.M.217 свободен (max = 216 после кандидата #4)

**Готовый текст (ready-to-commit):**

~~~markdown
---
