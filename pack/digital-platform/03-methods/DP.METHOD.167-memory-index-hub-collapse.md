---
id: DP.METHOD.167
name: "Схлопывание однотемных записей MEMORY.md в hub-файл (hub-collapse)"
type: method
pack: PACK-digital-platform
domain: digital-platform / agent-workspace
trust: confirmed
epistemic_stage: observed
valid_from: 2026-07-10
source: "session-transcript 2026-07-10, MEMORY consolidation (commit 91928b989 DS-my-strategy)"
schema_version: 1
---

# DP.METHOD.167 — Схлопывание однотемных записей MEMORY.md в hub-файл (hub-collapse)

## Описание

Когда горячий индексный файл агента (MEMORY.md, лимит 200 строк) накапливает 3–4 однотемных записи, их можно схлопнуть в один hub-файл с единственной строкой в индексе. Индекс сжимается без потери данных.

## Алгоритм

1. **Триггер:** hook сигнализирует о приближении к лимиту строк, или при ревью обнаруживаются кластеры из ≥3 записей на одну тему.
2. **Группировка:** найти записи с общим паттерном (диагностика, git-гигиена, session-механика, OS-quirks).
3. **Создать hub-файл:** `memory/reference_<тема>_hub.md` — содержит все N записей кластера.
4. **Заменить строки в индексе:** N строк → одна строка-указатель `- [Хаб: <тема>](reference_<тема>_hub.md)`.
5. **Ничего не выбрасывать:** содержимое полностью переезжает в hub, индекс хранит только указатель.

## Критерий группировки

Записи объединяются в hub, если: общий паттерн читается с одного раза, hub читается как самостоятельный справочник без контекста индекса, индексная строка достаточна для навигации к нужной теме.

## Применение

Применимо к любому горячему индексу с лимитом строк. Третье применение этого паттерна в IWE: macOS/zsh quirks hub, LLM-bot output quirks hub, три новых hub 10 июля 2026 (diagnosis technique, agent/session mechanics, day protocol gaps).

## Источник

session-transcript 2026-07-10 MEMORY consolidation; git diff DS-my-strategy (commit 91928b989): 22 однотемных записи схлопнуты в 3 новых hub + 2 existing. No content dropped.
