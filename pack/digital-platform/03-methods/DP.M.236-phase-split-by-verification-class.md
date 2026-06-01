---
id: DP.M.236
name: "Разделение фазы РП по классу верификации (trivial/closed-loop/open-loop/problem-framing)"
type: method
status: draft
valid_from: 2026-05-30
pack_refs:
  - DP.M.060  # atomic VDV step — atomic-фаза по одному классу
  - DP.M.163  # deferred-phase-finalization-checkpoint — отложенная финализация
domains: [wp-management, phase-decomposition, verification-classes]
---

# DP.M.236 — Разделение фазы РП по классу верификации

## Описание

Метод декомпозиции фазы РП по характеристике верификации каждой под-задачи (trivial / closed-loop / open-loop / problem-framing). Closed-loop + trivial закрываются в текущей сессии; problem-framing выделяется в отдельную фазу с pre-articulated открытыми вопросами и маркером «не блокирует closed-loop».

## Когда применять

- Фаза РП содержит ≥3 под-задачи разных классов верификации
- Closed-loop fix готов, но policy-decision ждёт peer-session / ArchGate
- Trivial lesson обновлён, но широкий runtime gate требует обсуждения

## Антипаттерн

Держать зонтичную фазу `pending` пока завершится самая медленная под-задача (problem-framing). Closed-loop fix теряется в backlog, runtime-gate ждёт debate, lesson не маршрутизируется в memory/.

## Алгоритм

1. Перед close/review фазы — пройти список под-задач и классифицировать каждую:
   - **trivial** — обновление документа, лога, lesson-файла
   - **closed-loop** — runtime check, smoke-test, verifiable в той же сессии
   - **open-loop** — требует данных за пределами сессии (метрики, бэкап, A/B)
   - **problem-framing** — требует policy-решения или peer-discussion
2. Закрыть **trivial + closed-loop** под-задачи в текущей сессии (`status: done`)
3. **Open-loop** → checkpoint (см. DP.M.163), ждать данных
4. **Problem-framing** → выделить **отдельную фазу** с маркером:
   - `target_close: TBD (не блокирует <RPA1>)`
   - pre-articulated открытые вопросы (см. DP.M.238)

## Инвариант

- Closed-loop fix не блокируется ожиданием problem-framing
- Каждая под-задача завершается в фазе своего класса, не в зонтичной
- Pre-articulated вопросы фиксируются при выделении problem-framing phase, не позже

## Тест применимости

«Можно ли сделать эту под-задачу до peer-session без новой информации?» Да → closed-loop, делать сейчас. Нет → выделить в отдельную фазу с явными открытыми вопросами.

## Источник

WP-7 RPA → RPA-policy split, сессия 2026-05-30 (commit `86e2388f`). Зонтичная фаза RPA блокировала closed-loop RPA1 + trivial RPA4 ожиданием problem-framing RPA2/RPA3 → split на RPA + RPA-policy.
