---
id: DP.M.331
name: "Agent Audit Trail as Append-only Sidecar"
name_ru: "Журнал действий агента — дозаписываемый sidecar-файл рядом с тредом сессии"
name_en: "Agent action audit trail as append-only sidecar alongside the session thread"
summary: "Машиночитаемый журнал действий агента хранится как отдельный append-only файл (audit-<id>.jsonl) рядом с человекочитаемым тредом сессии. Записывает события, которых нет в треде: вызовы инструментов, чтение/запись файлов, коммиты. Включается в коммит хода → переживает git reset --hard."
type: method
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: 2
category: audit-trail
valid_from: 2026-06-21
related:
  see_also: [DP.ROLE.047, DP.M.059]
tags: [audit-trail, sidecar, append-only, jsonl, agent, session, durability, git-reset]
source: "WP-428 Ф6, session-close 2026-06-21, commit a7ea6eb4"
schema_version: 1
---

# DP.M.331 — Журнал действий агента — дозаписываемый sidecar-файл

## Описание

Машиночитаемый audit-trail агента — это отдельный append-only файл (`audit-<id>.jsonl`), расположенный рядом с файлами сессии, а не встроенный в человекочитаемый тред (отчёт/transcript). Журнал записывает события, которые не попадают в тред: вызовы инструментов, чтение и запись файлов, git-коммиты.

## Algorithm

### Step 1: Создать sidecar-файл при открытии сессии

Создать `sessions/<date>/<session-id>/audit-<session-id>.jsonl` рядом с `report.md` и `transcript.md`. Файл создаётся пустым, дозаписывается в ходе работы.

### Step 2: Записывать только действия, отсутствующие в треде

```jsonl
{"ts": "2026-06-21T10:05:00Z", "event": "tool_call", "tool": "Read", "path": "inbox/WP-428/WP-428.md"}
{"ts": "2026-06-21T10:05:03Z", "event": "tool_call", "tool": "Edit", "path": "inbox/WP-428/WP-428.md"}
{"ts": "2026-06-21T10:07:00Z", "event": "commit", "sha": "a7ea6eb4", "message": "fix(wp-428): ..."}
```

Тред (report.md) содержит нарратив и решения. Sidecar содержит машиночитаемую хронологию действий.

### Step 3: Включить sidecar в коммит хода

```bash
git add sessions/<date>/<session-id>/audit-<session-id>.jsonl
git commit -m "step(wp-NNN): <описание>"
```

Включение в коммит делает файл долговечным: он переживает `git reset --hard origin/main`.

### Step 4: Применять PII-маску только к свободным полям

Поля с идентификаторами (`WP-\d+`, пути к файлам, SHA коммитов) сохраняются как есть — они не PII. Маскировать только свободные текстовые поля, если они могут содержать персональные данные.

## When to use

- При проектировании audit-trail для агентских сессий, требующих воспроизводимости и постмортема
- Когда нужно восстановить хронологию действий агента после `git reset --hard` или инцидента
- Когда тред сессии читается пилотом, но машинная обработка (паттерн-майнинг, replay) требует структурированных данных

## Тест применимости

«Может ли хронология действий агента потребоваться для воспроизведения после hard reset?»
- Да → sidecar обязателен (включить в коммит)
- Нет → можно оставить in-memory или не вести

## Отличие от DP.ROLE.047 (Trace Recorder)

DP.ROLE.047 записывает **рассуждения** LLM (гипотезы, выбор, обоснование) — что агент думает. DP.M.331 записывает **действия** агента (инструменты, файлы, коммиты) — что агент делает. Первое — semantic trace, второе — operational audit trail.

## Связи

- DP.ROLE.047 (Trace Recorder) — complementary: reasoning trace + action trail = полная картина сессии
- DP.M.059 (Phase Closure Artifact Triad) — sidecar является side-artifact фазы, если сессия закрывается как фаза РП
- DP.D.149 (Git ≠ Neon ≠ Session) — журнал в Git-слое, не в Session (эфемерный) и не в Neon
