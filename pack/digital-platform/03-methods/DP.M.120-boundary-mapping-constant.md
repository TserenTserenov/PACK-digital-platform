---
id: DP.M.120
name: "Boundary Mapping Constant — single source граничного маппинга"
kind: METHOD
status: active
domain: digital-platform
trust: medium
epistemic_stage: practice
valid_from: 2026-05-20
related:
  - DP.D.025  # Harness ≠ Agent
  - DP.ARCH.001  # 25 принципов архитектуры (OwnerIntegrity)
sources:
  - "WP-149 Ф0б — eb9a52a3 (_STAGE_ID_TO_INT + STAGE_NARRATIVE + E2E PASS)"
  - "FORM.089 (5 ступеней мастерства)"
---

# DP.M.120 — Boundary Mapping Constant

> Граница между semantic string-id (для людей и онтологии Pack) и dense int (для индексов, cap-таблиц, order) проходит через **одну mapping-константу** в одном файле, не через magic-numbers по местам использования.

## Контекст применения

Возникает на стыке двух представлений одной сущности:

| Представление | Зачем | Пример |
|---------------|-------|--------|
| Semantic string-id | Самодокументируемость, читаемость онтологии, стабильность при перестановке | `stage_random`, `stage_practitioner`, `stage_systematic` |
| Dense int | Индексы, order-by, cap-таблицы, плотные массивы, performance | `stage_id IN (1..5)` |

## Шаги

1. **Один файл — одна константа.** В точке входа в код границы определить:
   ```python
   _STAGE_ID_TO_INT = {
       'stage_random': 1,
       'stage_practitioner': 2,
       'stage_systematic': 3,
       'stage_disciplined': 4,
       'stage_proactive': 5,
   }
   ```
2. **Inverse при необходимости.** Если нужен обратный путь — вывести из источника, не дублировать руками:
   ```python
   _INT_TO_STAGE_ID = {v: k for k, v in _STAGE_ID_TO_INT.items()}
   ```
3. **Парные narrative-константы.** Если int-индекс несёт payload (нарратив, label, описание) — тоже одна константа:
   ```python
   STAGE_NARRATIVE = {
       1: "Я могу меняться",
       2: "Я — система",
       3: "Окружение влияет на меня",
       4: "Мир — система",
       5: "Мы меняем мир",
   }
   ```
4. **E2E-тест на синхронность.** Один тест проходит по всем ключам обеих констант: `for stage_id, stage_int in _STAGE_ID_TO_INT.items(): assert STAGE_NARRATIVE[stage_int] is not None`. Гарантия — добавление ступени без обновления второй константы упадёт в CI.
5. **Запрет magic-numbers.** В прикладном коде запрещены литералы `1..5` напрямую — только через константу. Lint-правило: `grep -n "stage_id == [0-9]"` должен быть пуст.

## Преимущества

- **Одно место правды.** Изменение состава ступеней = правка одного файла.
- **Тестируется единожды.** E2E-тест на mapping покрывает все use-cases.
- **Ревью добавления = один файл.** Code review добавления ступени тривиален.
- **OwnerIntegrity** (DP.ARCH.001 §7): один факт — одно место.

## Анти-паттерн

- Magic-numbers по местам использования (`if stage_id == 3: ...`)
- Дублирование маппинга в нескольких модулях
- Inverse-константа, написанная руками (`_STAGE_INT_TO_ID = {1: 'stage_random', ...}`) — рассинхрон гарантирован

## Универсальность

Паттерн применим к любым границам между semantic-id (для людей) и dense int (для индексов/order/cap):

- Stage IDs (FORM.089 § 5 ступеней мастерства)
- Qualification levels (WP-327 baseline-индексы)
- Course stages, role tiers, priority levels
- Любая enum-таблица БД с self-documenting именами

## Тест

«Можно ли добавить новый элемент за одну правку в одном файле, и упадёт ли E2E-тест, если забыть payload?» — Да на оба → паттерн применён.
