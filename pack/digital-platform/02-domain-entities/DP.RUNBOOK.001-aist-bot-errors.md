---
id: DP.RUNBOOK.001
name: "Runbook: Aist Bot Errors"
type: domain-entity
status: draft
summary: "Каталог типичных ошибок Aist Bot с классификацией по уровням (L1-L4), автоматическими действиями и ручными fix'ами"
created: 2026-02-18
trust:
  F: 3
  G: domain
  R: 0.4
epistemic_stage: emerging
related:
  - id: DP.AISYS.014
    type: extends
  - id: DP.AGENT.001
    type: uses
---

# Runbook: Aist Bot Errors

## 1. Определение

**Runbook** — каталог известных паттернов ошибок с инструкциями по автоматическому и ручному исправлению. Source-of-truth для Наладчика (I7).

## 2. Уровни реагирования

| Уровень | Название | Автономность | Время | Кто выполняет |
|---------|----------|-------------|-------|---------------|
| **L1** | Unstick | Полная (без человека) | <1 мин | I1 (bot, in-process) |
| **L2** | Auto-fix | Через PR (human review) | 5-15 мин | I7 (CLI) → GitHub PR |
| **L3** | Restart | Автоматический (по порогу) | 1-3 мин | Script (Railway API) |
| **L4** | Escalate | Уведомление человеку | <1 мин | I7 → GitHub Issue + TG |

## 3. Категории ошибок

### 3.1. FSM (State Machine)

| Паттерн | Симптомы | Severity | Auto Action | Manual Fix |
|---------|----------|----------|-------------|------------|
| **Dead-end state** | Пользователь не может выйти из состояния, нет кнопок | L1 | Reset → mode_select | Добавить transition в transitions.yaml |
| **Missing handler** | `No handler for state X`, traceback в error_logs | L1 | Reset → mode_select | Создать handler для нового state |
| **Stuck user** | Пользователь в одном state >60 мин без активности | L1 | Reset → mode_select + извинение | Проверить UX: почему застревают |
| **Repeated errors** | 3+ ошибки одного user_id за 5 мин | L1 | Reset → mode_select + извинение | Анализ error_logs → root cause |
| **State corruption** | FSM state не совпадает с DB current_state | L2 | PR: sync state | Проверить все пути обновления state |

### 3.2. DB (Database / Neon)

| Паттерн | Симптомы | Severity | Auto Action | Manual Fix |
|---------|----------|----------|-------------|------------|
| **Pool exhaustion** | `too many connections`, все запросы висят | L3 | Restart бот (освободить pool) | Увеличить pool_size, проверить leaks |
| **Connection timeout** | `connection timed out`, latency >10с | L3 | Restart + keep-alive ping | Проверить Neon status, region |
| **Query timeout** | Один запрос >30с, остальные в очереди | L2 | PR: optimize query | Добавить index, переписать запрос |
| **Migration failure** | `relation X does not exist` после деплоя | L4 | Escalate → Issue | Ручной запуск CREATE TABLE |

### 3.3. Claude API

| Паттерн | Симптомы | Severity | Auto Action | Manual Fix |
|---------|----------|----------|-------------|------------|
| **429 Rate limit** | `rate_limit_error`, пользователь ждёт | L1 | Retry с exponential backoff (уже в коде) | Увеличить лимит в Anthropic Console |
| **529 Overloaded** | `overloaded_error`, все Claude-запросы fail | L1 | Degrade: показать cached content | Подождать, мониторить status.anthropic.com |
| **Timeout** | Claude >30с, пользователь ушёл | L1 | Retry 1 раз, затем fallback message | Оптимизировать промпт (длина context) |
| **Invalid response** | Claude вернул невалидный JSON/format | L2 | PR: fix parsing | Обновить промпт + добавить validation |

### 3.4. Telegram API

| Паттерн | Симптомы | Severity | Auto Action | Manual Fix |
|---------|----------|----------|-------------|------------|
| **Flood control** | `RetryAfter: X seconds` | L1 | Задержка X секунд (aiogram auto) | Снизить частоту отправки |
| **Blocked by user** | `Forbidden: bot was blocked` | L1 | Skip + пометить в DB | Не отправлять больше этому user |
| **Chat not found** | `chat not found` | L1 | Skip + лог | Очистить из scheduled messages |
| **Message too long** | `message is too long` | L2 | PR: add truncation | Проверить все точки генерации текста |

### 3.5. MCP (knowledge-mcp)

| Паттерн | Симптомы | Severity | Auto Action | Manual Fix |
|---------|----------|----------|-------------|------------|
| **Connection failure** | `MCP connection failed`, консультант не работает | L3 | Retry 3x, затем fallback без MCP | Проверить CF Workers status |
| **Timeout** | MCP >5с, пользователь ждёт | L1 | Fallback: ответ без MCP knowledge | Оптимизировать запрос / index |
| **Invalid response** | MCP вернул невалидный формат | L2 | PR: fix parsing | Проверить MCP server logs |

### 3.6. Scheduler

| Паттерн | Симптомы | Severity | Auto Action | Manual Fix |
|---------|----------|----------|-------------|------------|
| **Job failure** | Scheduled job бросил exception | L1 | Лог в error_logs, retry в след. цикл | Проверить error_logs → fix |
| **Pre-gen timeout** | Feed generation >60с для одного user | L1 | Skip user, retry в след. цикл | Оптимизировать промпт / batch |
| **Reminder failure** | Не отправились напоминания | L1 | Retry через 5 мин | Проверить schedule_time, blocked users |
| **Scheduler stuck** | Scheduler не запускается / висит | L4 | Escalate → restart | Проверить Railway logs, asyncio deadlock |

## 4. Проекция в Downstream

| Source (Pack) | Target (Downstream) | Формат |
|---------------|---------------------|--------|
| Эта сущность (§3) | `DS-fixer-agent/runbook/patterns.yaml` | YAML (runtime) |
| Эта сущность (§3) | `core/unstick.py` (L1 in-process) | Python (embedded) |

## 5. Связанные документы

- [DP.AISYS.014 Aist Bot](DP.AISYS.014-aist-bot.md) — паспорт бота
- [DP.AGENT.001 ИИ-агенты](DP.AGENT.001-ai-agents.md) — I7 Наладчик
