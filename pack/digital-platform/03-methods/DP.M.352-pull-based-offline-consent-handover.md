---
id: DP.M.352
name: "Pull-based offline-consent handover"
name_ru: "Pull-based handover: stub+pull вместо push для offline-consent"
name_en: "Pull-based handover for offline-consent split-storage"
summary: "При split cloud/local storage с ограниченным consent-режимом: облако пишет stub+TTL, возвращает 'принято в ожидании'; локальный инстанс при reconnect вытягивает и подтверждает. Адаптер stateless. Альтернатива push = потеря данных при offline."
type: method
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: 2
category: consent-handling
valid_from: 2026-06-20
related:
  see_also: [DP.D.035, DP.D.028, DP.M.298]
tags: [consent, offline, split-storage, pull-pattern, stub, ttl, stateless-adapter]
source: "WP-427 Ф5, peer-session 2026-06-19, Учётчик следов design"
schema_version: 1
---

# DP.M.352 — Pull-based handover: stub+pull вместо push для offline-consent

## Описание

При проектировании системы с split cloud/local storage и ограниченным consent-режимом используй pull-based handover вместо push. Адаптер stateless — «выстрелил и забыл». Облако держит стаб с TTL; локальный инстанс при reconnect вытягивает и подтверждает.

Применимо к любой системе с двумя хранилищами (cloud + local) и периодическим offline одного из них.

## Algorithm

### Step 1: Ingestion (адаптер → облако)

1. Адаптер отправляет след в облачный Учётчик
2. Учётчик проверяет consent (если consent_db доступна) → пишет в `trace_stubs` с TTL=72h
3. Учётчик возвращает статус `pending` (принято в ожидании)
4. Адаптер завершает работу — ничего не хранит

### Step 2: Reconciliation (локальный инстанс → облако)

1. При reconnect локальный инстанс вызывает `GET /stubs?pending=true&agent_id={id}`
2. Получает список pending stubs (id, trace_data, expires_at)
3. Записывает в local-vault
4. Подтверждает: `DELETE /stubs/{id}` (или `POST /stubs/{id}/ack`)

### Step 3: Reconciler cleanup

1. Каждые 6ч reconciler помечает stubs старше TTL как `expired`
2. При `expired` → уведомление пилоту (slack/telegram): «N следов не забраны, истёк TTL»
3. Expired stubs не удаляются автоматически — требуют явного решения (хранить / удалить)

## When to use

- Система с cloud + local storage (VS Code extension + cloud backend)
- Пользователь может быть offline в момент записи
- Ограниченный consent-режим: не всё пишем сразу
- Адаптер не должен иметь постоянную память

## Тест применимости

«Если пользователь offline 2ч между Stop-хуком и следующим подключением — теряется ли след?»
- При push: **Да** — потеря
- При pull-based handover: **Нет** — stub ждёт в облаке до TTL

## Anti-patterns

- **Push-only**: Stop-хук пишет локально → при offline данные исчезают (нет persistent storage у хука)
- **Stateful adapter**: адаптер хранит очередь → растёт, нужна персистентность, усложняет деплой
- **TTL слишком короткий**: sync-window < TTL → stubs истекают до reconnect; минимум 24ч для активных пользователей

## Связи

- DP.D.035 — политика данных (consent categories, TTL policy)
- DP.D.028 — тирование данных пользователя
- DP.M.298 — fail-closed scope sidecar (паттерн изоляции scope при infra-сбое)
