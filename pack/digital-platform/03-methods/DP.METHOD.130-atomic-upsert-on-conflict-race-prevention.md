---
id: DP.METHOD.130
type: method
pack: PACK-digital-platform
domain: digital-platform / db-concurrency
trust: draft
epistemic_stage: empirical
valid_from: 2026-07-07
source: "session-close 2026-07-04 (DS-my-strategy 8ee7d0a59, WP-463 Ф4)"
schema_version: 1
related:
  see_also: [DP.M.213]
---

# DP.METHOD.130 — Атомарный INSERT ON CONFLICT DO UPDATE: устранение гонки SELECT+Python+UPSERT

## Описание

Перенос merge-логики с уровня Python-кода на уровень SQL. Один атомарный `INSERT ... ON CONFLICT DO UPDATE` вместо паттерна SELECT → Python-merge → UPSERT. Устраняет окно гонки состояний (TOCTOU) при параллельных прогонах.

## IPO

**Input:** данные для merge + уникальный ключ группировки

**Process:**
```sql
INSERT INTO table (key_col, data_col_a, data_col_b)
VALUES ($1, $2, $3)
ON CONFLICT (key_col) DO UPDATE
  SET data_col_a = EXCLUDED.data_col_a,
      data_col_b = COALESCE(table.data_col_b, EXCLUDED.data_col_b)
```

**Output:** строка создана или обновлена атомарно; параллельные прогоны мерджатся корректно

## Принцип

Паттерн SELECT+Python+UPSERT имеет окно между чтением и записью. При параллельных прогонах второй прогон перетирает данные первого. Один SQL-запрос атомарен внутри транзакции — нет окна гонки.

## Тест применимости

«Если два прогона запустить одновременно — что произойдёт?» SELECT+Python+UPSERT: второй затирает первый → применить этот метод. Атомарный UPSERT: оба мерджатся корректно.

## Отличие от DP.M.213

DP.M.213 (upsert + xmax=0): определяет, был ли результат INSERT или UPDATE (для downstream логики). Этот метод: устраняет гонку состояний при merge нескольких полей. Оба используют `ON CONFLICT DO UPDATE`, решают разные задачи.

## Проверка

3 интеграционных теста на реальном Postgres: нормальный прогон, два параллельных прогона (прямое воспроизведение гонки), COALESCE guard при пустых данных.
