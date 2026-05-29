---
id: DP.FM.098
name: SM-Mutex Guard Coverage Gap — Queue-Based Flows Bypass Guard
type: failure-mode
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: formalized
valid_from: 2026-05-28
source: session-transcript 2026-05-28 (WP-358 Fix 4 Root Cause B)
---

# DP.FM.098 — SM-Mutex Guard Coverage Gap: Queue-Based Flows Bypass Guard

## Описание

SM-mutex guard (`_sm_is_expecting_reply`) проверяет признак `current_state IS NOT NULL`. Это покрывает только flows, которые явно проходят через State Machine (устанавливают state). Queue-based flows (MarathonQueue, webhook, batch-intake) могут иметь pending-ответ, не устанавливая SM-state → guard ложно возвращает `False` и пропускает чужое сообщение.

## Симптом

Mutex работает корректно для SM-инициированных диалогов, но не блокирует параллельные сообщения при queue-based flows. Для новых участников марафона/онбординга guard всегда `False` → сообщения проходят без блокировки.

## Диагностика

```bash
# Проверить: все ли flow-типы устанавливают признак, который проверяет guard
grep -n "_sm_is_expecting_reply\|current_state" <bot_handler.py>
grep -n "MarathonQueue\|queue_send\|webhook" <bot_handler.py>
```

Если queue-send не устанавливает `current_state` → FM активен.

## Паттерн guard-audit

При добавлении нового flow-типа (очередь, вебхук, cron-триггер, batch) — явно проверить покрытие каждого guard, управляющего mutex'ом ответа.

**Фикс-паттерн:** расширить guard отдельным Check для нового flow типа (`has_recent_queue_sent`, `has_pending_webhook_reply`), **не** пытаясь унифицировать с SM-state. Каждый flow-путь → явный boolean в guard.

## Применимо

FSM-based Telegram-боты и любые системы с mutex'ом ответа, когда flow-путей несколько (SM + очереди + вебхуки).

## Связанные FM

- DP.FM.090 — Ordinal guard vs semantic role в turn-dispatcher
