---
id: DP.M.280
type: method
domain: digital-platform
pack: PACK-digital-platform
trust: validated
epistemic_stage: 3
valid_from: 2026-06-05
source: WP-330 (session-close 2026-06-05, session-transcript 2026-06-05-04)
---

# DP.M.280 — Allow-Fallback Cutover Pattern

**Назначение:** безопасная миграция потока при наличии живых пользователей в старом состоянии (deprecated SM).

## Проблема

При переключении с machine state M (старый) на поток N (новый) существующие пользователи без записи в N оказываются в тупике: старый путь закрыт, новый не начат.

## Паттерн

Центральный гейт `try_deliver_new_flow(allow_fallback: bool)` проверяет наличие прогресса пользователя в новом потоке:

**`allow_fallback=False` (основные входы — намеренное действие пользователя):**
- Нет прогресса → показать подсказку `/start_new_flow`, без автостарта.
- Пользователь сам инициирует начало нового потока.

**`allow_fallback=True` (автоматические входы — push-доставка планировщика):**
- Нет прогресса в новом потоке → доставить контент из deprecated потока.
- Гарантирует: пользователь получает что-то (доверие сохраняется), не получает ошибку.

## Разделение по типу входа

| Тип входа | allow_fallback | Обоснование |
|-----------|----------------|-------------|
| Намеренный (кнопка, команда) | False | Пользователь готов к новому флоу |
| Автоматический (push, scheduler) | True | Контент должен приехать |

## Lifecycle deprecated пути

После периода deprecated:
1. Всем оставшимся SM-пользователям — auto-migrate или explicit migration prompt
2. Убрать `allow_fallback=True` ветку в той же уборке кода
3. Удалить deprecated SM

## Тест завершения миграции

`count(users WITH old_sm_state AND NOT new_flow_progress) == 0` → deprecated путь можно удалять.

## Связи

- Паттерн применён: WP-330 /learn cutover
- Связан с: DP.M.279 (HELD-pattern — для случая, когда deprecated удаление заблокировано)
