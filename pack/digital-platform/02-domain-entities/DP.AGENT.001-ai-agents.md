---
id: DP.AGENT.001
name: ИИ-агенты
type: domain-entity
status: active
summary: "Реестр и классификация ИИ-агентов платформы: Стратег, Экстрактор, Проводник и др."
created: 2026-02-07
updated: 2026-02-21
trust:
  F: 4
  G: domain
  R: 0.7
epistemic_stage: formed
note: "Retitled from 'ИИ-агенты' to 'ИИ-системы'. ID preserved for backward compatibility."
related:
  supersedes: [DP.ASSIST.001]
  uses: [DP.ARCH.001, DP.D.033]
---

# ИИ-системы платформы

## 1. Определение

**ИИ-система** — компонент платформы (слой 3), использующий LLM для выполнения функций: принятие решений, генерация, диалог, анализ.

> ИИ-агент ≠ ИИ-ассистент (DP.D.009). Это **виды** ИИ-системы, а не разные архитектурные слои.

> **Принцип:** ИИ-система работает по промптам, не по коду. Промпт — это и есть «код» ИИ-системы. Детерминированная система управляется кодом (слой 2); ИИ-система управляется промптами (слой 3).

> **Role-Centric Architecture (DP.D.033):** Роль описывается независимо от исполнителя. Исполнитель выбирается и подготавливается отдельно. Одно имя (например, «Синхронизатор») может обозначать и роль (что делать), и систему-исполнителя (чем делать) — это разные ракурсы. Каталог ролей (§3.2) — первичен; реестр исполнителей (§3.1) — вторичен.

## 2. Виды и классификационные признаки

ИИ-агент и ИИ-ассистент — два основных **вида** ИИ-систем. Вид определяется тремя классификационными признаками:

| Признак | Значения | Описание |
|---------|----------|----------|
| **Ориентация** | `human` / `system` | На кого направлена: на человека (диалог) или на систему (автоматизация) |
| **Инициатива** | `by-request` / `autonomous` | Кто инициирует: пользователь или система по триггеру/расписанию |
| **Интерфейс** | `dialogue` / `api` / `both` | Через что взаимодействует: диалог, программный API или оба |

**Виды:**
- Проводник: human + by-request + dialogue → вид «ИИ-ассистент»
- Стратег day-plan: system + autonomous + api → вид «ИИ-агент»
- Стратег interactive: human + by-request + dialogue → вид «ИИ-ассистент»

Одна и та же ИИ-система может работать в разных режимах и менять вид в зависимости от сценария (Стратег: и агент, и ассистент).

## 3. Каталог ролей и исполнителей

> **Основание:** FPF A.2 (U.RoleAssignment) — роль описывается независимо от исполнителя. FPF A.13 (AgentialRole) — агентские роли требуют Grade 2+. Шаблон описания роли: DP.D.033.
>
> **Принцип:** Сначала роль (что делать, какие обязательства, рабочие продукты) → затем исполнитель (кто/чем играет роль). Роль = маска, которую надевает система: сама (агент) или по воле другого агента (инструмент).
>
> **Нотация FPF:** Holder#Role:Context@Window

### 3.1. Реестр исполнителей

| ID | Исполнитель | Категория | Grade | Репо |
|----|------------|-----------|-------|------|
| A1 | **Claude** | ИИ-агент | 3-4 | — |
| A2 | **Пользователь** | Человек | 4 | — |
| I1 | Бот Aist | Инструмент | 1 | DS-IT-systems |
| I2 | Синхронизатор (bash) | Инструмент | 1 | DS-ai-systems |
| I3 | Шаблонизатор (bash) | Инструмент | 0 | DS-ai-systems |
| I4 | MCP-серверы | Инструмент | 0 | DS-MCP |
| I5 | launchd | Инструмент | 0 | — (macOS) |
| I6 | Статистик | Инструмент | 1 | DS-ai-systems |
| I7 | Наладчик | Инструмент | 2 | DS-ai-systems |
| I8 | Оценщик | Инструмент | 2 | DS-ai-systems |
| I9 | Публикатор (bot scheduler) | Инструмент | 1 | DS-ai-systems |

### 3.2. Каталог ролей платформы

> Каждая роль описана по шаблону DP.D.033. Тип: **agential** (требует Grade 2+, автономия в выборе КАК действовать) или **functional** (Grade 0+, исполнение по алгоритму).

#### Агентские роли (Grade 2+)

| # | Роль | Suprasystem | Сценарии | Текущий Holder | Рабочие продукты |
|---|------|-------------|----------|----------------|------------------|
| R1 | **Стратег** | Экзокортекс | Day-plan, Session-prep, Note-review, Week-review, Interactive | A1 Claude (CLI headless / CLI) | WeekPlan, DayPlan, Review |
| R2 | **Экстрактор** | Экзокортекс | Session-close, On-demand, Inbox-check, Ontology-sync | A1 Claude (CLI headless / CLI) | Pack-сущности, Extraction Report |
| R3 | **Консультант** | Платформа DP | Q&A, ДЗ-чек, Генерация контента | A1 Claude via I1 (Claude API) | Ответы, оценки, контент |
| R4 | **Автор** | Экосистема | Написание постов, Подготовка презентаций | A1 Claude (CLI) | Посты, слайды |
| R5 | **Архитектор** | Платформа DP | АрхГейт, Архитектурные решения | A1 Claude (CLI) | ADR, оценки ЭМОГСС |
| R6 | **Кодировщик** | Платформа DP | Реализация кода, Рефакторинг | A1 Claude (CLI) | Код, коммиты |
| R7 | **Триажёр техдолга** | Платформа DP | Triage session (Issue Funnel) | A1 Claude (CLI) | Приоритизированный backlog |

> **R1, R2** — полное описание роли: `DS-ai-systems/strategist/system.yaml`, `DS-ai-systems/extractor/system.yaml`

<details>
<summary><strong>R3 Консультант</strong> — полное описание (DP.D.033)</summary>

```yaml
name: "Консультант"
type: agential
suprasystem: "Платформа DP"
context: "Диалог с учеником: ответы на вопросы, проверка ДЗ, генерация контента"

obligations:
  - "Отвечать на вопросы ученика по материалу курса (knowledge-mcp)"
  - "Проверять домашние задания с Bloom-aware обратной связью"
  - "Генерировать ленту и марафон-контент по расписанию"
  - "Использовать DT для персонализации ответов (proactive injection)"
  - "Удерживать latency <3 сек для пользовательского опыта"

expectations:
  - from: "R16 Ученик"
    expects: "Понятные ответы, соответствующие уровню ученика"
  - from: "R12 Оценщик"
    expects: "Оценки встроены в диалог (inline evaluation)"
  - from: "R13 Проводник"
    expects: "Контент соответствует текущему FSM-состоянию ученика"

methods:
  - name: "Knowledge-MCP Search"
    description: "Поиск по 9 источникам (semantic + keyword) для ответа на вопрос"
  - name: "DT Injection"
    description: "Чтение цифрового двойника ученика → персонализация промпта"
  - name: "Content Budget Model"
    description: "Адаптация объёма ответа к tier + контексту (DP.D.027)"
  - name: "Streaming SSE"
    description: "Потоковая генерация ответа для минимизации воспринимаемой задержки"

work_products:
  - product: "Ответ на вопрос"
    recipient: "R16 Ученик (TG message)"
    trigger: "Ученик задал вопрос"
  - product: "Проверка ДЗ"
    recipient: "R16 Ученик (TG message + оценка)"
    trigger: "Ученик отправил ДЗ"
  - product: "Лента/Марафон контент"
    recipient: "R16 Ученик (scheduled TG message)"
    trigger: "Scheduler (pre-gen за 3ч)"

current_holders:
  - holder: "A1 Claude Haiku (via I1 Bot, Claude API)"
    grade: 3
    covers_scenarios: [Q&A, DZ-Check, Content-Generation]

failure_modes:
  - "Hallucination — ответ не основан на Pack/knowledge-mcp"
  - "Tier Mismatch — контент не соответствует уровню ученика"

related_roles:
  - role: "R12 Оценщик"
    interaction: "Оценщик оценивает ответы, Консультант встраивает оценку в диалог"
  - role: "R13 Проводник"
    interaction: "Проводник определяет FSM-контекст → Консультант адаптирует контент"
  - role: "R16 Ученик"
    interaction: "Прямой диалог: вопрос → ответ"
```
</details>

<details>
<summary><strong>R4 Автор</strong> — полное описание (DP.D.033)</summary>

```yaml
name: "Автор"
type: agential
suprasystem: "Экосистема"
context: "Создание контента: посты, презентации, питчи, описания"

obligations:
  - "Писать посты по content-плану Стратега"
  - "Готовить презентации для семинаров (Marp + PDF)"
  - "Создавать описания мероприятий и продуктовые материалы"
  - "Соблюдать стиль и тон автора (голос владельца экзокортекса)"

expectations:
  - from: "R1 Стратег"
    expects: "Content plan с темами, аудиторией, приоритетами"
  - from: "R14 Заказчик"
    expects: "Текст соответствует замыслу, не нужно существенно переписывать"
  - from: "R15 Валидатор"
    expects: "Пост готов к публикации после review"

methods:
  - name: "Topic Research"
    description: "knowledge-mcp + Pack → сбор материала для поста"
  - name: "Structured Writing"
    description: "Заголовок → тезис → аргументация → вывод → CTA"
  - name: "Marp Slides"
    description: "Markdown → Marp → PDF для презентаций"

work_products:
  - product: "Пост (markdown)"
    recipient: "R15 Валидатор → DS-Knowledge-Index/docs/"
    trigger: "Content plan item"
  - product: "Презентация (Marp + PDF)"
    recipient: "R14 Заказчик (семинар)"
    trigger: "Семинар запланирован"
  - product: "Описание мероприятия"
    recipient: "Платформа (systemsworld.club, TG)"
    trigger: "Новое мероприятие"

current_holders:
  - holder: "A1 Claude (CLI interactive)"
    grade: 3
    covers_scenarios: [Post-Writing, Presentation, Description]

failure_modes:
  - "Voice Drift — стиль не соответствует голосу автора"

related_roles:
  - role: "R1 Стратег"
    interaction: "Стратег → content plan → Автор реализует"
  - role: "R15 Валидатор"
    interaction: "Валидатор проверяет текст перед публикацией"
```
</details>

<details>
<summary><strong>R5 Архитектор</strong> — полное описание (DP.D.033)</summary>

```yaml
name: "Архитектор"
type: agential
suprasystem: "Платформа DP"
context: "Архитектурные решения, АрхГейт-оценки, структура экосистемы"

obligations:
  - "Оценивать каждое архитектурное предложение по ЭМОГСС (6 характеристик)"
  - "Предлагать ТОЛЬКО решения с оценкой ≥8 по ArchGate"
  - "Определять BC-маппинг: знание → какой Pack"
  - "Поддерживать SOTA-справочник (memory/sota-reference.md)"
  - "Проверять приоритетную тройку: Context Engineering, DDD Strategic, Coupling Model"

expectations:
  - from: "R6 Кодировщик"
    expects: "Архитектура определена ДО начала кодирования"
  - from: "R2 Экстрактор"
    expects: "BC-маппинг и routing.md консистентны"
  - from: "R14 Заказчик"
    expects: "Обоснованные решения с таблицей ЭМОГСС"

methods:
  - name: "ArchGate (ЭМОГСС)"
    description: "6 характеристик: Эволюционируемость, Масштабируемость, Обучаемость, Генеративность, Скорость, Современность"
  - name: "SOTA Check"
    description: "Проверка по sota-reference.md: 18 практик (12 Platform + 6 Pack)"
  - name: "BC Mapping"
    description: "Определение Bounded Context → маршрутизация в правильный Pack"
  - name: "ADR (Architectural Decision Record)"
    description: "Формализация решения: контекст, варианты, выбор, обоснование"

work_products:
  - product: "ADR / ArchGate оценка"
    recipient: "R14 Заказчик, R6 Кодировщик"
    trigger: "Архитектурное решение требуется"
  - product: "BC-маппинг (routing.md update)"
    recipient: "R2 Экстрактор"
    trigger: "Новая предметная область или реорганизация Pack"
  - product: "SOTA-обновления"
    recipient: "memory/sota-reference.md → все роли"
    trigger: "Обнаружена новая SOTA-практика"

current_holders:
  - holder: "A1 Claude (CLI interactive)"
    grade: 3
    covers_scenarios: [ArchGate, BC-Mapping, ADR, SOTA-Update]

failure_modes:
  - "Weak Architecture — решение с оценкой ≤7 принято без обоснования"
  - "SOTA Ignorance — решение не учитывает доступные SOTA-практики"

related_roles:
  - role: "R6 Кодировщик"
    interaction: "Архитектор → архитектура → Кодировщик реализует"
  - role: "R2 Экстрактор"
    interaction: "Архитектор определяет BC → Экстрактор маршрутизирует знание"
  - role: "R1 Стратег"
    interaction: "Стратег определяет приоритеты → Архитектор решает HOW"
```
</details>

<details>
<summary><strong>R6 Кодировщик</strong> — полное описание (DP.D.033)</summary>

```yaml
name: "Кодировщик"
type: agential
suprasystem: "Платформа DP"
context: "Реализация кода, рефакторинг, баг-фиксы по архитектуре R5"

obligations:
  - "Реализовывать код по утверждённой архитектуре (R5)"
  - "Следовать CLAUDE.md репо (exit protocol, code style)"
  - "Не вводить security vulnerabilities (OWASP Top 10)"
  - "Capture-to-Pack при обнаружении паттернов/антипаттернов"
  - "Коммитить с осмысленными сообщениями, пушить с подтверждением"

expectations:
  - from: "R5 Архитектор"
    expects: "Архитектура определена, ArchGate пройден"
  - from: "R15 Валидатор"
    expects: "Код работает, тесты проходят"
  - from: "R14 Заказчик"
    expects: "Минимальные изменения — не over-engineer"

methods:
  - name: "Incremental Implementation"
    description: "Мелкие коммиты, каждый — рабочее состояние"
  - name: "Read-Before-Edit"
    description: "Понять существующий код ДО модификации"
  - name: "Pilot-First Deployment"
    description: "new-architecture → pilot branch → тестирование → prod"

work_products:
  - product: "Код (коммиты)"
    recipient: "Git repo → R15 Валидатор (review) → prod"
    trigger: "РП назначен, архитектура определена"
  - product: "Capture (паттерн/антипаттерн)"
    recipient: "R2 Экстрактор (через capture-to-pack)"
    trigger: "Обнаружен при кодировании"

current_holders:
  - holder: "A1 Claude (CLI interactive)"
    grade: 3
    covers_scenarios: [Implementation, Refactoring, Bug-Fix]

failure_modes:
  - "Over-Engineering — добавлены фичи, не запрошенные заказчиком"
  - "Security Hole — введена OWASP-уязвимость"

related_roles:
  - role: "R5 Архитектор"
    interaction: "Архитектор → решение → Кодировщик реализует"
  - role: "R7 Триажёр"
    interaction: "Триажёр приоритизирует backlog → Кодировщик берёт задачи"
  - role: "R11 Наладчик"
    interaction: "Наладчик L4 → GitHub Issue → Кодировщик фиксит"
```
</details>

<details>
<summary><strong>R7 Триажёр техдолга</strong> — полное описание (DP.D.033)</summary>

```yaml
name: "Триажёр техдолга"
type: agential
suprasystem: "Платформа DP"
context: "Двухуровневый триаж: auto-classify (Grade 1) + review (Grade 3)"

obligations:
  - "Auto-triage: классифицировать каждый helpful=false / ✏️ comment в реальном времени"
  - "Review: при открытии сессии техдолга проверить предклассифицированный backlog"
  - "Категоризировать: L(atency), C(orrectness), U(sability), K(nowledge)"
  - "Оценивать severity: low/medium/high/critical"
  - "Кластеризовать: группировать похожие проблемы (onboarding, content, ...)"
  - "Алертить: severity >= high ИЛИ user_comment → TG alert в реальном времени"
  - "Обновлять WP-debt backlog (приоритизированный список)"

expectations:
  - from: "R6 Кодировщик"
    expects: "Backlog приоритизирован, задачи конкретны"
  - from: "R14 Заказчик"
    expects: "Критичное — алерт сразу, некритичное — не копится"
  - from: "R8 Синхронизатор"
    expects: "Weekly report (unsatisfied-questions.md) из feedback_triage DB"

methods:
  - name: "Auto-Triage (Grade 1)"
    description: "helpful=false → Haiku classify → feedback_triage DB → TG alert if high"
  - name: "Issue Funnel Review (Grade 3)"
    description: "feedback_triage DB + fleeting-notes + captures → review → prioritize → backlog"
  - name: "Impact Assessment"
    description: "Сколько пользователей затронуто × severity × effort"

work_products:
  - product: "feedback_triage DB record"
    recipient: "R7 Review, R8 Синхронизатор (отчёт)"
    trigger: "Каждый helpful=false или ✏️ comment"
  - product: "TG alert"
    recipient: "R14 Заказчик"
    trigger: "severity >= high ИЛИ user_comment"
  - product: "Приоритизированный backlog (WP-debt)"
    recipient: "R6 Кодировщик, R14 Заказчик"
    trigger: "Открытие сессии техдолга"

scenarios:
  - name: "Auto-Triage"
    trigger: "helpful=false callback OR user_comment saved"
    min_agency_grade: 1
    method: "Auto-Triage (Grade 1)"
    inputs: [qa_history record]
    work_product: "feedback_triage DB record + TG alert"
  - name: "Triage-Session"
    trigger: "Открытие сессии техдолга (WP-7)"
    min_agency_grade: 3
    method: "Issue Funnel Review (Grade 3)"
    inputs: [feedback_triage DB, fleeting-notes.md, captures.md]
    work_product: "Приоритизированный backlog"

current_holders:
  - holder: "Bot process (core/feedback_triage.py, Haiku)"
    grade: 1
    covers_scenarios: [Auto-Triage]
  - holder: "A1 Claude (CLI interactive)"
    grade: 3
    covers_scenarios: [Triage-Session]

failure_modes:
  - "Backlog Bloat — замечания копятся без review (mitigated by auto-triage + alerts)"
  - "Alert Fatigue — слишком много high-severity алертов (monitor, tune threshold)"

related_roles:
  - role: "R6 Кодировщик"
    interaction: "Триажёр приоритизирует → Кодировщик реализует"
  - role: "R8 Синхронизатор"
    interaction: "Синхронизатор генерирует weekly report из feedback_triage DB"
  - role: "R11 Наладчик"
    interaction: "L4 escalations → попадают в triage"
```
</details>

#### Функциональные роли (Grade 0+, со смешанными сценариями)

| # | Роль | Suprasystem | Сценарии (Grade) | Текущие Holders | Рабочие продукты |
|---|------|-------------|------------------|-----------------|------------------|
| R8 | **Синхронизатор** | Экзокортекс | Scheduler Dispatch (0), Code-Scan (0), Pack Projection (0), Notify (0), Daily Report (0), Consistency Check (0), Pack README Gen (0), **Consistency Audit (2)**, **Unsatisfied Report (1)** | I2 bash (Grade 0-1), A1 Claude (Grade 2+) | Consistency Report, Projections, TG Notifications |
| R9 | **Шаблонизатор** | Экзокортекс | Template Sync (0), **Drift Detection (2)**, **Semantic Validation (2)**, First-Time Setup (0), User Update (0), Generativity Check (0) | I3 bash (Grade 0), A1 Claude (Grade 2) | Актуальный генеративный шаблон, Drift Report |
| R10 | **Статистик** | Платформа DP | Сбор метрик (1), **Analytics Report (2)** | I6 bash (Grade 1), A1 Claude (Grade 2) | Метрики, /analytics |
| R11 | **Наладчик** | Платформа DP | L1 Unstick (1), L3 Restart (0), **L2 Auto-fix (2)**, **L4 Escalate (3)** | I7 bash/bot (Grade 0-1), A1 Claude (Grade 2-3) | Fix PRs, Restarts, GitHub Issues |
| R12 | **Оценщик** | Платформа DP | Bloom Eval (2), WP Validation (1-2), Fixation (1) | I8 bot code (Grade 1), A1 Claude (Grade 2) | Оценки, валидации, заметки |
| R13 | **Проводник** | Платформа DP | FSM Routing (1), Access Control (0) | I1 Bot (Grade 1) | Маршрутизация, gating |
| R21 | **Публикатор** | Экосистема | Daily Scan (1), Scheduled Publish (0), Manual Publish (1), Comment Check (0) | I1 Bot + I9 (Grade 1) | Опубликованные посты, расписание, уведомления |

> **R8-R12, R21** — полное описание роли: `DS-ai-systems/<system>/system.yaml` (synchronizer, setup, pulse, fixer, evaluator, publisher)

<details>
<summary><strong>R13 Проводник</strong> — полное описание (DP.D.033)</summary>

```yaml
name: "Проводник"
type: functional
suprasystem: "Платформа DP"
context: "Маршрутизация пользователя по FSM-сценариям, контроль доступа по tier"

obligations:
  - "Маршрутизировать пользователя между FSM-состояниями (марафон/лента/Q&A)"
  - "Контролировать доступ к функциям по tier (T1-T5)"
  - "Показывать кнопки и команды соответственно tier"
  - "Не допускать dead-ends в FSM (все состояния имеют выход)"
  - "Перенаправлять на оплату при попытке доступа к закрытым функциям"

expectations:
  - from: "R16 Ученик"
    expects: "Понятная навигация, кнопки соответствуют контексту"
  - from: "R3 Консультант"
    expects: "FSM-контекст корректно передан для генерации"
  - from: "R12 Оценщик"
    expects: "После оценки FSM переходит к следующему шагу"

methods:
  - name: "FSM Routing"
    description: "aiogram FSM → определение текущего состояния → доступные переходы"
  - name: "Tier Gating"
    description: "user_profile.tier → набор доступных команд и кнопок"
  - name: "Progressive Disclosure"
    description: "Показывать функции постепенно по мере роста tier"

work_products:
  - product: "FSM Transition"
    recipient: "R16 Ученик (TG keyboard/inline buttons)"
    trigger: "Пользователь нажал кнопку или ввёл команду"
  - product: "Access Control Decision"
    recipient: "R3 Консультант, R12 Оценщик (разрешение/запрет)"
    trigger: "Каждый запрос пользователя"

current_holders:
  - holder: "I1 Бот (aiogram FSM, middleware)"
    grade: 1
    covers_scenarios: [FSM-Routing, Access-Control, Tier-Gating]

failure_modes:
  - "Dead-End State — пользователь застрял без кнопок выхода"
  - "Tier Leak — пользователь получил доступ к функции выше своего tier"

related_roles:
  - role: "R3 Консультант"
    interaction: "Проводник определяет контекст → Консультант генерирует контент"
  - role: "R12 Оценщик"
    interaction: "Оценщик завершает оценку → Проводник переводит в следующее состояние"
  - role: "R11 Наладчик"
    interaction: "Наладчик L1 расклинивает застрявших пользователей"
  - role: "R16 Ученик"
    interaction: "Прямое взаимодействие: кнопки, команды, навигация"
```
</details>

<details>
<summary><strong>R21 Публикатор</strong> — полное описание (DP.D.033)</summary>

```yaml
name: "Публикатор"
type: functional
suprasystem: "Экосистема"
context: "Автономная публикация готовых постов на внешние платформы по расписанию и по команде"

obligations:
  - "Сканировать индекс знаний на посты с status=ready и target=club"
  - "Составлять расписание публикаций (каденция: Пн/Ср/Пт 10:00, настраиваемо)"
  - "Публиковать посты по расписанию через Discourse API"
  - "Публиковать конкретный пост по команде пользователя — вне расписания"
  - "Перестраивать график после ручной публикации и согласовывать с пользователем"
  - "Уведомлять пользователя о каждой публикации (TG + ссылка)"
  - "Запрашивать новые посты, когда в очереди < 2"
  - "Отслеживать комментарии к опубликованным постам (polling)"
  - "Обновлять frontmatter поста (status→published) после публикации"

expectations:
  - from: "R4 Автор"
    expects: "Посты в индексе знаний со status: ready и target: club"
  - from: "R14 Заказчик"
    expects: "Публикация по расписанию без ручного вмешательства; уведомления; запрос новых постов при истощении очереди"
  - from: "R1 Стратег"
    expects: "Контент-план определяет порядок и приоритет публикаций"

methods:
  - name: "Index Scan"
    description: "GitHub API → DS-Knowledge-Index/docs/ → frontmatter filter (status=ready, target=club) → сравнение с published_posts"
  - name: "Auto-Schedule"
    description: "Новые ready-посты → распределение по ближайшим свободным слотам (FIFO по дате создания)"
  - name: "Schedule Rebuild"
    description: "При ручной публикации: опубликовать указанный пост → сдвинуть остальные → показать новый график → ждать подтверждения"
  - name: "Queue Watch"
    description: "После каждой публикации: pending < 2 → TG-уведомление с подсказкой draft-постов для R4 Автора"
  - name: "Comment Polling"
    description: "Каждые 15 мин: get_topic → сравнить posts_count → уведомить при изменении"

work_products:
  - product: "Опубликованный пост (Discourse topic)"
    recipient: "systemsworld.club → R14 Заказчик (TG notification + ссылка)"
    trigger: "schedule_time <= NOW() или команда пользователя"
  - product: "Расписание публикаций"
    recipient: "R14 Заказчик (/club schedule)"
    trigger: "Новый ready-пост обнаружен или ручная команда"
  - product: "Запрос новых постов"
    recipient: "R14 Заказчик → R4 Автор"
    trigger: "Очередь < 2 постов"
  - product: "Уведомление о комментарии"
    recipient: "R14 Заказчик (TG)"
    trigger: "posts_count изменился"

scenarios:
  - name: "Daily Scan + Auto-Schedule"
    trigger: "Scheduler (daily 06:00)"
    min_agency_grade: 1
    method: "Index Scan + Auto-Schedule"
    inputs: [github_api_contents, published_posts_db]
    work_product: "Расписание публикаций + TG-уведомление о новых постах в графике"
  - name: "Scheduled Publish"
    trigger: "Scheduler (*/5 min, schedule_time <= NOW())"
    min_agency_grade: 0
    method: "Discourse API create_topic"
    inputs: [scheduled_publications]
    work_product: "Опубликованный пост + обновлённый frontmatter"
  - name: "Manual Publish"
    trigger: "Пользователь: /club publish «Title»"
    min_agency_grade: 1
    method: "Schedule Rebuild"
    inputs: [user_command, scheduled_publications]
    work_product: "Опубликованный пост + пересмотренный график (с согласованием)"
  - name: "Comment Check"
    trigger: "Scheduler (*/15 min)"
    min_agency_grade: 0
    method: "Comment Polling"
    inputs: [published_posts]
    work_product: "TG-уведомление о новом комментарии + ссылка"

current_holders:
  - holder: "I1 Бот (scheduler + handlers/discourse.py)"
    grade: 1
    covers_scenarios: [Scheduled-Publish, Manual-Publish, Comment-Check]
  - holder: "I9 Публикатор (DS-ai-systems/publisher/)"
    grade: 1
    covers_scenarios: [Daily-Scan, Auto-Schedule]

failure_modes:
  - "Queue Starvation — очередь пуста, публикация прекращается без уведомления"
  - "Ghost Publish — пост опубликован, но frontmatter не обновлён (status drift)"
  - "Schedule Desync — расписание не соответствует реальным публикациям"

related_roles:
  - role: "R4 Автор"
    interaction: "Автор пишет пост (status: ready, target: club) → Публикатор подхватывает и публикует"
  - role: "R1 Стратег"
    interaction: "Стратег определяет content plan → приоритет публикаций"
  - role: "R8 Синхронизатор"
    interaction: "Синхронизатор может вызывать Daily Scan по расписанию (scheduler dispatch)"
  - role: "R14 Заказчик"
    interaction: "Заказчик получает уведомления, даёт команды на публикацию, согласует перестроенный график"
```
</details>

#### Роли Пользователя (A2) — 7 ролей

| # | Роль | Контекст |
|---|------|----------|
| R14 | Заказчик | Формулирует задачи для Claude |
| R15 | Валидатор | Human-in-the-loop: одобряет KE, решения, PR |
| R16 | Ученик | Учится в боте (марафон/лента) |
| R17 | Стратег (интерактив) | Участвует в strategy-session |
| R18 | Автор заметок | Пишет .заметки в TG → fleeting-notes |
| R19 | Тестировщик | Проверяет бот (pilot) |
| R20 | Рецензент | Просматривает отчёты, дайджесты |

> **Ключевое:** Стратег (R1) и Экстрактор (R2) не могут работать одновременно (один Claude Code process). Консультант (R3) работает через отдельный Claude API в боте — может параллельно.

**Статистика:** 2 агента (A1 Claude, A2 Пользователь), 9 инструментов (I1-I9), 21 роль (7 агентских + 7 функциональных + 7 пользовательских), ~39 сценариев. Репо: DS-ai-systems (монорепо, 8 систем).

### 3.3. Cross-Pack Role Index

> Каждый Pack владеет своими ролями (DDD: роль = часть Bounded Context). Этот индекс — единая точка входа для навигации по всем ролям экосистемы.

| Pack | Коды | Кол-во | Тип | Файл |
|------|------|--------|-----|------|
| **DP** (Digital Platform) | R1–R21 | 21 | Platform | [§3.2 этого файла](#32-каталог-ролей-платформы) |
| **EDU** (Education) | EDU.R.001–007 | 7 | Domain-agnostic | [PACK-education/02A-roles.md](../../../../PACK-education/pack/education/02-domain-entities/02A-roles.md) |
| **PD** (Personal Dev) | PD.R.001–004 | 4 | Domain-specific | [PACK-personal/02A-roles.md](../../../../PACK-personal/pack/personal-development/02-domain-entities/02A-roles.md) |
| **MIM** (Мастерская) | MIM.R.001–003 | 3 | Domain-specific | [PACK-MIM/MIM.R.*](../../../../PACK-MIM/pack/mim/02-domain-entities/) |

**Классификация (3 уровня):**

```
Level 0: Platform (R1-R21)           ← в шаблоне экзокортекса, нужны всем
Level 1: Domain-agnostic (EDU.R.*)   ← универсальные, для любого предмета
Level 2: Domain-specific (PD/MIM/*)  ← привязаны к одному домену
```

**В шаблоне экзокортекса:** только Level 0 (Platform). Pack-роли доступны по ссылкам при установке соответствующего Pack.

**Итого:** 35 ролей (21 DP + 7 EDU + 4 PD + 3 MIM).

## 4. IPO-паттерн ИИ-системы

Каждая ИИ-система описывается через Вход-Обработка-Выход (см. [DP.ARCH.001](DP.ARCH.001-platform-architecture.md) § 5):

| Элемент | Что описывает |
|---------|--------------|
| **Входы** | Данные из MCP (слой 2), триггеры (cron, команда, событие), внешние источники |
| **Обработка** | LLM-промпт + скрипты + логика маршрутизации |
| **Выходы** | Артефакты (файлы, сообщения), команды другим системам, события |

## 5. Реестр ИИ-систем

### 4.1. Группа: Стратегирование и планирование

| ID | Система | Ориентация | Инициатива | Статус |
|----|---------|-----------|------------|--------|
| AISYS.012 | **[Стратег](DP.AGENT.012-strategist/)** | both | both | draft |

### 4.2. Группа: Обучение и развитие

| ID | Система | Ориентация | Инициатива | Статус |
|----|---------|-----------|------------|--------|
| AISYS.001 | **Проводник** (AST.001) | human | by-request | active |
| AISYS.002 | **RouteGuide** | system | autonomous | active |
| AISYS.003 | **TrajectoryPlanner** | system | autonomous | active |
| AISYS.004 | **DailyAssignmentBuilder** | system | autonomous | stub |
| AISYS.005 | **ProgressAnalyst** | system | both | active |
| AISYS.006 | **RhythmKeeper** | system | autonomous | stub |

### 4.3. Группа: Контент и знания

| ID | Система | Ориентация | Инициатива | Статус |
|----|---------|-----------|------------|--------|
| AISYS.007 | **Генератор инфопродуктов** (AST.002) | system | autonomous | active |
| AISYS.008 | **ДЗ-чекер** (AST.003) | system | by-request | active |
| AISYS.009 | **Консультант** | human | by-request | stub |
| AISYS.010 | **Librarian** | system | autonomous | stub |
| AISYS.011 | **Индексатор знаний** | system | autonomous | stub |
| AISYS.013 | **[Знание-Экстрактор](DP.AISYS.013-knowledge-extractor.md)** | system | both | draft |

### 4.4. Группа: Координация и качество

| ID | Система | Ориентация | Инициатива | Статус |
|----|---------|-----------|------------|--------|
| AISYS.014 | **[Aist Bot](DP.AISYS.014-aist-bot.md)** | human | both | active |
| AISYS.015 | **ConsistencyChecker** | system | autonomous | процессы I2 (Grade 1) |
| AISYS.016 | **SystemArchitect** | human | by-request | stub |

## 6. Два шаблона: Роль и Система

### 6.1. Описание роли (DP.D.033)

> **Первичный шаблон.** Роль описывается НЕЗАВИСИМО от исполнителя. Полный шаблон: [DP.D.033](../01-domain-contract/DP.D.033-role-centric-architecture.md) §3.

```yaml
# Краткая форма (полная — в DP.D.033)
name: "<Роль>"
type: functional | agential
suprasystem: "<Надсистема>"
context: "<BC>"
obligations: [...]
expectations: [{from, expects}]
methods: [{name, description}]
work_products: [{product, recipient, trigger}]
scenarios: [{name, trigger, min_agency_grade, method, inputs, work_product}]
current_holders: [{holder, grade, covers_scenarios}]
failure_modes: [...]
related_roles: [{role, interaction}]
```

### 6.2. Паспорт ИИ-системы (исполнитель)

> **Вторичный шаблон.** Описывает конкретную систему-исполнителя. Ссылается на роли, которые система играет.

```yaml
id: AISYS.XXX
type: ai-system-passport
status: draft | active | deprecated

# Классификационные признаки
orientation: human | system | both
initiative: by-request | autonomous | both
interface: dialogue | api | both

# Какие роли играет (ссылки на каталог §3.2)
plays_roles: [R1, R8, ...]    # Роли из каталога

# IPO
inputs:
  data: [...]
  triggers: [...]
outputs:
  artifacts: [...]
  commands: [...]

# Сценарии (конкретизация сценариев ролей для этой системы)
scenarios: [...]
```

## 7. Процесс добавления/изменения

> Фреймворк стабилен (§1-6). Каталог ролей (§3.2) и реестр исполнителей (§3.1) растут по мере развития. Процесс ниже определяет, что и где менять.

### 7.1. Добавление новой роли

| Шаг | Что | Где | Когда |
|-----|-----|-----|-------|
| 1 | **Описать роль** по шаблону DP.D.033 | §3.2 (каталог ролей) | Потребность определена |
| 2 | **Выбрать исполнителя** из реестра (§3.1) или добавить нового | §3.1 | Роль описана |
| 3 | **Подготовить исполнителя:** prompt (LLM), program (скрипт), train (человек) | DS-ai-systems / промпт / тренинг | Исполнитель выбран |
| 4 | Сценарий (если межсистемный) | DS-ecosystem-development/PROCESSES.md | Новый сценарий |
| 5 | Selective reindex | knowledge-mcp | Pack изменён |

### 7.2. Добавление нового исполнителя

| Шаг | Что | Где | Когда |
|-----|-----|-----|-------|
| 1 | Определить Grade (0-4) | — | Анализ возможностей |
| 2 | Строка в Реестр исполнителей (§3.1) | Этот документ | Исполнитель создан |
| 3 | system.yaml (если инструмент) | DS-ai-systems/ | Система готова |
| 4 | Паспорт AISYS.XXX (если ИИ-система) | Pack + DS-ai-systems | Система готова |
| 5 | Привязать к ролям (current_holders) | §3.2 | Роли назначены |

### 7.3. Изменение существующей роли

| Изменение | Что обновить |
|-----------|-------------|
| Новый сценарий | §3.2 + system.yaml исполнителя + PROCESSES.md |
| Смена исполнителя | §3.2 (current_holders) + system.yaml |
| Upgrade Grade (0→2+) | §3.1 (Grade) + system.yaml + промпт для LLM-сценариев |
| Deprecation | §3.2 (убрать роль) + §3.1 (убрать привязку) |

### 7.4. Подготовка исполнителя (DP.D.033 §4)

| Способ | Для кого | Grade | Артефакт |
|--------|----------|-------|----------|
| Спрограммировать (program) | Скрипт, пайплайн | 0-1 | bash-скрипт, cron job |
| Задать промпт (prompt) | LLM | 2-3 | Файл в prompts/ |
| Обучить (train) | Человек | 3-4 | Тренинг, документация |

## 8. Связанные документы

- [DP.D.033 Role-Centric Architecture](../01-domain-contract/DP.D.033-role-centric-architecture.md) — шаблон описания роли, ArchGate сравнение
- [DP.ARCH.001 Архитектура](DP.ARCH.001-platform-architecture.md)
- [DP.ASSIST.001 ИИ-ассистенты (superseded)](DP.ASSIST.001-ai-assistants.md)
- [DP.SYS.001 Детерминированные системы](DP.SYS.001-deterministic-systems.md)
- [DP.AISYS.013 Знание-Экстрактор](DP.AISYS.013-knowledge-extractor.md)
- [DP.EXOCORTEX.001 Модульный экзокортекс](DP.EXOCORTEX.001-modular-exocortex.md)
- [DS-ai-systems](https://github.com/TserenTserenov/DS-ai-systems) — монорепо всех ИИ-систем (7 систем, system.yaml паспорта)
