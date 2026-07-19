---
id: DP.D.083
name: "Persistent TaskTracker (filesystem) ≠ Ephemeral TodoWrite (session memory)"
type: distinction
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-05-22
created: 2026-07-19
source: "миграция из 01B-distinctions.md (РП170 Ф2, 2026-07-19)"
see_also: []
schema_version: 1
---

# DP.D.083: Persistent TaskTracker (filesystem) ≠ Ephemeral TodoWrite (session memory)

| Аспект | TodoWrite | Filesystem TaskTracker |
|--------|-----------|----------------------|
| **Хранение** | Память процесса (in-process) | Файловая система (`inbox/agent/tasks/TASK-<id>.md`) |
| **Scope** | Текущая сессия | Персистентен между сессиями |
| **При рестарте** | Данные теряются | Задачи выживают |
| **Аудит** | Невозможен после завершения | Файлы доступны для проверки |
| **Применение** | Внутрисессионные чеклисты | Scheduled/periodic агенты, dispatcher |

**Тест выбора:** «Нужно ли возобновить задачу после рестарта агента?»
- Да → filesystem TaskTracker
- Нет → TodoWrite достаточен

**Почему важно:** Headless-агенты, работающие периодически (cron/dispatcher), не могут полагаться на TodoWrite — при каждом запуске `claude -p` начинается новый процесс. Для сохранения прогресса задачи между запусками обязателен файловый трекер.

**Контекст:** Выявлено при проектировании DP.IWE.011 (headless adapter) и Agent Inbox (WP-324). Расширяет DP.D.048 (Script ≠ Agent) — добавляет ось persistence.
