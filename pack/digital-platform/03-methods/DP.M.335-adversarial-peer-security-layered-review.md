---
id: DP.M.335
name: "Adversarial Layered Review for Security-Critical Components"
name_ru: "Адверсариальное послойное ревью для security-критичных компонентов"
summary: "Для security-critical компонентов первое «полное» решение — baseline для adversarial review, не финал. Peer последовательно ищет attack surface в принятом fix; каждый новый fix открывает следующую поверхность. 2-3 раунда существенно меняют архитектуру решения."
type: method
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: 2
category: security-review
valid_from: 2026-07-06
related:
  see_also: [DP.M.162]
tags: [adversarial-peer, security-review, immutable-storage, second-order-holes, layered-review, audit-trail]
source: "session-close 2026-07-03, sessions/2026-07/2026-07-03-07-wp457-f12-hash-chain/report.md (К1, ходы 3-10; Решение 3)"
schema_version: 1
---

# DP.M.335 — Адверсариальное послойное ревью для security-критичных компонентов

## Описание

Для security-critical компонентов (иммутабельные хранилища, consent-механизмы, audit trail, cryptographic boundaries) первое «полное» решение является baseline для adversarial review, а не финалом. Peer не предлагает альтернативу — он ищет способ обойти текущее решение. Каждый принятый fix открывает следующую поверхность атаки, которую peer немедленно проверяет.

## When to use

- Security-critical компонент: иммутабельное хранилище, consent-механизм, audit trail, cryptographic boundary
- Первая версия защиты согласована и принята
- Нужна проверка: достаточна ли защита против adversarial actor

## Algorithm

### Step 1: Написать первую версию защиты

Стандартная первая версия: REVOKE прав + один административный путь обхода. Это baseline, не финальное решение.

### Step 2: Adversarial peer проверяет attack surface

Peer задаёт вопрос: «Кто может обойти эту защиту без использования административного пути?»

Типичные второй-порядок атаки:
- **Владелец схемы** не ограничен REVOKE → второй слой: безусловный триггер
- **tip-only удаление** не защищает атомарность откатов → откат в той же транзакции
- **audit-таблица** не защищена тем же режимом → применить REVOKE+триггер и к ней
- **безусловный триггер** блокирует легитимный admin-путь → GUC-контекст для обхода

### Step 3: Принять fix, повторить раунд

Каждый принятый fix становится новым baseline. Повторить Step 2 для новой архитектуры.

**Эмпирическое правило:** 2-3 раунда adversarial review существенно меняют архитектуру решения. Планировать как minimum 2 раунда для security-critical компонентов.

### Step 4: Остановиться при закрытии поверхности

Завершить, когда на вопрос «кто может обойти?» ответ требует взлома infrastructure layer (не application layer).

## Инвариант

Adversarial peer НЕ предлагает альтернативу с нуля — он ищет способ обойти конкретное решение: «владелец схемы всё равно может удалить без лога» → второй слой.

## Тест применимости

«Это security-critical компонент с требованием к tamper-resistance?»
- Да → применять DP.M.335 (adversarial layered review)
- Нет (методологический текст, документация) → DP.M.162 (adversarial critique methodology)

## Отличие от DP.M.162

DP.M.162 — adversarial peer review для методологических текстов: проверка полноты аргументации, устранение профессионального жаргона, применимость для нового пользователя. DP.M.335 — для security-critical кода/архитектуры: поиск attack surface second-order holes.

## Связи

- DP.M.162 (Adversarial Peer Review для методологических текстов) — DP.M.335 является специализацией для security-critical компонентов
- DP.D.191 (Mitigation ≠ Fix) — adversarial review помогает различить mitigation и fix
