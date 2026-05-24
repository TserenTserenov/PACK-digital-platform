---
id: DP.M.159
name: "Скилл как единственная исполняемая точка входа"
type: method
pack: PACK-digital-platform
domain: skill-distribution
trust: 0.85
epistemic_stage: established
valid_from: 2026-05-22
pack_refs:
  - source: DP.IWE.001
    relation: applies-to
---

# DP.M.159 — Скилл как единственная исполняемая точка входа

## Суть

Фраза-триггер на человеческом языке активирует `.claude/skills/<name>/SKILL.md`, а не ad-hoc-файл инструкций в произвольном месте репо. Скилл = единственная входная точка для workflow; хранится в стандартном пути.

## Failure mode

Инструкции workflow лежат в произвольном файле (корень репо, docs/, inbox/) — claude.ai/code browser не обнаруживает скилл автоматически. Триггерная фраза не привязана к стандартному контракту → workflow не работает в новых каналах без ручного обновления.

## IPO

| | |
|---|---|
| **Вход** | Фраза-триггер на человеческом языке («начни peer-сессию», «запусти workflow X») |
| **Процесс** | Агент находит `.claude/skills/<name>/SKILL.md` → следует шагам контракта |
| **Выход** | Workflow выполнен по стандартному контракту; SKILL.md = source-of-truth |

## Правило оформления

1. **Один стандартный путь:** `.claude/skills/<name>/SKILL.md` (не корень репо, не docs/).
2. **Фраза-триггер в AGENTS.md или CLAUDE.md:** `«{фраза} → прочитай SKILL.md и следуй шагам»`.
3. **Запрет альтернатив:** старый ad-hoc файл → удалить или пометить DEPRECATED с redirect.
4. **Независимость от канала:** фраза работает в claude.ai/code, headless, боте — без модификации SKILL.md.

## Тест

«Если пользователь напишет триггерную фразу в новом контексте без истории сессии — агент найдёт и выполнит SKILL.md?» Да → правило соблюдено.

## Источник

WP-337-З Ф7 (22 мая 2026). Рефакторинг kimi-writer-instructions.md → .claude/skills/kimi-peer-writer/SKILL.md, commit 7840feaf.
