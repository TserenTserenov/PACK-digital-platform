# Pack Manifest: ИТ-платформа и цифровой двойник

## Идентификатор

- **Pack ID**: `DP`
- **Версия**: 0.2.1
- **Статус**: Draft

## Область

**Название**: ИТ-платформа и цифровой двойник

**Описание**: Предметная область ИТ-платформы экосистемы развития интеллекта: архитектура платформы (3 слоя, 6 характеристик ЭМОГСС), ИИ-системы (агенты + ассистенты), цифровой двойник созидателя, навигация знаний между репозиториями, модульный экзокортекс пользователя, метод экстракции знаний, IPO-паттерн компонентов, отчуждаемость (platform-space vs user-space), генеративность (общее знание → шаблон → fork).

## Scope

### В scope

- Архитектура платформы (3-слойная модель с двумя зонами обработки)
- Архитектурные характеристики (эволюционируемость, масштабируемость, обучаемость, генеративность, скорость, современность)
- ИИ-системы платформы (реестр, классификация, паспорта)
- Цифровой двойник как модель созидателя
- Различения объект/модель/данные/представление + доменные различения
- Навигация знаний (4 уровня, кросс-репо ссылки)
- Модульный экзокортекс (CLAUDE.md + Memory шаблоны, 3-слойная архитектура инструкций)
- Метод экстракции знаний (информация → Pack-совместимые сущности)
- IPO-паттерн как универсальный контракт компонентов
- Отчуждаемость: platform-space vs user-space
- Индикаторы и их типы
- Детерминированные системы и MCP-серверы
- Типовые ошибки (failure modes)

### Вне scope

- Код MCP-сервиса (это DS-twin — downstream/instrument)
- Код бота (это DS-aist-bot — downstream/instrument)
- Код Стратега (это DS-strategist — downstream/instrument)
- UI/UX (это downstream/surface)
- Содержание индикаторов созидателя (это PACK-personal)
- Знания об экосистеме (это PACK-ecosystem)
- Планы, сроки, реестры проекта (это DS-ecosystem-development — downstream/governance)

## Расширенные виды (SPF.SPEC.001)

> Доменные виды сущностей, определённые этим Pack. Базовые виды (M, WP, FM, D, R, CHR, SOTA, MAP) определены в SPF.

| Код | Вид | Описание | Папка |
|-----|-----|----------|-------|
| `ARCH` | Architecture | Архитектурное описание системы | `02-domain-entities/` |
| `CONCEPT` | Concept | Концепция системы/подсистемы | `02-domain-entities/` |
| `SYS` | System | Детерминированная подсистема (сервис, MCP) | `02-domain-entities/` |
| `AGENT` | AI Agent | Реестр / паспорт ИИ-системы | `02-domain-entities/` |
| `AISYS` | AI System | Конкретная ИИ-система (паспорт) | `02-domain-entities/` |
| `NAV` | Navigation | Навигационная структура знаний | `02-domain-entities/` |
| `EXOCORTEX` | Exocortex | Модульный экзокортекс пользователя | `02-domain-entities/` |

## Содержимое Pack'а

| Категория | Файлов | Ключевые сущности |
|-----------|--------|-------------------|
| Доменный контракт | 5 | 17 различений, bounded context, онтология (SPF.SPEC.002), DP.D.025, DP.D.026, DP.D.027 |
| Доменные сущности | 9+1 | DP.ARCH.001, DP.ARCH.002, DP.AGENT.001, DP.AISYS.013, DP.NAV.001, DP.EXOCORTEX.001, DP.AGENT.012, DP.SYS.001, DP.CONCEPT.001 |
| Методы | 5 | DP.M.001–005 (экстракция, DDD, CE, персонализация, АрхГейт) |
| Рабочие продукты | 1 | DP.WP.001 (отчёт экстракции) |
| Failure modes | 2 | DP.FM.001 (информация как знание), DP.FM.002 (смешение слоёв) |
| Карта | 1 | DP.MAP.001 (навигационная карта) |

> Полная карта: [07-map/DP.MAP.001.md](07-map/DP.MAP.001.md)

## Entity Index

| ID | Name | Kind | Summary | Status |
|----|------|------|---------|--------|
| DP.AGENT.001 | ИИ-агенты | AGENT | Реестр и классификация ИИ-агентов платформы: Стратег, Экстрактор, Проводник и др. | active |
| DP.AGENT.012 | 00 Agent Passport | AGENT | Агент преобразует намерения в структурированные планы рабочих продуктов на месяц, неделю и день с отслеживанием выполнения | draft |
| DP.AGENT.012.SC.01 | 01 Strategy Session | AGENT | Еженедельная сессия стратегирования (пн 7:00): анализ прошлой недели, сдвиг месячного окна и формирование плана на неделю | draft |
| DP.AGENT.012.SC.02 | — План дня | AGENT | Ежедневное планирование (7:00): апдейт вчера по коммитам, контекст недели и план дня с рекомендацией старта | draft |
| DP.AGENT.012.SC.03 | 03 Week Review | AGENT | Итоговое ревью недели (вс 22:00): агрегация дневных планов, анализ коммитов, расчёт статистики и публикация в клуб | draft |
| DP.AGENT.012.SC.04 | 04 Month Report | AGENT | Итоговый отчёт за месяц: агрегация недельных данных, проверка выполнения приоритетов, анализ трендов и достижений | draft |
| DP.AGENT.012.SC.05 | 01 Evening Review | AGENT | Вечерний итог дня по запросу: сопоставление коммитов со статусами РП, выявление незапланированного, carry-over на завтра | draft |
| DP.AGENT.012.SC.06 | 02 Check Plan | AGENT | Сверка задачи с планом по запросу: классификация на in-plan / aligned / unplanned / urgent с рекомендациями действия | draft |
| DP.AGENT.012.SC.07 | 03 Update Priorities | AGENT | Изменение приоритетов на уровне дня/недели/месяца: определение типа изменения, каскадные эффекты, diff и коммит | draft |
| DP.AGENT.012.SC.08 | 04 Add Workproduct | AGENT | Добавление нового РП в план: сбор атрибутов, проверка бюджета, определение уровня размещения и коммит в план | draft |
| DP.AGENT.012.SCENARIOS | 00 Scenarios Index | AGENT | Индекс и навигация по 8 сценариям Стратега: 4 по расписанию и 4 по запросу, с временной сеткой и потоком данных | draft |
| DP.AISYS.013 | Знание-Экстрактор | AISYS | Prompt-based ИИ-система экстракции знаний из сессий в Pack-совместимые сущности | draft |
| DP.AISYS.014 | AIST Bot | AISYS | Telegram-бот экосистемы: тонкий клиент с сервисным реестром, ИИ-консультантом, биллингом и связью с цифровым двойником | draft |
| DP.ARCH.001 | Архитектура платформы | ARCH | 3-слойная архитектура ИТ-платформы с 6 характеристиками (ЭМОГСС): эволюционируемость, масштабируемость, обучаемость, генеративность, скорость, современность | active |
| DP.ARCH.002 | Тиры платформы | ARCH | 5 тиров (T1–T5): каждый тир — конфигурация среды по 5 измерениям (знания, данные, роль ИИ, действия, рабочее пространство). T1 Старт → T2 Изучение → T3 Персонализация → T4 Созидание (IWE) → T5 Администрирование | draft |
| DP.ASSIST.001 | ИИ-ассистенты (superseded) | ASSIST | Объединены с DP.AGENT.001 — различие агент/ассистент сохранено как характеристика | superseded |
| DP.CONCEPT.001 | Концепция платформы | CONCEPT | Концепция ИТ-платформы экосистемы: цифровой двойник, ИИ-системы, интеграции, отчуждаемость | active |
| DP.D.025 | Harness ≠ Agent | D | Harness (упряжь/обвязка) определяет результат больше, чем мощность агента/модели | active |
| DP.D.027 | Content Budget Model (3 оси) | D | Длина, глубина и персонализация контента — три независимые оси, управляемые раздельно | active |
| DP.D.028 | User Data Tiers — тирование данных пользователя | D | Данные пользователя растут с тиром платформы: T1 базовые знания (без персонализации) → T2 минимальная персонализация (профиль + история) → T3 цифровой двойник (ИИ знает контекст) → T4 личный контекст + ИИ-агенты (со-мыслитель) → T5 управление экосистемой | active |
| DP.D.029 | Language Model ≠ World Model | D | LLM = пассивные знания о мире из текстов (кабинетный учёный). World Model = активная модель, обновляемая из взаимодействия с реальностью (инженер). Критерий: замыкает ли система цикл действие-измерение-обновление | active |
| DP.D.030 | Топология деплоя платформы | D | Railway (бот) + CF Workers (MCP) + Neon (DB + pgvector) + GitHub (код + шаблон). Позже Railway → Kubernetes. Каждый сервис — в своей экологической нише (compute vs edge vs serverless DB vs VCS) | active |
| DP.D.031 | MCP Access Model: публичный vs приватный | D | knowledge-mcp (Pack + онтология) — публичный для всех (один инстанс, общие знания). digital-twin-mcp — приватный (фильтрация по user_id, персональные данные). Критерий: знания ≠ данные пользователя | active |
| DP.D.032 | Единый Circuit Breaker для внешних зависимостей | D | Один паттерн circuit breaker для всех внешних зависимостей (Claude API, MCP, Neon). Цель: предотвратить циклические запросы, которые списывают деньги. Не per-client — единый middleware | active |
| DP.EXOCORTEX.001 | Модульный экзокортекс | EXOCORTEX | 3-слойная архитектура инструкций ИИ-агентов: CLAUDE.md + Memory + repo-CLAUDE.md | draft |
| DP.FM.001 | Информация как знание | FM | Необработанная информация ошибочно принимается за формализованное знание без экстракции | draft |
| DP.FM.002 | Смешение слоёв | FM | Смешение слоёв архитектуры платформы: код в Pack, знания в Downstream, UI в архитектуре | draft |
| DP.FM.003 | Контекстная слепота AI-агента | FM | Ускорение генерации модели без ресурсов на добычу контекста = ускорение самообмана. AI-агент не может сам получить живой контекст из реальной жизни | draft |
| DP.FM.004 | Narrow Pregeneration Scope | FM | — | draft |
| DP.FM.005 | Дрейф модель–реальность (Model-Reality Drift) | FM | AI-агент без петли измерения деградирует в самосогласованный текст: внутренняя непротиворечивость растёт, но близость к реальности падает. Двойной дрейф: мир меняется + цели агента дрейфуют | draft |
| DP.FM.006 | Когнитивный долг как следствие агентного ИИ | FM | Агентный ИИ производит код быстрее, чем разработчики успевают строить теорию системы. Техдолг — в коде, когнитивный долг — в головах. Программа — это теория в головах, код — лишь проекция | draft |
| DP.FM.007 | Дрейф представлений (View Drift) | FM | View-файлы (README, CLAUDE.md, REGISTRY, посты) рассинхронизируются с model-файлами (Pack-сущности, код, файловая система). Причина: отсутствие автоматической валидации claims в view-файлах при изменении model. | draft |
| DP.M.001 | Извлечение знаний | M | Трансформация сырой информации в Pack-совместимые сущности через обнаружение, классификацию и формализацию | draft |
| DP.M.002 | Применение стратегического DDD | M | Метод применения стратегического DDD к Pack и экзокортексу: BC mapping, UL extraction, Context Map для inter-agent integration | draft |
| DP.M.003 | Context Engineering Protocol | M | Метод проектирования контекста ИИ-агента: Write/Select/Compress/Isolate → CLAUDE.md + memory/ + Pack layers | draft |
| DP.M.004 | Адаптивная персонализация по состоянию | M | Адаптация контента обучения (промпты, bloom_level, тематика) на основе состояния пользователя из теста систематичности | draft |
| DP.M.005 | АрхГейт (ArchGate) | M | Блокирующая оценка архитектурного решения по 6 характеристикам (ЭМОГСС): эволюционируемость, масштабируемость, обучаемость, генеративность, скорость, современность. Без прохождения — решение не принимается | active |
| DP.M.006 | Самопроверка вайб-режима (Vibe-Check) | M | Метод оценки допустимости вайб-режима работы по 6 характеристикам проектной ситуации. Определяет: вайб допустим или нужна профессиональная работа | draft |
| DP.M.007 | Intervention Loop (Петля интервенции) | M | Метод замыкания цикла действие-измерение-обновление для AI-агентов на LLM-платформе: зондирование реальности, фиксация невязки, обновление модели. Компенсирует отсутствие world model | draft |
| DP.MAP.001 | Pack Navigation Map | MAP | — | — |
| DP.NAV.001 | Навигация знаний | NAV | 4-уровневая навигация знаний между репозиториями: FPF → SPF → Pack → Downstream | draft |
| DP.RUNBOOK.001 | Runbook: Aist Bot Errors | RUNBOOK | Каталог типичных ошибок Aist Bot с классификацией по уровням (L1-L4), автоматическими действиями и ручными fix'ами | draft |
| DP.SOTA.001 | DDD Strategic (Khononov) | SOTA | Стратегический DDD: Bounded Context, Context Map, Ubiquitous Language — метод добычи и инженерной реализации доменного ядра | active |
| DP.SOTA.002 | Context Engineering | SOTA | Дисциплина курирования контекста ИИ-агента: Write/Select/Compress/Isolate — что попадает в окно, в каком формате, как обновляется | active |
| DP.SOTA.003 | Open API Specifications | SOTA | Экосистема открытых спецификаций интерфейсов: OpenAPI (sync), AsyncAPI (event-driven), CloudEvents (envelope) + Arazzo (workflows) | active |
| DP.SOTA.004 | GraphRAG + Knowledge Graphs | SOTA | Комбинация vector search + knowledge graph traversal для multi-hop reasoning: 87% vs 23% accuracy по сравнению с базовым RAG | active |
| DP.SOTA.005 | AI-Native Org Design | SOTA | Организационная архитектура для AI-first: плоские иерархии, agent orchestration, end-to-end accountability вместо функциональных силосов | active |
| DP.SOTA.006 | Agentic Development | SOTA | Multi-agent orchestration: инженеры оркестрируют специализированных агентов, а не пишут код напрямую; requirement-to-deploy за часы | active |
| DP.SOTA.007 | AI-Accelerated Ontology Engineering | SOTA | LLM ускоряют онтологическую инженерию в 10x: моделирование, расширение, population, alignment, entity disambiguation | active |
| DP.SOTA.008 | Real-Time Knowledge Capture | SOTA | Capture during work, not after: знания фиксируются в момент обнаружения, а не ретроспективно — консенсус KM 2026 | active |
| DP.SOTA.009 | Knowledge-Based Digital Twins | SOTA | Персональные/enterprise knowledge graphs как digital twin: реплицируют перспективу, знания и предпочтения владельца | active |
| DP.SOTA.010 | DSL → DSLM Evolution | SOTA | Бифуркация DSL: классические domain-specific languages стабильны, фронтир ушёл в Domain-Specific Language Models (DSLM) | active |
| DP.SOTA.011 | Coupling Model (Khononov 2024) | SOTA | Многомерная модель связанности: knowledge coupling, distance coupling, volatility coupling — 4 уровня интеграции | active |
| DP.SOTA.012 | Multi-Representation Knowledge Architecture | SOTA | Model/View эволюционировал в multi-representation: vector + graph + hierarchical index, отделённые от surface (бот, курс, API) | active |
| DP.SOTA.013 | World Models | SOTA | Переход от LLM (модели знаний о мире) к World Models (модели мира): замыкание цикла действие-измерение-обновление, три линии исследований, архитектурные импликации для AI-агентов | active |
| DP.SYS.001 | Детерминированные системы | SYS | Реестр детерминированных подсистем платформы: MCP-серверы, базы данных, сервисы | active |
| DP.WP.001 | Отчёт экстракции | WP | Структурированный отчёт экстракции знаний с классификациями, предложениями и валидацией | draft |
| DP.WP.002 | Ubiquitous Language | WP | Единый язык домена: глоссарий терминов, прорастающий во все артефакты — код, UI, документацию, тикеты, планы | draft |

> *Auto-generated by `generate-map.py` on 2026-02-20*

## Upstream dependencies

- [TserenTserenov/SPF](https://github.com/TserenTserenov/SPF) — Second Principles Framework
- [ailev/FPF](https://github.com/ailev/FPF) — First Principles Framework
- [aisystant/PACK-personal](https://github.com/aisystant/PACK-personal) — контракт индикаторов созидателя

## Downstream outputs

- [DS-twin](https://github.com/aisystant/DS-twin) — MCP-реализация цифрового двойника (instrument)
- [DS-aist-bot](https://github.com/aisystant/DS-aist-bot) — Telegram-бот, тонкий клиент (instrument)
- [DS-strategist](https://github.com/TserenTserenov/DS-strategist) — Агент Стратег (instrument)
- [DS-extractor](https://github.com/TserenTserenov/DS-extractor) — Знание-Экстрактор, prompt-based ИИ-система (instrument)
- [DS-exocortex-setup](https://github.com/TserenTserenov/DS-exocortex-setup) — Агент развёртывания экзокортекса (instrument)
- [FMT-exocortex](https://github.com/TserenTserenov/FMT-exocortex) — Шаблон экзокортекса (format)
- [DS-strategy](https://github.com/TserenTserenov/DS-strategy) — Личный стратегический хаб (governance)

## Maintainers

- @TserenTserenov

## Changelog

- 0.2.1 — Архитектурная ревизия: 4 слоя → 3 слоя (ИИ + детерминированные = слой обработки с двумя зонами-пирами), +4-я характеристика «Генеративность» (общее знание → шаблон → fork → созидатель)
- 0.2.0 — Архитектурное обновление: 4-слойная модель, ИИ-системы (объединение агентов/ассистентов), 3 характеристики (эволюционируемость, масштабируемость, обучаемость), IPO-паттерн, отчуждаемость, навигация знаний, модульный экзокортекс, метод экстракции знаний, 6 новых различений, failure modes, навигационная карта
- 0.1.0 — Initial structure
