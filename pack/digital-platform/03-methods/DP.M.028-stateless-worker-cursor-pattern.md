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

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Амортизация I/O через batched-flush (N=100 events / T=5 sec) ↔ окно потери данных при crash | Увеличение N/T снижает нагрузку на БД, но расширяет буфер, который может быть потерян при незапланированном SIGKILL — только компонент 3 (shutdown handler) успевает сбросить буфер, и то лишь при получении SIGTERM/SIGINT |
| Скорость реакции shutdown handler'а (компонент 3) ↔ размер накопленного batched-flush буфера (компонент 2) | Компонент 3 требует немедленный flush перед выходом, но чем крупнее буфер (задан N=100), тем дольше сам flush — обработчик должен успеть завершить эту операцию до принудительного kill со стороны оркестратора (Docker/systemd timeout) |
| Per-domain isolation (компонент 4) ↔ простота единого курсора | Отдельная строка cursor на каждый домен (learning, billing, social) защищает от каскадного сбоя одного домена, но умножает состояние, которое нужно инициализировать и мониторить — N доменов × 1 cursor-строка вместо одной общей |

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

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| Переоценка CursorCache, недооценка Shutdown handler | Внимание съезжает на реализацию CursorCache (компонент 1, самый «database»-очевидный) — курсор кажется достаточным для «не терять позицию», хотя без graceful flush при SIGTERM теряются именно буферизированные, ещё не сброшенные события batched-flush (компонент 2) |
| Тест F6 проверяется только в однодоменной конфигурации | Практикующий тестирует «убей процесс → подними» только в happy-path сценарии с одним доменом, недооценивая per-domain isolation (компонент 4) — не проверяет, что сбой курсора в одном домене (learning) действительно не блокирует остальные (billing, social) |

## Связи

- 12-factor F6: Processes (stateless + share-nothing)
- Контекст: WP-307 Ф6, бот + projection-workers W3/W4 (12 мая 2026)
- Смежно: DP.ARCH.006 (Память — event sourcing + CQRS)
- Смежно: DP.M.014 (Evaluator Worker — другой тип воркера)

---

> 2026-07-27 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 2). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
