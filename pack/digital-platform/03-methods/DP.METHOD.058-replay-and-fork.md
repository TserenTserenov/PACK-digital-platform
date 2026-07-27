---
id: DP.METHOD.058
name: Повтор и форк сессии агента
type: method
status: active
valid_from: 2026-06-18
summary: "Восстановить контекст агентской сессии до выбранной точки решения, чтобы исследовать альтернативный путь (форк) или воспроизвести рассуждение (повтор)."
related:
  service_clauses:
    - DP.SC.038   # replay service clause
  roles:
    - DP.ROLE.050 # pattern miner — reader of realtime corpus
  artifacts:
    - WP-295      # source WP
  see_also:
    - DP.METHOD.057   # idempotent SQL migrations
    - DP.D.084        # workspace vs conversational peer coordination
created: 2026-06-18
updated: 2026-07-27
tags: [agent-trace, replay, fork, decision-corpus, peer-session]
source: "peer-session 2026-06-18-07-wp295-f2-replay (consensus 05-peer.md)"
schema_version: 1
---

# DP.METHOD.058 — Повтор и форк сессии агента

> **Применяется:** когда нужно воспроизвести решение агента, начиная с конкретной точки,
> или исследовать альтернативу (форк) от этой точки.

## §0 Назначение

Агент записывает ход своей работы в `agent_trace.decision` (хук `agent-trace-recorder.sh`).
Этот метод позволяет:
1. **Повторить** — вернуться к сессии и проверить, почему агент принял решение X.
2. **Форкнуть** — создать новую сессию с тем же контекстом, но предложить агенту альтернативу в точке Y.

## §1 Корпусное разделение (corpus split)

Журнал `agent_trace.decision` содержит два класса записей.
Смешивать их при обучении паттерн-майнера (Ф4, DP.ROLE.050) нельзя:

| Поле `source` | Поле `attributed_to` | Источник | Семантика |
|---|---|---|---|
| `realtime` | `agent` | хук в момент сессии | автономное решение агента |
| `peer-session-import` | `consensus` | `import_peer_session.py` | коллаборативное решение (диалог) |

**Правило:** паттерн-майнер по умолчанию фильтрует `WHERE source='realtime'`.
Peer-session corpus используется отдельно — для анализа collaborative-паттернов.

**Почему это важно:** «агент избегает X в автономном режиме» и «агент соглашается с X под оппонированием» — семантически разные паттерны. Смешение даёт ложный сигнал.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Чистота обучающего корпуса паттерн-майнера (§1: фильтр `WHERE source='realtime'`) ↔ доступность peer-session знаний | Метод по умолчанию исключает `peer-session-import` из корпуса для DP.ROLE.050, защищая от смешения автономных и коллаборативных решений (§1: «семантически разные паттерны») — но ценой того, что богатый корпус исторических peer-сессий (§4) остаётся неиспользованным без отдельного явного анализа |
| Идемпотентность replay (§3: байт-совпадающий контекст, «нет воспроизведения внешнего мира/файловой системы») ↔ полнота восстановления состояния | Строгое ограничение replay только контекстом принятых решений даёт детерминизм и надёжность, но заставляет практикующего вручную восстанавливать состояние файлов (git checkout) и окружение отдельно — replay-контекст сам по себе не самодостаточен |
| Ленивый парсер исторических сессий (§4: отсутствие `consensus:` не блокирует импорт) ↔ точность атрибуции | Дефолтная атрибуция `attributed_to='consensus'` расширяет покрытие корпуса на старые сессии без frontmatter, но за счёт точности разметки — дефолт может не совпадать с реальным характером конкретной записи |

## §2 Алгоритм повтора

```
1. Найти точку: iwe-trace.py tree <session_id>
   → выводит дерево решений с номерами sequence.

2. Построить контекст: iwe_replay.restore_context(session_id, target_seq, conn)
   → SnapshotRecord (ближайший снапшот ≤ target_seq) +
     [DecisionRecord, ...] от снапшота до target_seq.

3. Отформатировать: iwe_replay.format_injectable(ctx)
   → Markdown-строка «Replay Context» для инъекции в новую сессию.

4. Предпросмотр: iwe-trace.py replay <event_id> --dry-run
   → печатает context_str с оценкой токенов (~chars/4).

5. Форк: iwe-trace.py replay <event_id> --fork
   → создаёт запись в agent_trace.fork_session, запускает новую сессию.
   (C-full компонент, после C-skeleton Ф2)
```

## §3 Инварианты (из DP.SC.038)

- **Идемпотентность:** один и тот же `event_id` всегда даёт байт-совпадающий контекст.
- **Нет воспроизведения внешнего мира:** метод не восстанавливает состояние файловой системы, API-ответы или переменные окружения. Только контекст принятых решений.
- **Нет воспроизведения файловой системы:** если сессия редактировала файлы — их состояние нужно восстанавливать отдельно (git checkout к нужному коммиту).

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| Механика вместо точки восстановления | При выполнении шагов §2 внимание смещается с вопроса «это действительно нужная точка в дереве решений» на вопрос «context успешно сформирован» — шаг 1 (найти точку через `iwe-trace.py tree`) выполняется формально, а шаг 3 (`format_injectable`) незаметно становится фактической целью работы |
| Перенос доверия с dry-run на fork | При обращении к CLI (§6) внимание тянется к уже работающим командам (`tree`, `replay --dry-run`) и недооценивает статус `--fork` как незавершённого компонента (§7: «следующая фаза») — гладкая работа dry-run создаёт риск спутать демонстрационный предпросмотр с боеспособным форком |

## §4 Работа с историческими peer-сессиями

Исторические Markdown peer-сессии (`sessions/<YYYY-MM>/<SESSION-ID>/`) импортируются в `agent_trace` через `import_peer_session.py`.

**Ленивый парсер** — для сессий без `consensus:` frontmatter в ходах:
- Отсутствие `consensus:` не блокирует импорт.
- Записи получают `source='peer-session-import', attributed_to='consensus'` по умолчанию.
- Детали документируются в DP.METHOD (этот файл, §4) — в `import_peer_session.py` комментарий отсылает сюда.

## §5 Схема БД — добавленные поля (migration 266)

Миграция `266-wp295-f2-decision-source.sql` добавляет к `agent_trace.decision`:

```sql
source       TEXT NOT NULL DEFAULT 'realtime'
             CHECK (source IN ('realtime', 'peer-session-import'))
attributed_to TEXT NOT NULL DEFAULT 'agent'
             CHECK (attributed_to IN ('consensus', 'agent'))
```

Индекс: `idx_decision_source ON (source, attributed_to)` — покрывает фильтр паттерн-майнера.

## §6 CLI (iwe-trace.py)

```bash
# Дерево решений сессии
iwe-trace.py tree <session_id>
iwe-trace.py tree last

# Предпросмотр replay-контекста
iwe-trace.py replay <event_id> --dry-run

# Форк (C-full, ожидается)
iwe-trace.py replay <event_id> --fork

# Импорт peer-сессий в corpus
import_peer_session.py ~/IWE/DS-my-strategy/sessions/2026-06/2026-06-18-07-wp295-f2-replay/
import_peer_session.py --all ~/IWE/DS-my-strategy/sessions/
import_peer_session.py --dry-run <session-dir>
```

## §7 Статус реализации (WP-295 Ф2 build order)

| Компонент | Файл | Статус |
|---|---|---|
| C-skeleton | `scripts/iwe-trace.py` (tree + replay --dry-run) | ✅ done |
| Migration | `neon-migrations/mvp/266-wp295-f2-decision-source.sql` | ✅ done |
| Import | `scripts/import_peer_session.py` | ✅ done |
| A: restore_context | `scripts/iwe_replay.py` | ✅ done |
| B: formatter | `scripts/iwe_replay.py` (format_injectable) | ✅ done |
| D: метод | `PACK-digital-platform/.../DP.METHOD.058-replay-and-fork.md` | ✅ done |
| C-full | `iwe-trace.py replay --fork` + `fork_session` DB write | ⏳ следующая фаза |

---

> 2026-07-27 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 3). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
