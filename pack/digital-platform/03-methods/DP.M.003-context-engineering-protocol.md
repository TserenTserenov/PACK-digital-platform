---
id: DP.M.003
name: Context Engineering Protocol
type: method
status: draft
summary: "Метод проектирования контекста ИИ-агента: Write/Select/Compress/Isolate → CLAUDE.md + memory/ + Pack layers"
created: 2026-02-13
trust:
  F: 3
  G: domain
  R: 0.6
related:
  uses: [DP.SOTA.002, DP.EXOCORTEX.001]
  integrates_with: [DP.M.001]
  enables: [DP.ARCH.001]
tags: [context-engineering, method, claude-md, memory, layers, agent-design]
---

# Context Engineering Protocol

## 1. Определение

Метод проектирования и управления контекстом ИИ-агента через четыре стратегии (Write/Select/Compress/Isolate), операционализированный в 3-слойной архитектуре инструкций экзокортекса.

## 2. IPO-паттерн

| Элемент | Описание |
|---------|----------|
| **Входы** | Задача агента + доступные знания (Pack, memory, context files) |
| **Обработка** | Write → Select → Compress → Isolate |
| **Выходы** | Оптимизированный контекст агента (CLAUDE.md + memory/ + загруженные entities) |

## 3. Четыре стратегии

### 3.1. Write (создание артефактов)

Создавать правильные артефакты для контекста агента:

| Артефакт | Когда создавать | Размер |
|----------|----------------|--------|
| CLAUDE.md (root) | Кросс-репозиторные правила | ≤200 строк (MEMORY.md) |
| repo CLAUDE.md | Специфика одного репо | Без лимита |
| memory/*.md | Справочные знания | ≤100 строк/файл, ≤10 файлов |
| Pack entity | Доменное знание | ≤10K символов |
| WP context file | Контекст рабочего продукта | Свободный |

### 3.2. Select (выбор релевантного)

Для каждой задачи — минимальный достаточный контекст:

| Уровень | Что загружать | Когда |
|---------|---------------|-------|
| Always | CLAUDE.md + MEMORY.md | Каждая сессия |
| On-demand | memory/*.md по теме | Когда тема всплывает |
| Точечно | Pack entity по ID | Когда нужны детали |
| Context file | WP-N-slug.md | При работе над конкретным РП |

### 3.3. Compress (сжатие до минимума)

- summary в frontmatter ≤150 символов
- memory/ файлы ≤100 строк
- MEMORY.md — только блокирующее + навигация
- Правило: если информация не нужна в каждой сессии → она не в MEMORY.md

### 3.4. Isolate (изоляция контекстов)

- Каждый Pack = отдельный bounded context
- Каждый repo CLAUDE.md = изолированные инструкции
- Агенты не видят контекст друг друга (кроме явных контрактов)
- Platform-space ≠ User-space (DP.D.011)

## 4. Чеклист при проектировании агента

- [ ] Write: все необходимые артефакты созданы?
- [ ] Select: определён протокол загрузки (что always, что on-demand)?
- [ ] Compress: нет ли лишнего в always-in-context?
- [ ] Isolate: контекст агента не пересекается с другими без контрактов?

## 5. Связанные документы

- [DP.SOTA.002](../06-sota/DP.SOTA.002-context-engineering.md) — SOTA-источник
- [DP.EXOCORTEX.001](../02-domain-entities/DP.EXOCORTEX.001-modular-exocortex.md) — архитектура инструкций
