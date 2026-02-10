---
id: DP.AGENT.001
type: domain-entity
status: active
created: 2026-02-07
updated: 2026-02-10
trust:
  F: 4
  G: domain
  R: 0.7
epistemic_stage: formed
note: "Retitled from 'ИИ-агенты' to 'ИИ-системы'. ID preserved for backward compatibility."
---

# ИИ-системы платформы

## 1. Определение

**ИИ-система** — компонент платформы (слой 3), использующий LLM для выполнения функций: принятие решений, генерация, диалог, анализ.

> ИИ-агент ≠ ИИ-ассистент (D.DTP.009). Это **виды** ИИ-системы, а не разные архитектурные слои.

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

## 3. IPO-паттерн ИИ-системы

Каждая ИИ-система описывается через Вход-Обработка-Выход (см. [DP.ARCH.001](DP.ARCH.001-platform-architecture.md) § 5):

| Элемент | Что описывает |
|---------|--------------|
| **Входы** | Данные из MCP (слой 2), триггеры (cron, команда, событие), внешние источники |
| **Обработка** | LLM-промпт + скрипты + логика маршрутизации |
| **Выходы** | Артефакты (файлы, сообщения), команды другим системам, события |

## 4. Реестр ИИ-систем

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
| AISYS.014 | **Orchestrator** | system | autonomous | stub |
| AISYS.015 | **ConsistencyChecker** | system | autonomous | stub |
| AISYS.016 | **SystemArchitect** | human | by-request | stub |

## 5. Паспорт ИИ-системы (шаблон)

```yaml
id: AISYS.XXX
type: ai-system-passport
status: draft | active | deprecated

# Классификационные признаки (определяют вид: агент / ассистент)
orientation: human | system | both
initiative: by-request | autonomous | both
interface: dialogue | api | both

# IPO
inputs:
  data: [...]         # Данные из MCP (слой 2)
  triggers: [...]     # События-триггеры
  external: [...]     # Внешние источники

processing:
  llm: {model, temperature}
  prompts: [...]      # Шаблоны промптов
  logic: [...]        # Скриптовая логика

outputs:
  artifacts: [...]    # Рабочие продукты
  commands: [...]     # Команды другим системам
  events: [...]       # Генерируемые события

# Инструменты
tools:
  mcp: [...]          # MCP-инструменты (слой 2)

# Метрики
metrics: [...]

# Сценарии
scenarios: [...]      # Список сценариев (как у Стратега)
```

## 6. Взаимодействие ИИ-систем

```
Проводник (human, dialogue)
    ├── → RouteGuide (стратегия маршрута)
    │        └── → TrajectoryPlanner (детализация)
    │                 └── → DailyAssignmentBuilder (задания)
    ├── → ProgressAnalyst (анализ)
    └── → RhythmKeeper (напоминания)

Стратег (both, both)
    ├── → digital-twin-mcp (данные)
    └── → user-repos MCP (файлы)

Знание-Экстрактор (system, both) → реализация: extractor-agent
    ├── → pack-MCP (структура пака)
    ├── → user-repos MCP (источники)
    └── → guides-mcp (нормативы)
```

## 7. Структура репо ИИ-системы

```
<ai-system-name>/
├── CLAUDE.md           # Системный промпт
├── config.yaml         # Capabilities + MCP + расписание
├── prompts/            # Шаблоны промптов для сценариев
├── scripts/            # Логика, не входящая в промпт
├── api/                # API-слой (HTTP/MCP)
└── tests/
```

## 8. Связанные документы

- [DP.ARCH.001 Архитектура](DP.ARCH.001-platform-architecture.md)
- [DP.ASSIST.001 ИИ-ассистенты (superseded)](DP.ASSIST.001-ai-assistants.md)
- [DP.SYS.001 Детерминированные системы](DP.SYS.001-deterministic-systems.md)
- [DP.AISYS.013 Знание-Экстрактор](DP.AISYS.013-knowledge-extractor.md)
- [DP.EXOCORTEX.001 Модульный экзокортекс](DP.EXOCORTEX.001-modular-exocortex.md)
