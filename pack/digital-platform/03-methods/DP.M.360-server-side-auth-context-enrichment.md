---
id: DP.M.360
title: "Server-side enrichment данных с auth-контекстом"
type: method
domains: [api-design, architecture, auth, enrichment]
trust: confirmed
epistemic_stage: validated
valid_from: 2026-06-04
source: WP-398 peer-session 36 (team-mode), commit b222c762
pack_refs:
  - DP.M.244 (Trust Boundary Server-Side — смежный: security enforcement)
---

# DP.M.360 — Server-side enrichment данных с auth-контекстом

## Суть

Данные, требующие авторизационного контекста для обогащения, должны обогащаться **на сервере**. Клиент получает готовые данные без знания об auth-инфраструктуре.

## Почему не в клиенте

1. Клиент вынужден знать схему аутентификации (DATABASE_URL? Kratos? Ory?) — утечка auth-знания в client-слой.
2. N клиентов = N lookup-путей → drift → несоответствие отображаемых данных.
3. Обновление auth-схемы требует обновления всех клиентов вместо одного server-side lookup.

## Паттерн реализации

```
Server (API handler) → lookup-функция внутри handler
                     → подтягивает display_name / role / email через auth-источник
                     → API возвращает {agent, display_name, status}

Client (CLI/dashboard) → получает готовые данные
                        → НЕ знает о DATABASE_URL / Kratos / Ory
```

## Тест

«Знает ли клиент о схеме auth?» Да → server-side enrichment недоделан.

## Связь

- DP.M.244 — смежный: auth-enforcement в gateway (security gate, не обогащение данных)
