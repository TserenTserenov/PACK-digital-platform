---
id: DP.M.362
name: "Prefetch snapshot empty guard"
name_ru: "Защита рабочего снапшота при пустом результате prefetch"
name_en: "Prefetch snapshot empty guard — never overwrite working snapshot with empty result"
summary: "Когда prefetch-запрос возвращает all-empty результаты (сбой сети/сервиса), не перезаписывать рабочий снапшот. Рабочий снапшот сохраняется и используется downstream-конвейером как fallback."
type: method
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: 2
category: resilience-pattern
valid_from: 2026-06-22
related:
  see_also: [DP.M.018, DP.M.311]
tags: [prefetch, snapshot, resilience, empty-guard, fallback, idempotent, cache]
source: "WP-149 B4 (prefetch-knowledge-snapshot.py), peer-12, commit 1104046, 2026-06-21"
schema_version: 1
---

# DP.M.362 — Защита рабочего снапшота при пустом результате prefetch

## Описание

При выполнении prefetch (периодической загрузки данных в снапшот) проверяй, не является ли весь результат пустым, перед перезаписью файла снапшота. Если все N запросов к платформе вернули пустые массивы — это сигнал сбоя сети или сервиса, а не «знания нет». В этом случае оставить предыдущий рабочий снапшот без изменений.

**Инвариант:** рабочий снапшот никогда не перезаписывается пустым результатом.

**Per-program изоляция:** падение одной программы (ЛР/РР/ИР) не блокирует другие — каждая обрабатывается независимо.

## Algorithm

### Step 1: Выполни prefetch-запросы с изоляцией ошибок

Запускай N программ независимо. Одна retry на transient 5xx/network.

```python
results = {}
for program in programs:
    try:
        results[program] = fetch_knowledge(program)  # one retry inside
    except Exception:
        results[program] = []  # isolated failure, not propagated
```

### Step 2: Проверь all-empty guard перед записью

```python
non_empty = {k: v for k, v in results.items() if v}
if not non_empty:
    logger.warning("All prefetch results empty — keeping prior snapshot")
    return  # do not overwrite working snapshot
```

### Step 3: Записывай только если есть данные

```python
snapshot = {
    "generated_at": datetime.utcnow().isoformat(),
    "programs": non_empty,
}
write_yaml(SNAPSHOT_PATH, snapshot)
```

Программы с пустым результатом пропустить (per-program isolation — не включать в снапшот с ключом `[]`).

## When to use

- При реализации фоновых prefetch-скриптов с записью в snapshot/cache файл
- Когда downstream-конвейер зависит от снапшота как source-of-truth

## Тест применимости

«Есть ли хотя бы одна программа с непустым результатом?»
- Нет → keep prior snapshot (all-empty guard сработал)
- Да → write snapshot с непустыми программами

## Связи

- DP.M.018 (External data fallback hierarchy) — данный метод реализует принцип «не затирать рабочий кеш сбойным результатом» на уровне prefetch
- DP.M.311 (File-fallback graceful pack degradation) — смежный паттерн для деградации Pack при отсутствии файла
