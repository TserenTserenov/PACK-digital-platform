---
id: DP.SOTA.002
name: Context Engineering
type: sota
status: active
summary: "Дисциплина курирования контекста ИИ-агента: Write/Select/Compress/Isolate — что попадает в окно, в каком формате, как обновляется"
created: 2026-02-13
edition: "2026-02"
trust:
  F: 4
  G: external
  R: 0.8
related:
  enables: [DP.M.003, DP.EXOCORTEX.001]
  integrates_with: [DP.SOTA.001, DP.SOTA.008, DP.SOTA.012]
  operationalized_by: [DP.EXOCORTEX.001]
tags: [context-engineering, llm, prompt-engineering, anthropic, memory-management]
---

# Context Engineering

## 1. Определение

**Context Engineering** — дисциплина курирования контекста, который видит LLM/агент: какие токены и артефакты попадают в окно, в каком формате, как обновляются. Заменяет prompt engineering как основную практику 2025-2026.

> FPF-мост: инженерия представлений (views) и их привязка к ядру знания, чтобы агент действовал в bounded context.

## 2. Статус SOTA (февраль 2026)

**SOTA, фронтир.** Самая быстрорастущая практика в AI engineering.

- **Anthropic (2025):** +39% производительности, -84% токенов при комбинации memory tools + context editing.
- **Shopify CEO Tobi Lutke (2025):** «The art of providing all the context for the task to be plausibly solvable by the LLM.»
- **Andrej Karpathy (2025):** популяризировал термин, отделив от prompt engineering.
- **Консенсус 2026:** context engineering заменил prompt engineering как primary discipline.

## 3. Четыре стратегии

| Стратегия | Описание | Реализация в экзокортексе |
|-----------|----------|--------------------------|
| **Write** | Создавать правильные артефакты для контекста | CLAUDE.md, memory/, Pack entities |
| **Select** | Выбирать релевантные артефакты для задачи | WP Gate, Layer 0→1→2 загрузка |
| **Compress** | Сжимать информацию до необходимого минимума | summary в frontmatter, memory ≤100 строк |
| **Isolate** | Изолировать контексты друг от друга | Bounded contexts Pack, repo-CLAUDE.md |

## 4. SPF-интеграция

| SPF-механизм | Context Engineering аспект |
|-------------|---------------------------|
| Pack Layer 0/1/2 | Select: послойная загрузка по необходимости |
| summary в frontmatter | Compress: retrieval без чтения полного файла |
| Bounded Context | Isolate: каждый Pack = отдельный контекст |
| CLAUDE.md + memory/ | Write: операционные инструкции для агента |
| Entity Index в manifest | Select: навигация по summary для точечной загрузки |

## 5. Правила применения

1. **При написании CLAUDE.md / memory/** — это context engineering. Каждая строка = токен в окне агента. Удалять неиспользуемое.
2. **При создании Pack entity** — summary обязателен (≤150 символов). Это не документация, это retrieval key.
3. **При проектировании агента** — определить: что в always-in-context (Layer 0)? что on-demand (Layer 1-2)?
4. **Приоритетная тройка:** всегда проверяй — Write (артефакты созданы?), Select (релевантные загружены?), Compress (лишнее убрано?).

## 6. Harness Engineering: Context Engineering в масштабе (OpenAI 2026)

OpenAI применили Context Engineering как один из трёх компонентов «Harness Engineering» — инженерии обвязки для автономной разработки. За 5 месяцев, 3-7 инженеров, 1M строк кода, 0 строк вручную.

**Новые паттерны из практики OpenAI:**

| Паттерн | Описание | Аналог в экзокортексе |
|---------|----------|----------------------|
| **Agent Legibility** | Код и документация навигабельны для ИИ-агента без внешних подсказок | Knowledge legibility: Pack + MCP навигабельны для агентов и людей |
| **Progressive Disclosure** | Агент начинает с маленькой стабильной точки входа, учится где искать дальше | CLAUDE.md Layer 0 → memory/ Layer 1 → Pack Layer 2 |
| **Co-located Plans** | Активные планы, завершённые планы, известный техдолг — всё версионировано рядом с кодом | DS-my-strategy/current/ + inbox/ + archive/ — всё в Git |
| **Taste Invariants** | Человеческий вкус фиксируется один раз (правило/линтер), затем принуждается автоматически | FPF/SPF rules → CLAUDE.md → принуждаются в каждой сессии |

**Ключевой инсайт (Can Boluk, 2026):** изменение только обвязки (не модели) улучшило результаты 15 LLM на 5-14 п.п. Harness > Model. См. DP.D.025.

## 7. Источники

- Anthropic. «Effective Context Engineering for AI Agents» (2025)
- Lutke T. Context Engineering definition (Shopify, 2025)
- Karpathy A. Context Engineering popularization (2025)
- SPF.SPEC.003 — Pack scalability (3-layer loading = Context Engineering applied)
- OpenAI. «Harness Engineering: Leveraging Codex in an Agent-First World» (2026-02-11)
- Boluk C. «The Harness Problem» (2026-02-12)
- Fowler M. «Harness Engineering» (2026)
