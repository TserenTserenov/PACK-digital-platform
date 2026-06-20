---
id: DP.SC.185
name: Владелец тира (Tier Authority)
type: sc
status: draft
layer: L2-Platform
summary: "Единственный authoritative-источник уровня доступа (traits.tier T0-T4). Вычисляет, хранит и поддерживает актуальность тира персоны по lifecycle-событиям: подписка, AI-клиент, GitHub, admin."
consumer: Шлюз (чтение GET /tier) · Бот (чтение GET /tier) · Onboarder (чтение) · Billing
provider: user-profile-service (L2-Platform)
valid_from: 2026-06-20
created: 2026-06-20
updated: 2026-06-20
related:
  replaces: [DP.SC.018]
  uses: [DP.ARCH.002, DP.ARCH.005]
  realized_by: [DP.ROLE.078]
wp: WP-430
decision: ADR-WP430-001
---

# [DP.SC.185] Владелец тира (Tier Authority)

> # see DP.ROLE.078

## Обещание

**Кому:** Шлюз, бот, онбордер и любой компонент платформы, которому нужен актуальный уровень доступа персоны.

**Что гарантирует:**

1. **Единый источник истины.** В `ory_identity.traits.tier` пишет ровно один компонент (user-profile-service). Шлюз, бот и worker являются читателями, не писателями.

2. **Актуальность.** Тир обновляется в течение 30 секунд с момента наступления lifecycle-события.

3. **Консистентность.** Тир, возвращаемый `GET /tier`, совпадает с `traits.tier` в Ory.

**Инвариант:** `grep` писателей `traits.tier` в codebase = 1.

## Триггеры и lifecycle

| Событие | Действие | Результат |
|---------|----------|-----------|
| Создание аккаунта | Установить T1 | traits.tier = T1 |
| Активация подписки | Upgrade → T2 | traits.tier = T2 |
| Истечение подписки | Downgrade → T1 | traits.tier = T1 |
| `mcp-signal` (AI-клиент подключён) + активная подписка | Upgrade → T3 | traits.tier = T3 |
| AI-клиент отключён | Downgrade → T2 (если есть подписка) | traits.tier = T2 |
| `github-signal` (GitHub подключён) + T3 | Upgrade → T4 | traits.tier = T4 |
| GitHub отключён | Downgrade → T3 | traits.tier = T3 |
| Admin-override | Установить явный тир (T0-T5) | traits.tier = указанный |

## Edge cases

**Гонка «сигнал раньше подписки»:**
Если `mcp-signal` пришёл до активации подписки → тир остаётся T1. Сигнал сохраняется как pending в `user_tier_signals` (таблица: user_id, signal_type, received_at, status: pending|applied). При активации подписки — применяются все pending-сигналы → автоматический upgrade T1→T3.

**Идемпотентность:**
Повторный `mcp-signal` (фликер) не меняет тир и не создаёт дублирующих событий.

**Downgrade на несколько ступеней:**
Если истекла подписка при T4 → T1 (не T3 или T2), т.к. T3/T4 предполагают активную подписку.

**T5 (admin):**
Специальный override для административного доступа. Не вычисляется автоматически, только явная запись admin-путём. Не сбрасывается lifecycle-событиями подписки/сигналов.

## Режим отказа

**Transient недоступность Ory:**
`GET /tier` возвращает cached-значение из таблицы `user_tier_cache` (хранит последний успешно записанный тир T0-T4, заполняется при каждом PATCH). Read-only, без записи в traits.
Кеш действителен 5 минут. После восстановления Ory — reconcile.
Примечание: `contract`-таблица (подписка) НЕ используется как fallback — она содержит только T2-статус и не отражает T3/T4.

**Постоянная недоступность Ory:**
Алёрт. Тир не обновляется, возвращается cached.

## SLA

- Обновление traits.tier: P95 ≤ 30 секунд с момента события.
- `GET /tier`: P95 ≤ 200ms.
- Uptime: 99.5% (следует Railway deploy).

## Связи с другими SC

- **Заменяет** DP.SC.018 (Переход T3→T4) в части записи tier — SC.018 остаётся как UX-сценарий.
- **Использует** DP.ARCH.002 (service tiers definition) как источник семантики T0-T4.
- **Реализуется** DP.ROLE.078 (Tier Authority role).
