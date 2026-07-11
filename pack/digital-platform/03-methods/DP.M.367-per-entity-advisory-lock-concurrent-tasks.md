---
id: DP.M.367
name: "Per-Entity Advisory Lock for Concurrent Background Tasks"
name_ru: "Блокировка по идентификатору объекта для конкурирующих фоновых задач"
name_en: "Per-entity advisory lock prevents race conditions between concurrent background workers"
summary: "Два и более фоновых процесса, способных одновременно мутировать один и тот же объект, требуют advisory lock по идентификатору объекта. SELECT ... FOR UPDATE SKIP LOCKED обеспечивает serial execution для одного объекта без deadlock при параллельной обработке очереди."
type: method
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: 2
category: concurrency
valid_from: 2026-07-02
related:
  see_also: [DP.M.206]
tags: [advisory-lock, concurrency, background-tasks, race-condition, postgresql, select-for-update, skip-locked]
source: "WP-446 Ф3b, session-transcript 2026-07-02-15, commit 5c3f8e5fa (DS-IT-systems)"
schema_version: 1
---

# DP.M.367 — Блокировка по идентификатору объекта для конкурирующих фоновых задач

## Описание

Два фоновых процесса, способных одновременно действовать на один и тот же объект, требуют per-entity advisory lock. Без блокировки оба процесса читают состояние до любых изменений и производят несовместимые side effects.

## Пример

WP-446 Ф3b: `check_payment` (подтверждение оплаты) и `expire_reservation` (откат просрочки) могут одновременно обрабатывать один резерв бонусов. Если подтверждение оплаты чуть медленнее откатa — пользователь получает и доступ к курсу, и возврат бонусов (двойная выгода).

## Algorithm

### Step 1: Взять advisory lock по entity_id перед мутацией

```sql
SELECT id FROM reservations
WHERE id = $payment_id
FOR UPDATE SKIP LOCKED;
```

Если запись заблокирована другим процессом — `SKIP LOCKED` возвращает пустой результат → этот воркер пропускает объект (обработает другой воркер или следующий cron-цикл).

### Step 2: Выполнить мутацию внутри транзакции

```python
with db.transaction():
    row = db.query(
        "SELECT id FROM reservations WHERE id = %s FOR UPDATE SKIP LOCKED",
        (payment_id,)
    ).fetchone()
    if row is None:
        return  # другой процесс обрабатывает сейчас
    # применить изменения
    db.execute("UPDATE reservations SET status = %s ...", ...)
```

### Step 3: Lock освобождается автоматически

Advisory lock снимается при commit/rollback транзакции.

## When to use

При наличии двух и более фоновых процессов (cron, worker, event handler), которые:
- Читают и модифицируют один и тот же объект в БД
- Могут запускаться одновременно
- Производят несовместимые side effects (выдача доступа + откат средств)

## Тест применимости

«Могут ли два процесса одновременно читать один объект и производить несовместимые изменения?»
- Да → per-entity advisory lock обязателен
- Нет → можно без блокировки

## Отличие от DP.M.206

DP.M.206 описывает advisory lock для управления одним процессом-singleton (session-scoped). DP.M.367 — per-entity lock для сериализации конкурирующих воркеров на множестве объектов в очереди (queue pattern).

## Связи

- DP.M.206 (Fast-fail and Restart) — смежный контекст: advisory lock singleton vs per-entity
