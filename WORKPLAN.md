---
type: workplan
updated: 2026-02-10
---

# WORKPLAN

> Операционный план рабочих продуктов для PACK-digital-platform (Pack, source-of-truth).

## Месячные приоритеты (февраль 2026)

| # | Приоритет | Бюджет | Потрачено | Статус |
|---|-----------|--------|-----------|--------|
| 1 | Архитектурное обновление Pack v0.2.0 (4 слоя, ИИ-системы, IPO, отчуждаемость) | 3h | 3h | **done** |
| 2 | Развитие stub-ИИ-систем до паспортов (AISYS.004, 006, 009, 010, 011, 014, 015, 016) | 4h | 0h | pending |
| 3 | SoTA-аннотации (MCP, FSM-ассистенты, knowledge extraction) | 2h | 0h | pending |
| 4 | Кросс-Pack навигация: индекс связей с PACK-personal и PACK-ecosystem | 1h | 0h | pending |
| 5 | Обновить DP.CONCEPT.001 (добавить IPO, каналы, 3 характеристики) | 1h | 0h | pending |

## Недельный план (W08: 9–15 фев)

| РП | Бюджет | Статус | Критерии |
|----|--------|--------|----------|
| Архитектурное обновление Pack v0.2.0 | 3h | **done** | [x] 4-слойная модель, [x] ИИ-системы, [x] IPO, [x] 6 различений, [x] Навигация, [x] Экзокортекс, [x] Экстрактор, [x] Карта |
| Fix: виды vs характеристики, обучаемость active | 0.5h | **done** | [x] DP.D.009 исправлен, [x] DP.AGENT.001, [x] DP.ARCH.001, [x] DP.ASSIST.001 |
| Downstream: Skill→Протокол, глоссарий, Заметочник | 0.5h | **done** | [x] CLAUDE.md, [x] DP.EXOCORTEX.001, [x] DP.SYS.001 |
| README.md + WORKPLAN.md обновления | 0.5h | **done** | [x] README с основными идеями, [x] WORKPLAN актуален |

## Недельный план (W09: 17–23 фев) — предварительный

| РП | Бюджет | Статус |
|----|--------|--------|
| Паспорта ИИ-систем: Проводник, ДЗ-чекер, Консультант (stub→draft) | 2h | pending |
| Обновить DP.CONCEPT.001 (IPO, каналы, 3 характеристики) | 1h | pending |

## Бэклог

### Ближайший (март)

- [ ] Паспорта stub-ИИ-систем: DailyAssignmentBuilder, RhythmKeeper, Консультант, Librarian, Индексатор, Orchestrator, ConsistencyChecker, SystemArchitect
- [ ] SoTA: MCP Protocol (Model Context Protocol) — текущее состояние, ограничения
- [ ] SoTA: FSM-архитектуры ИИ-ассистентов — best practices
- [ ] SoTA: Knowledge extraction из LLM-сессий — методы, инструменты
- [ ] Обновить DP.CONCEPT.001 с учётом IPO и 3 характеристик
- [ ] Кросс-Pack индекс связей (формат `pack:ID`)

### Средний срок (Q2)

- [ ] Failure modes: DP.FM.003 — нарушение отчуждаемости (hardcoded user data)
- [ ] Failure modes: DP.FM.004 — пропуск capture-to-pack (знание потеряно)
- [ ] Метод: DP.M.002 — навигация знаний (как найти нужное в системе Pack'ов)
- [ ] Work Product: DP.WP.002 — паспорт ИИ-системы (заполненный шаблон)
- [ ] Интеграция с PACK-personal: контракт характеристик созидателя → индикаторы двойника

### Долгий срок

- [ ] Формализация обучаемости: метрики, feedback loops, data pipelines
- [ ] Pack для Aist Bot (если вырастет в отдельную предметную область)
