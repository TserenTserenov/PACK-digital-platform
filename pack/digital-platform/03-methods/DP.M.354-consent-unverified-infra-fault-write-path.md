---
id: DP.M.354
name: "consent_unverified infra-fault write-path"
name_ru: "consent_unverified как статус инфра-сбоя: пишем с меткой, не блокируем"
name_en: "consent_unverified as infra-fault status on write-path"
summary: "При недоступности consent-DB на write-path: записывать с меткой consent_unverified, не блокировать запись. Infra-сбой ≠ policy-deny. Fail-closed при infra-сбое = SPOF. Downstream аудитор видит метку и принимает решение."
type: method
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: 2
category: consent-handling
valid_from: 2026-06-20
related:
  see_also: [DP.M.298, DP.M.352, DP.D.035]
tags: [consent, infra-fault, fail-open, write-path, audit, consent-unverified, spof]
source: "WP-427 Ф5, DP.SC.182, peer-session 2026-06-19, Учётчик следов design; инцидент 12 июня 2026"
schema_version: 1
---

# DP.M.354 — consent_unverified как статус инфра-сбоя на write-path

## Описание

При записи следа (write-path ingestion) consent-DB может быть недоступна (pool exhausted, timeout, DB restart). Это **infra-сбой**, а не policy-deny. Различение критично для дизайна:

- **Policy-deny** (consent не дан) → fail-closed, запись отклоняется
- **Infra-fault** (consent-DB недоступна) → записать с меткой `consent_unverified`, не блокировать

Fail-closed при infra-сбое = SPOF: один флап consent-DB блокирует всю запись (инцидент 12 июня 2026).

## Vocabulary статусов Учётчика

| Статус | Значение |
|--------|----------|
| `stored` | Согласие подтверждено, запись сделана |
| `pending_review` | Ожидает ручной проверки |
| `pending_consent` | Согласие запрошено, ответа нет |
| `consent_unverified` | **Infra-сбой при проверке consent** |
| `expired_consent_block` | Согласие истекло, запись заблокирована (policy-deny) |
| `rejected` | Явный отказ (policy-deny) |
| `orphaned` | Запись без привязки к пользователю |
| `duplicate_skipped` | Пропущена как дубль |

## Algorithm

### При consent-DB доступна

```python
consent_status = check_consent(user_id, trace_type)
if consent_status == DENIED:
    return reject(trace)
write_trace(trace, status="stored")
```

### При consent-DB недоступна (infra-fault)

```python
try:
    consent_status = check_consent(user_id, trace_type, timeout=2s)
except (DBTimeout, PoolExhausted) as e:
    log_infra_fault(e)
    write_trace(trace, status="consent_unverified")  # не блокируем
    return
```

### Downstream аудит

```sql
SELECT * FROM traces WHERE status = 'consent_unverified' 
ORDER BY created_at DESC;
-- Аудитор решает: хранить, удалить, запросить consent ретроспективно
```

## When to use

- Write-path с проверкой consent через внешнюю DB
- Требования к availability выше, чем к немедленной consent-верификации
- Система с downstream-аудиторами (compliance, GDPR)

## Тест применимости

«Если consent-DB недоступна 30 секунд — сколько следов потеряно?»
- С `consent_unverified`: **Ноль** — все записаны с меткой
- С fail-closed: **Все за 30 секунд** — SPOF

## Anti-patterns

- **Fail-closed при infra-сбое**: один флап DB = полная потеря записи (инцидент 12 июня 2026)
- **Отсутствие метки**: запись без `consent_unverified` не позволяет ретроспективно аудитировать
- **Смешение статусов**: `consent_unverified` ≠ `pending_consent` (первый — infra-проблема, второй — ожидание пользователя)

## Связи

- DP.M.298 — fail-closed scope sidecar (когда fail-closed оправдан)
- DP.M.352 — pull-based offline-consent handover (смежный паттерн для split-storage)
- DP.D.035 — политика данных (consent categories)
