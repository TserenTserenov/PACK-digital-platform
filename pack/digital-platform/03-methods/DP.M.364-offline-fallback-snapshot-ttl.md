---
id: DP.M.364
name: "Offline-fallback с явным TTL снапшота"
name_ru: "Offline-fallback с явным TTL снапшота (missing/malformed/stale → None → local seeds)"
name_en: "Snapshot load with explicit TTL and graceful offline fallback to local seeds"
summary: "При загрузке кеш-снапшота проверить три условия последовательно: отсутствие файла / повреждение JSON / устаревание >TTL. При любом — вернуть None и переключиться на локальные seeds. Инвариант: pipeline никогда не блокируется из-за недоступности снапшота."
type: method
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: 2
category: resilience-pattern
valid_from: 2026-06-21
related:
  see_also: [DP.M.311, DP.M.190]
tags: [offline-fallback, snapshot, TTL, local-seeds, resilience, cache, graceful-degradation]
source: "WP-149 render-pilot-guides.py, peer-session 2026-06-21, commit 1104046"
schema_version: 1
---

# DP.M.364 — Offline-fallback с явным TTL снапшота

## Описание

При загрузке кеш-снапшота (файл JSON с платформенным знанием) последовательно проверить три условия. При любом из них — вернуть None и перейти к fallback на локальные seeds. Pipeline никогда не блокируется из-за недоступности снапшота.

## Algorithm

### Step 1: Загрузить снапшот с тройной защитой

```python
def _load_knowledge_snapshot(path: str, ttl_hours: int = 48) -> dict | None:
    # Condition 1: file missing
    if not os.path.exists(path):
        return None
    # Condition 2: malformed JSON
    try:
        data = json.loads(Path(path).read_text())
    except (json.JSONDecodeError, OSError):
        return None
    # Condition 3: stale (age > TTL)
    mtime = os.path.getmtime(path)
    if (time.time() - mtime) > ttl_hours * 3600:
        return None
    return data
```

### Step 2: Ветвление по результату

```python
snapshot = _load_knowledge_snapshot(SNAPSHOT_PATH)
if snapshot is None:
    context = get_local_seeds()   # offline fallback
else:
    context = snapshot
```

### Step 3: Вынести TTL в константу / env

TTL 48h = текущая конвенция для платформенного знания. Вынести в конфигурацию при изменении требований к свежести.

## When to use

- Любой pipeline, где есть кеш с сетевым источником и fallback на локальные данные
- Когда сбой обновления снапшота не должен блокировать основной поток
- Рендер руководств, дашборды, оффлайн-агенты с локальными seeds

## Тест применимости

«При отсутствии / повреждении / устаревании снапшота — pipeline блокируется или переключается на local seeds?»
- Блокируется → применить паттерн
- Переключается → уже реализовано

## Связи

- DP.M.311 — file-fallback graceful Pack degradation; данный метод — специализация с явным TTL-staleness-check
- DP.M.190 — 3-level fallback для live-demo (другой контекст, схожая идея нескольких уровней деградации)
