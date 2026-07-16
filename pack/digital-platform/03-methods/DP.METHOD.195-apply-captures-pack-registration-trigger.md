---
id: DP.METHOD.195
name: "Apply-captures session как триггер регистрации нового Pack и применения defer-кандидатов"
type: method
pack: PACK-digital-platform
domain: digital-platform / knowledge-extraction
kind: Method
status: active
created: 2026-07-15
trust: confirmed
epistemic_stage: observed
valid_from: 2026-07-10
sources:
  - "peer-session 2026-07-10-14-apply-captures-reject-defer + DS-ai-systems commit d073ec2 + PACK-rhetoric commit 6d17f27"
schema_version: 1
---

# DP.METHOD.195 — Apply-captures session: органический триггер создания нового Pack

## Паттерн

Накопление **3+ defer-кандидатов** с одинаковым «предполагаемым Pack, которого нет в routing-vocab» → сигнал для регистрации нового Pack прямо в сессии, без отдельного РП.

## IPO

- **Вход:** apply-captures session, 3+ defer-кандидата с одинаковым "предполагаемым Pack"
- **Процесс:**
  1. Зарегистрировать Pack в `routing-vocab.md` (путь + аббревиатура + исключения)
  2. Зафиксировать в `DS-ai-systems/extractor/config/routing.md`
  3. Вернуться к defer-кандидатам — применить как accept
- **Выход:** Pack зарегистрирован; N накопленных defer → N немедленных accept в той же сессии

## Ценность

Разблокировка KE-очереди **без отдельного РП** на «создать Pack».

**Прецедент:** PACK-rhetoric — не существовал в routing-vocab; 8+ кандидатов в defer; на сессии apply-captures зарегистрирован и немедленно применены все.

## Граница применимости

- Только при 3+ defer одного домена (не одиночный defer)
- Репо Pack должен уже существовать (или готов к созданию за 5 мин)
- Не применять как замену явному планированию нового Pack с нуля
