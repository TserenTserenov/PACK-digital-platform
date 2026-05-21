---
id: DP.IWE.011-adapter-claude-code
name: Claude Code Adapter for IWE Host Contract
name_ru: Адаптер Claude Code (референсная реализация)
type: adapter
parent: DP.IWE.011
contract_version: v0.1
status: active
created: 2026-05-21
---

# Адаптер Claude Code — референсная реализация контракта DP.IWE.011

> Документирует, как Claude Code реализует каждый компонент Host Contract v0.1.
> Статус: **активен** — это текущая реализация IWE.
> Это не изменение поведения, а формализация существующего.

---

## A. Tool API — маппинг инструментов

| Контракт (абстрактный) | CC-реализация (нативная) | Примечание |
|------------------------|--------------------------|------------|
| `task_tracker.create(tasks[])` | `TodoWrite` — массив `{content, status}` | Нативно поддерживается в CC |
| `task_tracker.update(id, status)` | `TodoWrite` — обновить `status` по `content` как ключу | CC не хранит UUID задач — поиск по content |
| `task_tracker.list()` | `TodoWrite` (чтение) | Возвращает текущий список |
| `scheduler.schedule_at(ts, msg)` | `ScheduleWakeup` — `delaySeconds`, `reason`, `prompt` | Пробуждает CC-процесс; не внешний push |
| `fs.read(path)` | `Read` | Путь = абсолютный |
| `fs.write(path, content)` | `Write` | Перезаписывает целиком; создаёт директории |
| `fs.edit(path, old, new)` | `Edit` — `old_string` / `new_string` | `replace_all: false` — ошибка если не уникально |
| `shell.exec(cmd)` | `Bash` — `command`, опц. `timeout` | stdout+stderr+exit_code |
| `agent.delegate(model, prompt)` | `Agent` — `subagent_type`, `prompt`, `model` | Изолированный контекст |
| `skill.invoke(skill_id, args)` | `Skill` — `skill`, `args` | Ищет `.claude/skills/<id>/SKILL.md` |

**Несоответствия (известные ограничения CC-адаптера):**
- `task_tracker.update(id, ...)` — CC не возвращает ID при create; адаптер использует `content` как суррогатный ключ
- `scheduler.schedule_at(ts, ...)` — CC `ScheduleWakeup` принимает `delaySeconds`, не timestamp; адаптер вычисляет delta
- `agent.delegate` — CC создаёт субагента в том же процессе; настоящая изоляция памяти зависит от версии CC

---

## B. Lifecycle Events — хуки CC

| Контракт (событие) | CC-механизм | Хук | Payload |
|--------------------|-------------|-----|---------|
| `SESSION_START` | `UserPromptSubmit` hook | `wakatime-heartbeat.sh` (старт) | session_id из timestamp |
| `SESSION_END` | `Stop` hook | `wakatime-heartbeat.sh` (стоп), `agent-trace-recorder.sh` | duration_sec, exit из env |
| `TOOL_EXECUTED` | `PostToolUse` hook | `agent-trace-recorder.sh`, `wakatime-heartbeat.sh` | tool_name из `$TOOL_NAME`, result из `$TOOL_RESULT` |

**Регистрация хуков:**

```json
// .claude/settings.json
{
  "hooks": {
    "UserPromptSubmit": [{"hooks": [{"type": "command", "command": "bash ~/.claude/hooks/wakatime-heartbeat.sh"}]}],
    "PostToolUse": [{"hooks": [{"type": "command", "command": "bash ~/.claude/hooks/wakatime-heartbeat.sh"}]}, {"hooks": [{"type": "command", "command": "bash ~/.claude/hooks/agent-trace-recorder.sh"}]}],
    "Stop": [{"hooks": [{"type": "command", "command": "bash ~/.claude/hooks/wakatime-heartbeat.sh"}]}, {"hooks": [{"type": "command", "command": "bash ~/.claude/hooks/agent-trace-recorder.sh"}]}]
  }
}
```

---

## C. Environment Variables — CC-адаптер устанавливает

Переменные устанавливаются в `settings.json → env` или вычисляются в хуке при старте:

| Переменная (контракт) | CC-источник | Значение |
|----------------------|-------------|---------|
| `IWE_RUNTIME` | Статически в `settings.json → env` | `"claude-code"` |
| `AGENT_SESSION_ID` | Хук генерирует при `SESSION_START`: `date +%s%N | md5` | UUID-like строка |
| `AGENT_TASK_ID` | `$CLAUDE_TASK_ID` (env от iwe-agent-dispatcher.py) | Может быть пустым для ручных сессий |
| `AGENT_MODEL_ID` | `$CLAUDE_AGENT_ID` (env CC) | `claude-sonnet-4-6` или иная |
| `IWE_STATE_DIR` | Статически: `~/.iwe/state/` | Создаётся при первом использовании |

**Добавить в `.claude/settings.json` (если ещё не сделано):**

```json
{
  "env": {
    "IWE_RUNTIME": "claude-code",
    "IWE_STATE_DIR": "~/.iwe/state/"
  }
}
```

`AGENT_SESSION_ID`, `AGENT_TASK_ID`, `AGENT_MODEL_ID` — динамические, устанавливаются в `wakatime-heartbeat.sh` при `SESSION_START` и экспортируются через `~/.iwe/state/current-session.env`.

---

## D. Конфигурация скиллов в CC

Скиллы регистрируются через структуру директорий:
- Путь: `.claude/skills/<skill_id>/` + `SKILL.md` + точка входа
- Активация: `/skill_id` в чате или фраза-триггер из SKILL.md `triggers.phrases`
- Каталог: генерируется `generate-skills-catalog.sh` в `skills-catalog.yaml`

---

## Известные отклонения от контракта

| Компонент | Контракт требует | CC реализует | Статус |
|-----------|-----------------|--------------|--------|
| task_tracker ID | UUID для update | content как суррогатный ключ | ⚠️ Workaround |
| SESSION_END duration | seconds float | Вычисляется из diff timestamp | ⚠️ Приближение |
| TOOL_EXECUTED result | Полный result | Первые 500 символов | ⚠️ Truncated |
| skill.invoke return | Структурированный ответ | Текст ответа агента | ⚠️ Нет схемы |

Отклонения допустимы в v0.1; v1.0 потребует устранить.
