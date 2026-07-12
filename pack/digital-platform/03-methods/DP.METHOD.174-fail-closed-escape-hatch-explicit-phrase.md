---
id: DP.METHOD.174
name: "Аварийный рычаг fail-closed: явная фраза-подтверждения, не булев флаг"
type: method
pack: PACK-digital-platform
domain: digital-platform / security
kind: Method
status: active
created: 2026-07-07
trust: confirmed
epistemic_stage: observed
valid_from: 2026-07-07
sources:
  - "peer-session 2026-07-07-06-wp454-panel-writer-role"
  - "WP-454: fail-closed при отсутствии кредов новой роли БД"
related:
  see_also: [DP.METHOD.164]
schema_version: 1
---

# DP.METHOD.174 — Аварийный рычаг fail-closed: явная фраза-подтверждения

## Определение

Escape hatch для возврата к legacy-правам в security-критичном сервисе должен требовать **буквальную фразу-подтверждения**, а не булев флаг (`ENV=true`). Каждое использование рычага оставляет видимую запись-инцидент в журнале.

## IPO

- **Вход:** попытка запустить сервис с включённым аварийным рычагом
- **Процесс:** проверка literal confirmation phrase → логирование инцидента → откат к legacy-правам
- **Выход:** сервис запущен + запись `[INCIDENT] emergency lever activated by {source} at {timestamp}` в журнале

## Свойства метода

1. **Explicit intent:** фраза (`EMERGENCY_LEVER=i-confirm-rollback-to-legacy-role`) случайно не устанавливается и не копируется из старого конфига.
2. **Audit trail:** каждый запуск с рычагом оставляет visible incident record — «может ли откат произойти незаметно?» → нет.
3. **No silent inheritance:** булев флаг `true` копируется из env-шаблона и остаётся включённым незаметно; фраза — нет.

## Когда применять

- Security-критичные сервисы с принципом минимальных прав (fail-closed default)
- Любой escape hatch, где несанкционированное включение = security risk
- Infrastructure automation с правами на БД/файловую систему

## Тест

«Может ли аварийный рычаг включиться при копировании .env из шаблона или при обновлении конфига?»
- Да (булев флаг) → заменить на literal confirmation phrase
- Нет (фраза) → правильно

## Различение с DP.METHOD.164

| Метод | Проблема | Решение |
|-------|---------|---------|
| DP.METHOD.164 | Неизвестный режим → fail-open | Refuse to start при unknown MODE |
| DP.METHOD.174 | Аварийный откат → незаметен | Explicit phrase + incident log |

## Источник

WP-454: сервис с `LEAST_PRIVILEGE=true` (новая роль БД с минимальными правами) + аварийный рычаг для возврата к старым правам при инциденте. Проблема: `EMERGENCY_ROLLBACK=true` случайно оставался в конфиге из предыдущего деплоя.
