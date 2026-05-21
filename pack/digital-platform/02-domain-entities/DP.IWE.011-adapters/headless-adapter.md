---
id: DP.IWE.011-adapter-headless
name: Headless Adapter for IWE Host Contract
name_ru: Headless-адаптер (Agent Inbox как runtime)
type: adapter
parent: DP.IWE.011
contract_version: v0.1
status: active
created: 2026-05-21
entry_point: headless-runner.sh
---

# Headless-адаптер — реализация контракта DP.IWE.011 через Agent Inbox

> Документирует, как Agent Inbox (WP-324) + `iwe-agent-dispatcher.py` реализуют Host Contract v0.1.
> Хост: `IWE_RUNTIME=headless` — нет открытого Claude Code окна, нет интерактивного UI.
> Точка входа: `headless-runner.sh` — принимает протокол + задачу, устанавливает env, запускает `claude -p`.

---

## A. Tool API — маппинг инструментов

| Контракт (абстрактный) | Headless-реализация | Примечание |
|------------------------|---------------------|------------|
| `task_tracker.create(tasks[])` | Файл `inbox/agent/tasks/TASK-*.md` + `status: pending` в frontmatter | Создаётся до запуска runner'а или внутри `claude -p` через `Write` |
| `task_tracker.update(id, status)` | `update_task_frontmatter()` в dispatcher — патчит frontmatter по task_id | UUID = имя файла (TASK-<id>.md) |
| `task_tracker.list()` | `find_pending_tasks()` — сканирует `tasks/*.md` по `status: pending` | Используется dispatcher'ом перед вызовом claude |
| `scheduler.schedule_at(ts, msg)` | cron / systemd-timer (`*/30 * * * *` → `iwe-agent-dispatcher.py`) + `due:` поле в task frontmatter | `schedule_at` = записать task с `due: <timestamp>`, dispatcher возьмёт по расписанию |
| `fs.read(path)` | `Read` (нативный инструмент `claude -p`) | Без изменений |
| `fs.write(path, content)` | `Write` (нативный инструмент `claude -p`) | Без изменений |
| `fs.edit(path, old, new)` | `Edit` (нативный инструмент `claude -p`) | Без изменений |
| `shell.exec(cmd)` | `Bash` (нативный инструмент `claude -p`) | Без изменений |
| `agent.delegate(model, prompt)` | `Agent` (нативный инструмент `claude -p`) | В `claude -p` поддерживается |
| `skill.invoke(skill_id, args)` | `Skill` (нативный инструмент `claude -p`) | SKILL.md доступны если `$CLAUDE_CONFIG_DIR` указан |

**Ключевое отличие от CC-адаптера:**
- `task_tracker.*` — не эфемерный TodoWrite (в памяти процесса), а файловая система. Задачи персистентны между сессиями.
- `scheduler.schedule_at` — не `ScheduleWakeup` (не будит процесс), а `due:` поле + cron-цикл dispatcher'а.

---

## B. Lifecycle Events — как headless генерирует события

В CC жизненный цикл = хуки (`PostToolUse`, `Stop`, `UserPromptSubmit`).
В headless те же хуки **работают** при `claude -p` — это ключевое свойство Claude CLI.

| Контракт (событие) | Headless-механизм | Хук | Payload |
|--------------------|-------------------|-----|---------|
| `SESSION_START` | `claude -p` вызывает `UserPromptSubmit` hook | `wakatime-heartbeat.sh` | session_id из `AGENT_SESSION_ID` (env) |
| `SESSION_END` | `claude -p` завершается → `Stop` hook | `wakatime-heartbeat.sh` + `agent-trace-recorder.sh` | duration, exit code |
| `TOOL_EXECUTED` | Каждый инструмент → `PostToolUse` hook | `agent-trace-recorder.sh` | tool_name, result (first 500 chars) |

**Важно:** хуки `.claude/hooks/*.sh` срабатывают при `claude -p` **так же**, как при интерактивном использовании. Это не нужно имитировать — оно работает из коробки.

---

## C. Environment Variables — headless-runner.sh устанавливает

`headless-runner.sh` устанавливает env перед вызовом `iwe-agent-dispatcher.py`:

| Переменная (контракт) | Headless-источник | Значение |
|----------------------|-------------------|---------|
| `IWE_RUNTIME` | Статически в `headless-runner.sh` | `"headless"` |
| `AGENT_SESSION_ID` | `date +%s%N \| md5 \| cut -c1-16` в runner'е | Генерируется при каждом вызове |
| `AGENT_TASK_ID` | Из `--task` аргумента runner'а (или auto из pending) | Имя файла TASK-*.md без расширения |
| `AGENT_MODEL_ID` | `--model` аргумент runner'а (дефолт: `sonnet`) | `claude-sonnet-4-6` |
| `IWE_STATE_DIR` | Из env или дефолт: `~/.iwe/state/` | Создаётся автоматически |
| `CLAUDE_TASK_ID` | Alias = `AGENT_TASK_ID` для CC-совместимости | Нужен для `agent-trace-recorder.sh` |

**Запись session env:** runner записывает `~/.iwe/state/current-session.env` (тот же формат, что CC-адаптер) — для совместимости с хуками.

---

## D. Конфигурация скиллов в headless

Скиллы доступны при `claude -p` если:
1. `CLAUDE_CONFIG_DIR` указывает на директорию с `.claude/skills/` (дефолт: `~/.claude`)
2. Агент вызывает `Skill` инструмент по имени

Для IWE-протоколов через headless:
- `open` → задача с шаблоном `protocol-open` из `inbox/agent/templates/protocol-open.md`
- `close` → задача с шаблоном `protocol-close`
- произвольный скилл → `skill.invoke(id)` внутри `claude -p` сессии

---

## E. Покрытие HIGH-зависимостей из Ж-Ф1

| # | Зависимость CC | Headless-эквивалент | Статус |
|---|---------------|---------------------|--------|
| 1 | `TodoWrite` | `tasks/TASK-*.md` файловая система | ✅ Покрыто |
| 2 | `ScheduleWakeup` | `due:` в frontmatter + cron dispatcher | ✅ Покрыто |
| 3 | `Read/Edit/Write` | Нативные инструменты `claude -p` | ✅ Без изменений |
| 4 | `Bash` | Нативный инструмент `claude -p` | ✅ Без изменений |
| 5 | `PostToolUse/Stop` хуки | Те же хуки срабатывают при `claude -p` | ✅ Без изменений |
| 6 | `Agent/Skill` инструменты | Нативные в `claude -p` | ✅ Без изменений |

**Вывод:** все 6 HIGH-зависимостей покрыты без изменения хуков или протоколов.

---

## Известные отклонения от контракта

| Компонент | Контракт требует | Headless реализует | Статус |
|-----------|-----------------|-------------------|--------|
| `task_tracker.list()` realtime | Список задач в текущем контексте агента | Файловый скан до запуска claude | ⚠️ Pre-session только |
| `task_tracker.update()` из агента | Агент обновляет task mid-session | dispatcher обновляет до/после claude | ⚠️ Только dispatcher |
| Interactive clarification | Агент может задать вопрос пользователю | Нет UI — вопросы без ответа | ⚠️ Задачи должны быть самодостаточны |
| Real-time output | Пользователь видит вывод в интерфейсе | Всё в RESULT-*.md post-factum | ⚠️ Асинхронный результат |

Отклонения приемлемы для автономных задач (Agent Inbox design intent).
