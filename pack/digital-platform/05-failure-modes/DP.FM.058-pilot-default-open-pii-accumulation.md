---
id: DP.FM.058
name: "Pilot-инсталляция с открытым дефолтом = silent PII-accumulation"
type: failure-mode
status: active
trust: tier-2
epistemic_stage: documented
valid_from: 2026-05-20
domains: [security, gdpr, deployment, pilot]
related: [DP.FM.047, B7.3-security-gate]
---

# DP.FM.058 — Pilot-инсталляция с открытым дефолтом → silent PII-accumulation

## Симптом

Pilot/staging-инсталляция работает с deployment-флагом в режиме «open» (например, `MAINTENANCE_MODE=false`, `ENABLE_ACCOUNT_SIGNUP=true`, `CONSENT_GATE=off`) без активного consent-grant-механизма. Прямые ссылки на бота/портал остаются достижимыми → пользователи входят и оставляют PII (telegram_id, email, user-state) → платформа собирает данные без записи в consent-store.

## Механизм

1. Платформенная установка флага: дефолт production-окружения переиспользуется на pilot без override
2. Consent-механизм не активирован (на pilot ещё не подключён feature-gate)
3. Прямые ссылки на бота/портал остаются достижимыми (бот зарегистрирован, домен живой)
4. Внешние пользователи находят точку входа (через автора, паблик, индексацию)
5. Бот пишет в `bot_data.public.users` + `development.user_state` без проверки `learning.tracking_consent`
6. Cumulative GDPR-долг линейно растёт по времени без видимого сигнала

## Прецедент (2026-05-20)

`@aist_pilot_bot` с `MAINTENANCE_MODE=false` накопил **≥160 пользователей** в `bot_data.public.users` + `development.user_state` без соответствующих записей в `learning.tracking_consent` / `consent_grant` за период январь–май 2026 (4+ месяца). Обнаружено случайно через bug-report 20 мая; коммиты ремедиации — `9b59cccc`, `25142681` (cleanup anarchuk).

## Детектор

Cross-table audit:

```sql
SELECT count(*)
FROM bot_data.public.users u
LEFT JOIN learning.tracking_consent c ON c.user_uuid = u.user_uuid
WHERE c.user_uuid IS NULL AND u.created_at < now() - interval '1 day';
```

Ненулевая дельта → open-loop accumulation. Дополнительно — periodic `users \ consent_grant` diff с алертом при росте >0 per day.

## Профилактика

1. **Default-closed для pilot:** до GA любая инсталляция стартует с `MAINTENANCE_MODE=true` / `ENABLE_ACCOUNT_SIGNUP=false` / `CONSENT_GATE=on`. Открытие — только явным релиз-гейтом.
2. **Pre-deployment audit:** перед stage→prod флипом — checklist «закрыт ли open-loop сбор PII?»
3. **Continuous detector:** запланированный cross-table audit (alerter), pager-level severity при дельте > N.
4. **Security Gate (B7.3) на каждом ArchGate** для PII-touching РП — ответить на §Б чеклист до реализации.

## Область применимости

Multi-tenant платформы с pilot/staging/production окружениями, где обработка PII контролируется feature-flags или deployment-флагами. Не ограничено GDPR-юрисдикцией — применимо к любым PII compliance frameworks (LGPD, CCPA, PIPEDA).

## Связи

- **B7.3 Security Gate** — обязательная проверка для PII-touching РП (CLAUDE.md §2)
- **DP.FM.047** — third-party PII vendor gate (родственная FM, другая ось: vendor vs deployment-flag)
- **WP-212** — текущий security audit cadence
