---
id: DP.M.212
name: "Маппинг Discourse webhook в IWE event pipeline"
type: method
domain: digital-platform
trust: high
epistemic_stage: empirical
status: active
valid_from: 2026-05-28
schema_version: 1
source: "session-transcript 2026-05-28, WP-327-Этап23"
---

# DP.M.212 Маппинг Discourse webhook в IWE event pipeline

## Вход

Webhook POST от Discourse с заголовком `X-Discourse-Event: post_created`.

## Алгоритм

1. Верификация подписи: `X-Discourse-Event-Signature: sha256=<hmac>` — тот же HMAC-паттерн что у GitHub и Chatwoot
2. Development mode: если секрет не задан — пропускать проверку подписи
3. Маппинг события: `post_created` → `club_post_created`
4. Создание события в pipeline: `post_event(external_id=discourse-{event_type}-{event_id})`
5. Нераспознанные события → return 200 OK молча (не ошибка)
6. Username не привязан к аккаунту → 200 без ошибки (mapping просто отсутствует)

## Идемпотентность

Через `external_id` — дубли gateway отбрасывает автоматически.

## Применимость

Любая внешняя платформа с webhook + `X-{Platform}-Event` header → IWE event pipeline.

## Связи

- Failure mode: DP.FM.101 rule-engine NOOP при отсутствии записи
- Источник: WP-327 Этап 23, 2026-05-28
