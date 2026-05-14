---
id: DP.M.028
name: Stateless Worker — PostgresStorage + CursorCache + batched-flush
type: method
domain: DP
status: active
valid_from: 2026-05-12
source: WP-307 Ф6, 12-factor F6
---

# DP.M.028 — Correct F6 stateless pattern для workers

## Назначение

Трёхкомпонентный паттерн для stateless projection-workers: отказ процесса не теряет позицию в потоке событий и не дублирует обработку.

## Компоненты

### 1. CursorCache — позиция в БД

Курсор (последний обработанный event_id) хранится в PostgreSQL, не в памяти процесса.

```python
# Псевдокод
cursor = await db.fetchval("SELECT last_event_id FROM cursors WHERE domain=$1", domain)
```

### 2. Batched-flush — амортизация I/O

Обработка буферизируется: flush каждые N событий ИЛИ каждые T секунд (whichever first).

```
N = 100 events / T = 5 sec — рабочий баланс для production Neon
```

### 3. Shutdown handler — flush перед остановкой

При SIGTERM/SIGINT — немедленный flush буфера перед выходом. Иначе буферизированные события теряются.

### 4. Per-domain isolation

Каждый домен (learning, billing, social) имеет независимую строку cursor. Сбой в одном домене не блокирует остальные.

## Тест F6

Убей процесс → подними → проверь: нет потери данных, нет дублей. Прошло? → F6 compliant.

## Применимость

Любой worker, читающий event stream из PostgreSQL/Neon с требованием exactly-once или at-least-once семантики.

## Связи

- 12-factor F6: Processes (stateless + share-nothing)
- Контекст: WP-307 Ф6, бот + projection-workers W3/W4 (12 мая 2026)
- Смежно: DP.ARCH.006 (Память — event sourcing + CQRS)
- Смежно: DP.M.014 (Evaluator Worker — другой тип воркера)
