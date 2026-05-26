---
id: DP.FM.084
name: "OAuth+CDN миграция без redirect_uri pre-flight: полный outage вместо частичного"
type: failure-mode
pack: PACK-digital-platform
domain: infrastructure-migration
trust: 0.85
epistemic_stage: confirmed
valid_from: 2026-05-26
source: WP-355 production deploy (peer-сессия 2026-05-26-05-wp355-ory-cf-access, report-draft.md строки 12-14)
---

# DP.FM.084 — OAuth+CDN миграция без redirect_uri pre-flight: полный outage вместо частичного

## Описание

Failure mode миграции домена через CDN-прокси (Cloudflare, AWS CloudFront), когда callback-URL OAuth-провайдера зависит от точного URL. Конструкция:

1. DNS proxy включается (CDN перехватывает все запросы по домену).
2. OAuth-провайдер (ORY, Auth0, Keycloak) не получил обновлённый `redirect_uri` или ещё не подтвердил совместимость SSL-mode (Full strict vs Flexible).
3. Все callbacks с новым URL → reject от OAuth-провайдера → полный outage авторизации.

Без proxy миграция могла бы быть частичной (grace для существующих пользователей через старый URL).

## Симптом

- После включения DNS proxy все попытки логина выдают `redirect_uri mismatch` / SSL-handshake-fail.
- Существующие сессии работают, новые логины — нет.
- Outage начинается с момента DNS flip и продолжается до ручного отката proxy.

## Причина

Порядок шагов миграции: DNS proxy включён ДО pre-flight верификации OAuth-провайдера. Anti-pattern — «сначала переключим трафик, потом проверим».

## Профилактика

Шаг 0 миграции — параллельный pre-flight блок ДО любых DNS-изменений:

1. **ORY client config:** GET `client_id` → проверить, что `redirect_uri` содержит новый URL.
2. **SSL mode:** проверить CDN SSL mode (Full strict vs Flexible) совместим с origin-сертификатом.
3. **Origin доступность:** прямой curl к origin (минуя CDN) с новым URL — 200 OK.

Только при всех зелёных → включать DNS proxy.

## Применимость

Любая миграция «домен меняется + проксируется через CDN + OAuth-callback зависит от точного URL»:
- Cloudflare Access / Tunnel
- Auth0 / Keycloak / ORY Hydra
- AWS Cognito + CloudFront

## Тест

«Может ли OAuth-провайдер reject новый URL?» Да → pre-flight обязателен. Нет (например, OAuth не используется) → FM неприменим.

## Связи

- WP-355 (production deploy ORY + Cloudflare Access)
- DP.SC.* (миграция доменов — service clauses, если будут формализованы)
