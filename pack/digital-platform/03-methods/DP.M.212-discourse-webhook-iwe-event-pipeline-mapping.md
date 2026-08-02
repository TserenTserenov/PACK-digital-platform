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
last_updated: 2026-08-01
---

# DP.M.212 Маппинг Discourse webhook в IWE event pipeline

## Вход

Webhook POST от Discourse с заголовком `X-Discourse-Event: post_created`.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Совместимость внешних webhook-форматов ↔ внутренняя единообразная семантика событий | Discourse-специфичные `X-Discourse-Event` и payload нужно транслировать в общий pipeline (`club_post_created`); удобство внутренней модели требует слоя перевода, который усложняет отладку интеграции |
| Безопасность проверки подписи ↔ удобство development-режима | HMAC-проверка защищает production, но её отключение для локальной разработки создаёт риск переноса небезопасной конфигурации в боевое окружение |
| Тихое игнорирование нераспознанных событий ↔ явная сигнализация об ошибке | Возврат 200 для неизвестных событий и отсутствующих username'ов предотвращает шум внешнему сервису, но делает менее заметным дрейф схемы событий и неполноту привязки аккаунтов |

## Алгоритм

1. Верификация подписи: `X-Discourse-Event-Signature: sha256=<hmac>` — тот же HMAC-паттерн что у GitHub и Chatwoot
2. Development mode: если секрет не задан — пропускать проверку подписи
3. Маппинг события: `post_created` → `club_post_created`
4. Создание события в pipeline: `post_event(external_id=discourse-{event_type}-{event_id})`
5. Нераспознанные события → return 200 OK молча (не ошибка)
6. Username не привязан к аккаунту → 200 без ошибки (mapping просто отсутствует)

## Идемпотентность

Через `external_id` — дубли gateway отбрасывает автоматически.

## Bias-Annotation

_Куда систематически съезжает внимание практикующего. Epistemic stage карточки — `empirical`._

| Bias | Direction of distortion |
|------|--------------------------|
| Недооценка development-режима | Внимание уходит к быстрому локальному тестированию без проверки подписи; такая конфигурация незаметно мигрирует в production, где webhook-запросы становятся непроверяемыми |
| Игнорирование событий без username | Пропуск username-mapping трактуется как «не ошибка», что смещает фокус с мониторинга полноты привязки аккаунтов; накапливаются «тихие» потери событий |
| Паттерн «200 OK всегда» | Стремление подавить все ошибочные ответы упрощает внешний интерфейс, но затрудняет observability: внешний сервис не получает сигнала о сломанной интеграции |

## Применимость

Любая внешняя платформа с webhook + `X-{Platform}-Event` header → IWE event pipeline.

## Связи

- Failure mode: DP.FM.101 rule-engine NOOP при отсутствии записи
- Источник: WP-327 Этап 23, 2026-05-28

---
> 2026-08-01 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 6). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
