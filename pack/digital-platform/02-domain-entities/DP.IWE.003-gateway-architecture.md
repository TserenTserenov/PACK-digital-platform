---
id: DP.IWE.003
name: Gateway-архитектура IWE
type: domain-entity
status: active
created: 2026-03-31
updated: 2026-04-01
trust:
  F: 3
  G: domain
  R: 0.7
epistemic_stage: evidence
related:
  uses: [DP.ARCH.001, DP.EXOCORTEX.001, DP.IWE.001, DP.IWE.002]
  enables: [DP.D.036]
---

# Gateway-архитектура IWE

## 1. Определение

Gateway-архитектура — паттерн развёртывания IWE, при котором пользователь подключается к платформе через единый MCP Gateway URL из любого AI-клиента, без обязательного использования VS Code или Claude Code CLI.

IWE при этом остаётся слоем: **git-репо пользователя + MCP Gateway + протоколы**. Клиент — сменная деталь.

## 2. Два режима подключения

| Режим | Аудитория | Точка входа | Что нужно пользователю |
|---|---|---|---|
| **Gateway** | Массовая (нетехническая) | Один URL + OAuth | Зарегистрироваться, подключить connector |
| **Direct MCP** | Технари | VS Code + Claude Code CLI | Настроить `.mcp.json` вручную |

Оба режима существуют параллельно. Gateway не вытесняет CLI — он расширяет аудиторию.

## 3. Три MCP в Gateway

| MCP | Слой | Данные | Изоляция |
|---|---|---|---|
| **knowledge-mcp** (платформенный) | L2 | ZP, FPF, SPF, Pack платформы, курсы + CLAUDE.md пользователя | Публичный + user-space через user_id |
| **digital-twin-mcp** (авторизованный) | L2 | ЦД пользователя (SurrealDB) | RLS по user_id, Ory OAuth |
| **personal-knowledge-mcp** (личный) | L4 | GitHub-репо пользователя + эмбеддинги на платформе | GitHub App token (repo-scoped) |

Весь Gateway — под Ory-авторизацией. Открытые (публичные) MCP подключаются напрямую, минуя Gateway.

## 4. Доставка CLAUDE.md (system prompt)

CLAUDE.md пользователя хранится в его git-репо. knowledge-mcp доставляет его как system prompt через MCP `prompts`/`resources` endpoint. Это обеспечивает работу протоколов ОРЗ и скиллов в любом клиенте — не только в Claude Code.

Механизм защиты: version pinning (git commit hash). Alert при изменении hash без явного деплоя.

## 5. Personal Knowledge MCP — архитектура данных

```
Git-репо пользователя (GitHub)
  └── Pack, CLAUDE.md, протоколы, заметки (Markdown)
         ↓ при write через MCP
personal-knowledge-mcp:
  1. GitHub API commit (данные остаются у пользователя)
  2. ingest_event() → Activity Hub (Event Bus, ADR-009)
  3. Vectorize → эмбеддинги на платформе (Neon pgvector → Qdrant)
         ↓
  MCP search(query, user_id) → RLS изолирует данные
```

**Принцип разделения:** данные (репо) у пользователя, эмбеддинги (производные) на платформе.

## 6. Онбординг

При регистрации платформа форкает шаблон IWE (FMT-exocortex-template) в GitHub-пространство пользователя через GitHub OAuth. Пользователь получает рабочую среду без ручной настройки.

## 7. Связь с принципами DP.ARCH.001

| Принцип | Как реализуется |
|---|---|
| #6 Отчуждаемость | Данные в GitHub пользователя — уходит с данными |
| #8 Знания публичны, данные приватны | knowledge-mcp (публичный) ≠ DT-mcp (приватный) |
| #11 Per-user blast radius | GitHub App token repo-scoped, RLS по user_id |
| #13 ИИ-системы UI-agnostic | Gateway работает в любом клиенте |
| #15 Multi-surface | Gateway = новая поверхность наряду с ботом, Web App, CLI |

## 6a. Изоляция данных (multi-tenant)

**Принятое решение (АрхГейт 2026-04-01):** namespace per user в pgvector (фильтр по `user_id`).

| Фаза | Решение | Порог |
|------|---------|-------|
| **Сейчас → ~30к пользователей** | Namespace per user: фильтр `WHERE user_id = $1` + HNSW индекс на всю таблицу | 30к пользователей × ~1к векторов = 30М строк |
| **При росте >30к** | Миграция на Qdrant: collection per user, отдельный HNSW индекс на коллекцию | ADR при достижении порога |

**Обоснование:** pgvector с фильтром по `user_id` эффективен при ~30М строк. При превышении порога деградирует recall (ANN-поиск не партиционирован). Миграция на Qdrant — отдельное архитектурное решение, не требует изменения протокола MCP.

**Безопасность:** `user_id` берётся из JWT-токена (Ory), не из параметра запроса. SQL-инъекция исключена на уровне ORM.

## 6b. Переиндексация embeddings

**Принятое решение:** webhook-триггер при push в Pack-репо пользователя.

```
Push в Pack-репо (GitHub)
  → GitHub webhook → personal-knowledge-mcp
  → Задача в очередь (async)
  → Векторизация изменённых файлов
  → Обновление embeddings в pgvector/Qdrant
```

**Гарантии:**
- Пользователь не управляет индексом вручную
- Задержка переиндексации: целевое ≤5 мин после push
- Частичная переиндексация: только изменённые файлы (diff от предыдущего commit hash)
- При сбое: retry с backoff, alert в логах платформы

## 6c. Gateway latency SLA

| Компонент | Целевое |
|-----------|---------|
| Gateway overhead (auth + routing) | ≤200ms |
| Контекстный кэш (Pack не менялся) | ≤50ms (из кэша) |
| Инвалидация кэша | При push в репо (тот же webhook, §6b) |
| Полный путь вендорского AI → данные → ответ | ≤500ms (без LLM inference) |

**Кэш:** TTL определяется commit hash репо. При совпадении hash — отдаём из кэша без чтения файлов.

## 8. Связанные документы

- [DP.ARCH.001](DP.ARCH.001-platform-architecture.md) — архитектура платформы (принципы ЭМОГССБ)
- [DP.EXOCORTEX.001](DP.EXOCORTEX.001-modular-exocortex.md) — модульный экзокортекс
- [DP.IWE.001](DP.IWE.001-intelligent-working-environment.md) — IWE как концепция
- [DP.IWE.002](DP.IWE.002-iwe-template-and-setup.md) — шаблон и setup
- [DP.D.036](../../../02-domain-entities/) — BYOB Knowledge Architecture
