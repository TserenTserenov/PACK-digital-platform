---
id: DP.D.135
name: "Метод входа (authentication method) ≠ LLM-аккаунт (ресурсная подписка)"
type: distinction
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-06-14
created: 2026-07-19
source: "миграция из 01B-distinctions.md (РП170 Ф2, 2026-07-19)"
see_also: []
schema_version: 1
---

# DP.D.135: Метод входа (authentication method) ≠ LLM-аккаунт (ресурсная подписка)

При проектировании multi-method login возникает соблазн связать «каким методом вошёл» с «какую LLM использует». Это смешение двух ортогональных осей.

| Метод входа | LLM-аккаунт |
|-------------|-------------|
| Какой идентификатор использован для логина (email, Google, Apple, BYOK-код) | Какая подписка или ключи используются для модели (Claude subscription, BYOK-ключи) |
| Ось идентификации: Ory/Kratos anchor | Ось ресурсов: billing, квоты, провайдер модели |
| Несколько методов → одна identity (account linking, стандартный паттерн) | Один billing-аккаунт → одна LLM-квота |
| Управляется identity provider (Ory Kratos) | Управляется billing системой (отдельно от IdP) |
| Смена метода входа не меняет ресурсы | Смена LLM-аккаунта не меняет identity |

**Тест:** «Смена метода входа меняет ли LLM-квоту?» Нет → оси независимы. Если отвечаешь «да» — смешиваешь оси.

**Ложный отказ от account linking:** «Добавить Google-логин = создать второй аккаунт» — ошибочно, если у Ory уже есть identity с этим email. Account linking привязывает новый метод входа к существующей identity, LLM-аккаунт не затрагивается.

**Связи:** DP.D.087 (OAuth state storage — смежно), DP.D.134 (Logout ≠ OAuth-grant revocation — другой аспект Ory sessions)

**Источник:** session-transcript 2026-06-12 + git diff IWE/.claire/rules/distinctions.md (38b1205, WP-411 Ф5)
