---
id: DP.MAP.001
name: Pack Navigation Map
scope: full-pack
created: 2026-02-16
last_updated: 2026-02-16
generated: true
---

# [DP.MAP.001] Pack Navigation Map

> Auto-generated from frontmatter on 2026-02-16. Do not edit manually.

---

## Statistics

| Kind | Count |
|------|-------|
| AGENT (AGENT) | 11 |
| AISYS (AISYS) | 2 |
| ARCH (ARCH) | 2 |
| ASSIST (ASSIST) | 1 |
| CONCEPT (CONCEPT) | 1 |
| EXOCORTEX (EXOCORTEX) | 1 |
| Failure Modes (FM) | 4 |
| Methods (M) | 6 |
| Maps (MAP) | 1 |
| NAV (NAV) | 1 |
| SoTA Annotations (SOTA) | 12 |
| SYS (SYS) | 1 |
| Work Products (WP) | 2 |
| **Total** | **45** |

## Methods

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.M.001 | Извлечение знаний | Трансформация сырой информации в Pack-совместимые сущности через обнаружение, классификацию и формализацию | draft |
| DP.M.002 | Применение стратегического DDD | Метод применения стратегического DDD к Pack и экзокортексу: BC mapping, UL extraction, Context Map для inter-agent integration | draft |
| DP.M.003 | Context Engineering Protocol | Метод проектирования контекста ИИ-агента: Write/Select/Compress/Isolate → CLAUDE.md + memory/ + Pack layers | draft |
| DP.M.004 | Адаптивная персонализация по состоянию | Адаптация контента обучения (промпты, bloom_level, тематика) на основе состояния пользователя из теста систематичности | draft |
| DP.M.005 | АрхГейт (ArchGate) | Блокирующая оценка архитектурного решения по 6 характеристикам (ЭМОГСС): эволюционируемость, масштабируемость, обучаемость, генеративность, скорость, современность. Без прохождения — решение не принимается | active |
| DP.M.006 | Самопроверка вайб-режима (Vibe-Check) | Метод оценки допустимости вайб-режима работы по 6 характеристикам проектной ситуации. Определяет: вайб допустим или нужна профессиональная работа | draft |

## Work Products

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.WP.001 | Отчёт экстракции | Структурированный отчёт экстракции знаний с классификациями, предложениями и валидацией | draft |
| DP.WP.002 | Ubiquitous Language | Единый язык домена: глоссарий терминов, прорастающий во все артефакты — код, UI, документацию, тикеты, планы | draft |

## Failure Modes

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.FM.001 | Информация как знание | Необработанная информация ошибочно принимается за формализованное знание без экстракции | draft |
| DP.FM.002 | Смешение слоёв | Смешение слоёв архитектуры платформы: код в Pack, знания в Downstream, UI в архитектуре | draft |
| DP.FM.003 | Контекстная слепота AI-агента | Ускорение генерации модели без ресурсов на добычу контекста = ускорение самообмана. AI-агент не может сам получить живой контекст из реальной жизни | draft |
| DP.FM.004 | Narrow Pregeneration Scope | — | draft |

## SoTA Annotations

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.SOTA.001 | DDD Strategic (Khononov) | Стратегический DDD: Bounded Context, Context Map, Ubiquitous Language — метод добычи и инженерной реализации доменного ядра | active |
| DP.SOTA.002 | Context Engineering | Дисциплина курирования контекста ИИ-агента: Write/Select/Compress/Isolate — что попадает в окно, в каком формате, как обновляется | active |
| DP.SOTA.003 | Open API Specifications | Экосистема открытых спецификаций интерфейсов: OpenAPI (sync), AsyncAPI (event-driven), CloudEvents (envelope) + Arazzo (workflows) | active |
| DP.SOTA.004 | GraphRAG + Knowledge Graphs | Комбинация vector search + knowledge graph traversal для multi-hop reasoning: 87% vs 23% accuracy по сравнению с базовым RAG | active |
| DP.SOTA.005 | AI-Native Org Design | Организационная архитектура для AI-first: плоские иерархии, agent orchestration, end-to-end accountability вместо функциональных силосов | active |
| DP.SOTA.006 | Agentic Development | Multi-agent orchestration: инженеры оркестрируют специализированных агентов, а не пишут код напрямую; requirement-to-deploy за часы | active |
| DP.SOTA.007 | AI-Accelerated Ontology Engineering | LLM ускоряют онтологическую инженерию в 10x: моделирование, расширение, population, alignment, entity disambiguation | active |
| DP.SOTA.008 | Real-Time Knowledge Capture | Capture during work, not after: знания фиксируются в момент обнаружения, а не ретроспективно — консенсус KM 2026 | active |
| DP.SOTA.009 | Knowledge-Based Digital Twins | Персональные/enterprise knowledge graphs как digital twin: реплицируют перспективу, знания и предпочтения владельца | active |
| DP.SOTA.010 | DSL → DSLM Evolution | Бифуркация DSL: классические domain-specific languages стабильны, фронтир ушёл в Domain-Specific Language Models (DSLM) | active |
| DP.SOTA.011 | Coupling Model (Khononov 2024) | Многомерная модель связанности: knowledge coupling, distance coupling, volatility coupling — 4 уровня интеграции | active |
| DP.SOTA.012 | Multi-Representation Knowledge Architecture | Model/View эволюционировал в multi-representation: vector + graph + hierarchical index, отделённые от surface (бот, курс, API) | active |

## Maps

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.MAP.001 | Pack Navigation Map | — | — |

## Domain-Specific Entities

### AGENT

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.AGENT.001 | ИИ-агенты | Реестр и классификация ИИ-агентов платформы: Стратег, Экстрактор, Проводник и др. | active |
| DP.AGENT.012 | 00 Agent Passport | Агент преобразует намерения в структурированные планы рабочих продуктов на месяц, неделю и день с отслеживанием выполнения | draft |
| DP.AGENT.012.SC.01 | 01 Strategy Session | Еженедельная сессия стратегирования (пн 7:00): анализ прошлой недели, сдвиг месячного окна и формирование плана на неделю | draft |
| DP.AGENT.012.SC.02 | — План дня | Ежедневное планирование (7:00): апдейт вчера по коммитам, контекст недели и план дня с рекомендацией старта | draft |
| DP.AGENT.012.SC.03 | 03 Week Review | Итоговое ревью недели (вс 22:00): агрегация дневных планов, анализ коммитов, расчёт статистики и публикация в клуб | draft |
| DP.AGENT.012.SC.04 | 04 Month Report | Итоговый отчёт за месяц: агрегация недельных данных, проверка выполнения приоритетов, анализ трендов и достижений | draft |
| DP.AGENT.012.SC.05 | 01 Evening Review | Вечерний итог дня по запросу: сопоставление коммитов со статусами РП, выявление незапланированного, carry-over на завтра | draft |
| DP.AGENT.012.SC.06 | 02 Check Plan | Сверка задачи с планом по запросу: классификация на in-plan / aligned / unplanned / urgent с рекомендациями действия | draft |
| DP.AGENT.012.SC.07 | 03 Update Priorities | Изменение приоритетов на уровне дня/недели/месяца: определение типа изменения, каскадные эффекты, diff и коммит | draft |
| DP.AGENT.012.SC.08 | 04 Add Workproduct | Добавление нового РП в план: сбор атрибутов, проверка бюджета, определение уровня размещения и коммит в план | draft |
| DP.AGENT.012.SCENARIOS | 00 Scenarios Index | Индекс и навигация по 8 сценариям Стратега: 4 по расписанию и 4 по запросу, с временной сеткой и потоком данных | draft |

### AISYS

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.AISYS.013 | Знание-Экстрактор | Prompt-based ИИ-система экстракции знаний из сессий в Pack-совместимые сущности | draft |
| DP.AISYS.014 | AIST Bot | Telegram-бот экосистемы: тонкий клиент с сервисным реестром, ИИ-консультантом, биллингом и связью с цифровым двойником | draft |

### ARCH

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.ARCH.001 | Архитектура платформы | 3-слойная архитектура ИТ-платформы с 6 характеристиками (ЭМОГСС): эволюционируемость, масштабируемость, обучаемость, генеративность, скорость, современность | active |
| DP.ARCH.002 | Тиры обслуживания платформы | 4-тирная модель обслуживания: T1 Эксперт → T2 Наставник → T3 Со-мыслитель (IWE) → T4 Архитектор. Каждый тир определяет знания, персональные данные, роль ИИ и доступные действия | draft |

### ASSIST

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.ASSIST.001 | ИИ-ассистенты (superseded) | Объединены с DP.AGENT.001 — различие агент/ассистент сохранено как характеристика | superseded |

### CONCEPT

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.CONCEPT.001 | Концепция платформы | Концепция ИТ-платформы экосистемы: цифровой двойник, ИИ-системы, интеграции, отчуждаемость | active |

### EXOCORTEX

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.EXOCORTEX.001 | Модульный экзокортекс | 3-слойная архитектура инструкций ИИ-агентов: CLAUDE.md + Memory + repo-CLAUDE.md | draft |

### NAV

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.NAV.001 | Навигация знаний | 4-уровневая навигация знаний между репозиториями: FPF → SPF → Pack → Downstream | draft |

### SYS

| ID | Name | Summary | Status |
|----|------|---------|--------|
| DP.SYS.001 | Детерминированные системы | Реестр детерминированных подсистем платформы: MCP-серверы, базы данных, сервисы | active |

## Warnings

- Missing `summary`: DP.FM.004 (DP.FM.004-narrow-pregeneration-scope.md)
- Missing `summary`: DP.MAP.001 (DP.MAP.001.md)

---

*Generated by `scripts/generate-map.py` on 2026-02-16*