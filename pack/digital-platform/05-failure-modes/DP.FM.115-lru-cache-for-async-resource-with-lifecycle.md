---
id: DP.FM.115
name: "lru_cache для async resource с lifecycle: leak + cross-loop errors"
type: failure-mode
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: formalized
category: framework
severity: major
valid_from: 2026-05-30
related:
  see_also: [DP.FM.114]
tags: [fastapi, dependency-injection, async, lifecycle, lru-cache, connection-leak, httpx, asyncpg]
source: "session 2026-05-30 WP-201 Ф3.4 mcp_tools (peer-session 30, 01-peer.md:25-29)"
schema_version: 1
---

# DP.FM.115 — lru_cache для async resource с lifecycle: leak + cross-loop errors

## Описание

FastAPI pattern `@lru_cache + Depends(get_resource)` популярен для shared-singleton dependencies. **Antipattern для async resources с явным lifecycle** (httpx.AsyncClient, asyncpg.Pool, aiokafka.Producer, redis.asyncio.Redis): кэш переживает shutdown, `.aclose()` не вызывается, первый init случается в случайном event-loop.

Симптомов три, все — слабо-сигнальные:
1. Connection leak: lru_cache не вызывает `aclose()` при shutdown → dangling sockets, leak в metrics.
2. Cross-loop errors: первый вызов в random request handler context → connection pool инициализируется не в lifespan event-loop → `Future attached to a different loop`.
3. State-bleeding в тестах: `lru_cache.cache_clear()` нужно вызывать в каждом setup — easy to forget.

## Симптом

- В мониторинге: рост open file descriptors, неубывающее число соединений к downstream-сервису.
- В логах: `Future attached to a different loop`, `Event loop is closed` при graceful shutdown.
- В тестах: тест A модифицирует resource, тест B читает изменённое состояние — flaky tests без явного shared state в коде.

## Механизм

1. Разработчик пишет `@lru_cache; def get_client(): return httpx.AsyncClient()`.
2. FastAPI `Depends(get_client)` инжектит singleton в handlers.
3. Первый вызов происходит в request-handler — клиент привязывается к **этому** event-loop.
4. Shutdown event у FastAPI не имеет hook для `.aclose()` — lru_cache не expose lifecycle.
5. При перезапуске worker'а / test-teardown сокеты остаются open, connection pool остаётся живым в кэше.

## Canonical fix

Создавать resource в `lifespan` event-handler, хранить в `app.state.<name>`, закрывать в `finally` после `yield`:

```python
@asynccontextmanager
async def lifespan(app):
    async with httpx.AsyncClient() as client:
        app.state.gh_client = client
        yield
    # client.aclose() автоматически вызван __aexit__

# Доступ
async def handler(request: Request):
    client = request.app.state.gh_client
```

Sync-only ресурсы без lifecycle (config, logger) — `lru_cache` ОК.

## Тест применимости

«У ресурса есть `aclose()` / `__aexit__` или connection pool?» — да → lifespan, не lru_cache.

## Применимо к

httpx.AsyncClient, asyncpg.Pool, aiokafka.Producer/Consumer, redis.asyncio.Redis, aioboto3 clients — любые async resources с явным `.aclose()` или `__aenter__/__aexit__`.