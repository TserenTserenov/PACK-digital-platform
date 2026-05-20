---
id: DP.SOTA.026
name: "Unified pipeline + content-hash skip — альтернатива дубль-pipeline для одного state"
type: sota
status: draft
summary: "Анти-паттерн: два кода (delta + full-rebuild) для одного derived state → drift risk. Паттерн: единая функция reindexFor(files[]) idempotent + content_hash skip → полный rebuild почти-нулевой стоимости; webhook / heartbeat-cron / manual вызывают одну точку."
created: 2026-05-19
valid_from: 2026-05-19
trust:
  F: 3
  G: pattern
  R: 0.75
related:
  informs: [DP.M.098]
  uses: []
  see_also: [DP.SOTA.021]
sources:
  - DS-my-strategy WP-339-pack-graph-pipeline.md Ф3 unified pipeline + Ф5 heartbeat
  - commit 2da20e4 (content_hash skip pattern в ingest-concepts.ts, прецедент)
  - memory/feedback_silent_projection_fail.md (silent fail = invisible drift, мотивация)
---

# DP.SOTA.026 — Unified Pipeline + Content-Hash Skip

> Один pipeline для всех триггеров (webhook + cron + manual), idempotent и self-skipping по content_hash, делает «полный rebuild» дешёвым по умолчанию и устраняет drift между параллельными кодовыми путями.

## Антипаттерн (что делаем НЕ так)

Три отдельные кодовые ветки для одного derived state:

1. **Delta-pipeline** (фоновый поток, частые triggers — webhook).
2. **Full-rebuild** (cron, защита от silent fail).
3. **Manual MCP / CLI** (recovery, развилка для оператора).

Каждая ветка реализует свою версию преобразования. Через 2-3 итерации они расходятся (delta учитывает поле X, full-rebuild — нет). Любая попытка recovery через manual обнаруживает третий вариант drift.

## Паттерн (что делаем вместо)

**Единая функция:** `reindexFor(env, files[])`.

Контракт:

- **idempotent** — повторный вызов с теми же входами не меняет state;
- **batch** — принимает список (один файл = batch размера 1);
- **self-skipping через content_hash** — на входе сравнивает hash(file) с хранимым в state; совпало → skip без работы.

Три триггера одной функции:

| Триггер | Аргумент | Назначение |
|---------|---------|-----------|
| webhook | изменённые файлы из событийного payload | latency optimization |
| heartbeat-cron | все файлы | silent-fail safety net |
| drift detector | подмножество с признаками drift | observability |

L3 (manual MCP / CLI как самостоятельный код) — устраняется: heartbeat-cron покрывает recovery.

## Условия применимости

- Имеется derived state, вычисляемый из набора source-файлов (search index, knowledge graph, projection table, denormalized cache)
- Стоимость пересчёта одного файла приемлема (≤секунд), стоимость пересчёта всех — терпима если skip работает
- Content_hash доступен (хеш файла или его semantic key)

## Когда НЕ применять

- Источник не файлы, а поток событий с side-effects (event ≠ файл; idempotency требует event_id, не content_hash)
- Стоимость одного пересчёта высока (минуты CPU) — тогда нужен явный delta-tracking
- Derived state имеет ordering constraints (порядок событий важен; content_hash не различает порядок)

## Прецеденты

- `ingest-concepts.ts` (commit 2da20e4) — content_hash skip pattern, прецедент применения
- WP-339 Pack-граф (план Ф3) — рефакторинг к `reindexConceptsForFiles(env, files[])` под три триггера

## Различения

- **Webhook OR cron — false binary:** webhook = latency, heartbeat = silent-fail safety net, drift detector = observability. Это три триггера, не три альтернативы.
- **Content_hash ≠ event_id:** работает для file-based source, не для event-stream.
