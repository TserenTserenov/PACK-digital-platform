---
id: DP.FM.090
name: "Числовой порядковый guard в multi-producer turn-log вместо семантического"
type: failure-mode
pack: PACK-digital-platform
domain: multi-agent-coordination
trust: 0.85
epistemic_stage: confirmed
valid_from: 2026-05-28
source: WP-358 Ф8 peer-session 2026-05-28-05, commit aa85f81e (fix guard-order-not-number)
---

# DP.FM.090 — Числовой порядковый guard в multi-producer turn-log вместо семантического

## Описание

Failure mode диспетчера очерёдности ходов (turn-taking dispatcher): проверка «ответили ли мы на последний ход пилота» реализована через сравнение числовых индексов (`claude_turn_max >= pilot_turn`), а не через семантическую позицию.

**Почему ломается.** В системе с несколькими производителями записей (бот-обработчик + dispatcher) числовые счётчики ходов конкурируют независимо. `max(claude_turn)` может оказаться больше `pilot_turn` не потому что dispatcher ответил на ход пилота, а потому что бот-обработчик сгенерировал запись с бо́льшим номером до следующего pilot-хода. Итог: dispatcher пропускает ответ на реальный вопрос пилота.

## Симптом

- Pilot пишет второй раз — dispatcher не отвечает («считает, что уже ответил»).
- В turn-log видно: `claude_turn_max ≥ pilot_turn`, хотя последняя запись с `role=claude` соответствует предыдущему turn, а не текущему.
- Баг воспроизводится только когда два producer'а генерируют записи в интерлив (бот + dispatcher).

## Причина

Суррогатный числовой индекс используется как замена семантического состояния («чья очередь»). В single-producer системе числовой порядок монотонен → совпадает с семантикой. В multi-producer системе нарушается.

## Профилактика

**Правильный guard — order-based (семантический):**

```python
# НЕПРАВИЛЬНО — числовой guard
if max(t.turn_n for t in turns if t.role == 'claude') >= max(t.turn_n for t in turns if t.role == 'pilot'):
    skip()

# ПРАВИЛЬНО — семантический guard
if turns[-1].role != 'pilot':
    skip()  # последняя запись не от пилота → отвечать не нужно
```

Принцип: для очередей с несколькими производителями семантический маркер (role/status/type последней записи) надёжнее числового индекса.

## Применимость

- Telegram-сессии с несколькими write-путями (бот-команды + background dispatcher).
- Чат-диалоги с concurrent writer'ами (user + bot + webhook).
- Очереди задач с несколькими воркерами, где нужно определить «кому следующий ход».
- Любой multi-producer append-log, где порядок значим семантически.

## Тест обнаружения

```bash
grep -n "turn_max.*>=.*pilot_turn\|pilot_turn.*<=.*turn_max" dispatcher.py
```
→ 0 совпадений = ок; ≥1 = FM активен.

## Связи

- WP-358 Ф8 — контекст обнаружения (TG-сессии с dispatcher)
- DP.FM.074 — смежный FM: SM callback-router missing (multi-path write)
