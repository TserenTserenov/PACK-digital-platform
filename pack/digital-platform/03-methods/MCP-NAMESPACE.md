# MCP Namespace — соглашение IWE

> **Источник:** WP-189 Ф2 (1 апр 2026)
> **Статус:** active
> **Связи:** WP-175 (tailor-mcp контракт), WP-187 (knowledge-mcp изоляция), WP-184 (knowledge_feedback), WP-73 ADR-018

## Namespace соглашение

| Префикс | Категория | Кто управляет | Примеры |
|---------|-----------|--------------|---------|
| без префикса | Платформенные (наши) | Платформа (обновляем через update.sh) | `knowledge-mcp`, `digital-twin-mcp` |
| `ext-*` | Вендорские | Вендоры (OAuth-токены пользователя) | `ext-google-calendar`, `ext-linear`, `ext-slack` |
| `<username>-*` | Пользовательские | Пользователь (его данные, его ответственность) | `tseren-notes`, `tseren-obsidian` |

**Правило:** платформенные — без префикса (их имена зарезервированы). Вендорские — `ext-*`. Пользовательские — свой префикс (username или любой уникальный).

## Симметричная схема трёх платформенных MCP

```
Пользователь работает в: claude.ai / Cursor / Telegram
         ↓ (один Gateway URL, OAuth через Ory)
┌──────────────────────────────────────────────────┐
│  MCP Gateway (наш, Ory auth)                     │
│                                                  │
│  knowledge-mcp        ← общее знание платформы  │
│  (L2 shared + L4 per-user) ZP/FPF/SPF/Pack + личные репо │
│                                                  │
│  digital-twin-mcp     ← ЦД пользователя         │
│  (per-user RLS)         прогресс, профиль        │
│                                                  │
│  personal-knowledge-mcp  ← его репо             │
│  (per-user RLS)           эмбеддинги на нашей   │
│                           инфре                 │
└──────────────────────────────────────────────────┘
```

Все три — **наши сервисы**. Изоляция через RLS по `user_id`, не отдельные инстансы.

## Ownership данных

| MCP | Читает | Пишет | Владелец расчёта |
|-----|--------|-------|-----------------|
| `knowledge-mcp` | ZP, FPF, SPF, Pack платформы | — | Платформа |
| `digital-twin-mcp` | `3_derived` из Neon | `1_declarative`, `2_collected` | **R28 Профилировщик** = writer `3_derived` |
| `personal-knowledge-mcp` | Репо пользователя (выбранные) + Qdrant | GitHub коммит (async) | Пользователь |

> **⚠️ digital-twin-mcp = read-only потребитель `3_derived`.** R28 Профилировщик (AISYS.018) = единственный writer. On-demand recalc → вызов через R28, не внутри digital-twin-mcp.

## Выбор репо для индексации (personal-knowledge-mcp)

Пользователь явно указывает какие репо индексировать — платформа не индексирует всё автоматически.

**Где указывается:** настройки Gateway (UI) или `.exocortex.env`:
```
PERSONAL_KNOWLEDGE_REPOS=TserenTserenov/PACK-my-domain,TserenTserenov/DS-my-notes
```

**Как работает:**
1. Пользователь добавляет репо в список
2. Платформа индексирует → эмбеддинги в Neon/Qdrant с его `user_id`
3. При push в репо → webhook → переиндексация изменённых файлов
4. `personal-knowledge-mcp` возвращает только его данные (RLS)

## Контракты MCP (спецификации для downstream РП)

### knowledge-mcp → WP-187

```
Tool: search(query: str, source?: str, source_type?: str, limit: int = 5) → SearchResult[]
  Изоляция:
    - Авторизованный пользователь автоматически видит платформенные + свои личные документы
    - user_id передаётся прозрачно через HTTP-заголовок X-User-Id (Gateway из Ory JWT)
    - НЕ через аргументы tool (защита от подмены user_id)
    - platform namespace (user_id IS NULL): ZP, FPF, SPF, Pack платформы — доступно всем
    - user namespace (user_id = UUID): Pack пользователя — только владельцу
    - прямой доступ минуя Gateway: только platform namespace

Tool: knowledge_feedback(document_id: str, query: str, helpfulness: bool, user_id: str) → void
  Сохраняет в retrieval_feedback(user_id, document_id, query_hash, helpfulness, created_at)
  Реализация: WP-184 Ф3
```

### digital-twin-mcp → WP-175

```
Tool: get_tailor_context(user_id: str) → TailorContext
  Возвращает: diagnostic_profile, learning_history, current_areas, suggested_depth
  RLS: изолировано по user_id
  Latency: < 500ms (кэш, обновляется R28)
```

### personal-knowledge-mcp → WP-187

```
Tool: search(query: str, user_id: str, limit: int = 5) → SearchResult[]
  Ищет только в репо пользователя (его user_id)

Tool: propose_capture(content: str, suggested_location: str, user_id: str) → CaptureProposal
  Поведение: зависит от capture_autonomy в params.yaml
    propose (default): возвращает предложение, ждёт подтверждения
    auto: сразу пишет в GitHub

Tool: write(path: str, content: str, user_id: str) → WriteResult
  Auth: GitHub App Installation Token (repo-scoped)
  Async: write → queue → background commit (~5 сек)
  Side effect: ingest_event() в Activity Hub (ADR-009)
```

## Temporal метаданные (единый формат с WP-180 Ф10.1)

Все эмбеддинги в Qdrant:
```json
{
  "document_id": "PACK-my-domain/concept.md",
  "content": "...",
  "valid_from": "2026-01-15",
  "superseded_by": null,
  "user_id": "TserenTserenov"
}
```

Единый формат с MEMORY.md frontmatter (WP-180 Ф10.1):
```yaml
---
valid_from: 2026-01-15
superseded_by: null
---
```

При поиске: `superseded_by != null` → пониженный score.

## ADR-018 (WP-73 §3.8) — обновить

Добавить namespace соглашение: платформенные (без префикса) / вендорские (`ext-*`) / пользовательские (`<username>-*`).
