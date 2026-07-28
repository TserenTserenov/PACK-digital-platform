---
id: DP.M.222
title: "Event-тип = 3-компонентная атомарная единица деплоя"
type: method
pack: digital-platform
bounded_context: event-driven-architecture
trust: validated
epistemic_stage: observed-in-production
valid_from: 2026-05-29
---

# DP.M.222: Event-тип = 3-компонентная атомарная единица деплоя

## Суть

Новый event-тип требует 3 компонента, которые должны быть задеплоены атомарно. Частичный деплой (1 или 2 из 3) создаёт невидимые failure mode.

## Три компонента

| # | Компонент | Где | Gap-эффект |
|---|-----------|-----|-----------|
| 1 | JSON Schema | event-gateway Worker | Видимый rejection (DLQ) |
| 2 | Запись в `reference.projection_rules` | БД (которой projection_worker) | Невидимый silent drop |
| 3 | Хендлер | projection_worker | Невидимый unprocessed |

## Чеклист деплоя

```
[ ] JSON Schema добавлена в event-gateway Worker и задеплоена
[ ] Запись в reference.projection_rules добавлена (event_type → projection_worker)
[ ] Хендлер в projection_worker реализован
[ ] Smoke-test: тестовое событие → проверить target table (reward_transactions или аналог)
```

## Применение

PR на добавление нового event-типа должен содержать изменения во всех трёх точках. Code review: grep по event-type-имени в трёх местах.

## Источник

WP-327 v4.5 post-mortem (Этап 26 + 22+27), 2026-05-29.
~~~

**Вердикт:** accept
**Обоснование:** Конкретный метод с чеклистом. Production-validated (обнаружены два gap одновременно в WP-327). Атомарность деплоя — реusable паттерн, применим к любой event-driven системе с gateway + projection_worker.

---

## Кандидат #3

**Источник capture:** Agent workspace auto-commit: threshold + daily squash pattern
**source_file:** captures.md
**Сырой текст:** «Commit only if >10 files OR >30 min since last commit. Daily squash at 05:00 collapses auto-commits into one. Reduces dirty state window and git log noise.»
**Классификация:** method

**Куда записать:**
- **Репо:** PACK-autonomous-agents
- **Файл:** `pack/autonomous-agents/04-methods/AS.M.047-agent-workspace-threshold-commit.md`
- **Действие:** создать файл

**Совместимость:**
- **Результат:** совместим — метод управления git-состоянием агентского workspace, not covered (max AS.M.046)
- **Проверено:** find PACK-autonomous-agents -name "AS.M.*" | sort (max=AS.M.046), pending reports (нет AS.M.* резерваций)

**Готовый текст (ready-to-commit):**

~~~markdown
---
