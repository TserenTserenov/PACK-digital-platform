---
id: DP.M.057
name: A/B-оценка альтернативного ML-компонента
type: method
domain: digital-platform
status: active
work_products: [A/B evaluation report]
distinctions: []
valid_from: 2026-05-17
schema_version: 1
---

# DP.M.057 — A/B-оценка альтернативного ML-компонента

## Описание

Метод замены встраивающей или retrieval-модели (embedding, reranker, BM25) на основе двухшагового критерия: feasibility (cost/leak) → quality gate (recall@N). Применим к любой паре компонентов с одинаковым интерфейсом.

## Входы

- Текущий компонент (baseline)
- Кандидат-компонент (challenger)
- Реальный корпус prod-запросов (минимум 50 пар)

## Выходы

- A/B evaluation report: recall@5 baseline vs challenger с парными примерами
- Решение: принять / отклонить / defer (при сомнительных результатах)

## Алгоритм

### Этап 1 — Entry criterion (feasibility)

Проверить оба условия. Оба должны быть истина для перехода к Этапу 2:

1. **Нулевой data leak**: challenger не обучен на данных из prod-корпуса (или open-weights без fine-tuning)
2. **Нулевой cost**: challenger бесплатен или дешевле baseline

Если хотя бы одно ложно → не рассматривать, закрыть как infeasible.

### Этап 2 — Quality gate

1. Выбрать ≥50 реальных prod-запросов (случайная выборка)
2. Прогнать оба компонента на одном корпусе документов
3. Вычислить recall@5 для baseline и challenger
4. **Критерий приёмки:** `recall@5(challenger) ≥ recall@5(baseline) - 0.05`
   - Не требуется улучшение, достаточно «не хуже -5%»
5. Для каждого запроса с расхождением подготовить парный пример (baseline top-5 vs challenger top-5)

### Этап 3 — Отчёт

A/B evaluation report содержит:
- Сводная таблица: recall@5 baseline / challenger / delta
- Парные примеры расхождений (≥10 штук)
- Решение и обоснование

## Критерий принятия решения

| Результат | Решение |
|-----------|---------|
| recall@5 ≥ baseline - 0.05 | Принять challenger |
| recall@5 < baseline - 0.05 | Отклонить |
| Граничное значение (±0.01) | Defer: расширить корпус до 200+ запросов |

## Контекст применения

WP-323 (Granite Embedding R2 vs текущий, 2026-05-17). Паттерн выявлен как недостающий: без формального entry criterion команды принимают компоненты без проверки feasibility, а без quality gate — без метрики сравнения.

## Связи

- WP: `DS-my-strategy/inbox/WP-323-granite-embedding-r2-experiment.md`
