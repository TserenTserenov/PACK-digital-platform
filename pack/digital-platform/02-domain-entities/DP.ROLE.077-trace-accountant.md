---
id: DP.ROLE.077
name: Учётчик следов (trace-accountant)
type: role-description
status: draft
valid_from: 2026-06-19
summary: "Единственный authorized writer в learning.domain_event. Принимает следы от сенсорных адаптеров, применяет consent-guard, нормализует, маршрутизирует по route_catalog, управляет trace_stubs и reconciler-отчётом."
related:
  specializes: [U.RoleAssignment]
  component_of: [DP.ROLE.001]
  realizes: [DP.SC.182]
  upstream_from:
    - projection-worker      # читает domain_event → баллы
    - Навигатор/Портной      # читают дома знания
    - R15 Экстрактор         # принимает pending_review
  downstream_of:
    - сенсорные адаптеры     # отдают сырые следы
    - consent-хранилище      # предоставляет политику
    - route_catalog.yaml     # задаёт маршруты и dedup_window
created: 2026-06-19
wp: WP-427
---

# Учётчик следов (trace-accountant) — DP.ROLE.077

> # see DP.SC.182, DP.ROLE.077
>
> **Kind:** Platform Service Role — семантический слой ingestion. Не транспортный (транспорт = event-gateway); не доменный консультант (это Навигатор).
> **Owner Role:** IWE Platform. Два инстанса: облачный (Railway/CF Worker) + локальный (когда Local Gateway созреет).

---

## 1. Миссия

Быть единственной точкой, через которую следы пользователя попадают в дома знания — с проверкой consent, нормализацией типа, маршрутизацией в правильный дом и буферизацией при offline+restrictive.

Аналогия: таможня аэропорта. Сенсор-адаптер = самолёт, доставил груз (след). Таможня проверяет документы (consent), классифицирует груз (тип события), направляет в нужный склад (дом знания). При проблеме с документами — временное хранение (staging buffer), не выброс.

**Граница:** Учётчик не решает, ЧТО делать со следом после записи (это downstream-потребители), не маршрутизирует входящие запросы пользователя (это event-gateway), не хранит знания (это домы знания).

---

## 2. Обязанности

| Обязанность | Как выполняется |
|-------------|----------------|
| Принять сырой след от адаптера | API endpoint `POST /traces`; адаптер fire-and-forget |
| Применить consent-guard (write-path инвариант) | Читает политику из consent-хранилища; additive → продолжить; restrictive+offline → stub |
| Нормализовать тип следа | `event_type` из каталога по `sensor_id`; PII-фильтр B7.3 |
| Маршрутизировать в дом | Routing Gate DP.KR.001 §5; `target_home` из route_catalog |
| Дедуплицировать | `(user_id, event_type, timestamp)` в окне `dedup_window` из route_catalog → `duplicate_skipped` |
| Записать в `learning.domain_event` | Единственный writer (И2); явный факт → авто-запись; нечёткий → `pending_review` |
| Управлять `trace_stubs` | При `pending_consent`: INSERT stub; при reconnect-pull: UPDATE claimed; reconciler: expire |
| Reconciler-отчёт | Сканирует: orphaned (есть в сенсоре, нет в domain_event) + expired stubs; отчёт пилоту |

---

## 3. Входы / Выходы

**Входы (от адаптера):**
- `sensor_id: string` — откуда след (bot_note / vscode_session / bot_reflection / club_webhook / ...)
- `content: object` — сырое содержимое следа
- `timestamp: ISO-8601` — когда произошло
- `user_id: string` — чей след

**Входы (внешние конфигурации):**
- `consent-policy(user_id)` — из consent-хранилища
- `route_catalog.yaml` — маршруты + dedup_window per event_type
- `connection_status` — online / offline

**Выходы:**
- `capture_status` → адаптеру: `accepted_stored` / `accepted_pending` / `accepted_review` / `rejected`
- Запись в `learning.domain_event` (при `stored`)
- INSERT в `trace_stubs` (при `pending_consent`)
- Предложение в `/apply-captures` (при `pending_review`)
- Reconciler-отчёт пилоту (периодически)

---

## 4. Партиционированное И2

| Инстанс | Пишет в | НЕ пишет в |
|---------|---------|------------|
| Облачный | `learning.domain_event`, `trace_stubs` | `local-vault/staging/` |
| Локальный | `local-vault/staging/` | `learning.domain_event`, `trace_stubs` |

Клиентский адаптер = соединительный слой: при reconnect локальный инстанс сам пулит stubs от облачного.

**Migration scope:** И2 enforced after cut-over (Ф4). До cut-over legacy-writer'ы (`learning.club.*`, бот-рефлексия) допустимы time-bounded.

---

## 5. Полномочия

- **Write:** `learning.domain_event` (облачный), `trace_stubs` (облачный), `local-vault/staging/` (локальный)
- **Read:** consent-политика пользователя, `route_catalog.yaml`, `SensorAdapter.list_items()` (read-only к физическому сенсору)
- **Reconciler read-only:** к физическим сенсорам через адаптерный интерфейс (только `list_items`)
- **Запрещено:** читать/писать в дома знания напрямую (исполнители downstream делают это сами по `target_home` из команды Учётчика)

---

## 6. Связи с другими ролями

| Роль | Связь |
|------|-------|
| Сенсорные адаптеры | upstream → отдают сырые следы |
| R15 Экстрактор (DP.ROLE.002) | downstream → получает `pending_review`, принимает R15-решение |
| Projection-worker | downstream → читает `domain_event`, начисляет баллы |
| Навигатор/Портной | downstream → читают дома знания (PACK-personal, w_reflections, ...) |
| R1 Стратег | downstream (подотчётный) → получает reconciler-отчёт |
| Local Gateway (DP.IWE.005) | инфра → локальный инстанс работает поверх него |
| event-gateway | транспорт → маршрутизирует к Учётчику по prefix (не содержит логики захвата) |

---

## 7. Режим отказа

| Ситуация | Поведение |
|---------|----------|
| consent-хранилище недоступно | fail-open: `consent_unverified`; громкий алёрт; downstream может аудитировать |
| `learning.domain_event` недоступна | fallback-log локальный; reconciler обнаружит при восстановлении |
| Локальный инстанс недоступен (offline) | Облачный создаёт stub; TTL 72h; уведомление пилоту за 24h и 4h |
| TTL stub истёк | `expired_consent_block`; reconciler-отчёт; данные не теряются (payload_ref сохранён) |

Принцип: [[Явный policy-deny ≠ infra-сбой стража]] — каждый режим имеет явный статус.
