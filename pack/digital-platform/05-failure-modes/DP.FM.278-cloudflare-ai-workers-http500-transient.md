---
id: DP.FM.278
name: "Cloudflare AI Workers getEmbedding: HTTP 500 при таймауте — не логическая ошибка"
type: failure-mode
domain: DP
status: active
valid_from: 2026-07-13
source: "session-close 2026-07-09; WP-7 Block KS5 (commit 2e5232c, issue #231)"
related:
  see_also:
    - "DP.FM.275: Neon serverless cold-start (другой слой: DB, не AI Workers)"
    - "DP.FM.123: reverse proxy truncates long-running handler (смежно: timeout, но другой уровень)"
tags: [cloudflare, ai-workers, embedding, http-500, transient-error, retry, serverless-ai]
---

# DP.FM.278 — Cloudflare AI Workers: HTTP 500 при таймауте getEmbedding() — транзиент

## Паттерн

Cloudflare Workers AI при вызове `getEmbedding()` (и других AI-эндпоинтов) возвращает **HTTP 500** при таймауте или перегрузке — не `408 Timeout` и не network exception.

Симптом маскируется под логическую ошибку («something failed»), а не под транзиентную инфраструктурную проблему.

## Диагностика

**Тест:** «Ручной повторный запрос через 10-30 секунд — успешен?» Да → транзиент Cloudflare AI, не логическая ошибка.

**Инструмент диагностики:** `wrangler tail` в live-режиме → увидеть сырой error body от AI Workers.

## Инцидент

WP-7 Block KS5 (issue #231, commit 2e5232c): `knowledge_search` периодически возвращал 500 — обнаружен в продакшне через health-check. Диагностика через `wrangler tail` показала: ошибка на слое AI Workers при embedding, не в логике поиска.

## Fix

Явный retry с backoff вокруг `getEmbedding()`:

```typescript
async function getEmbeddingWithRetry(text: string, ai: Ai): Promise<number[]> {
  for (let attempt = 1; attempt <= 3; attempt++) {
    try {
      const result = await ai.run('@cf/baai/bge-base-en-v1.5', { text: [text] });
      return result.data[0];
    } catch (err) {
      if (attempt === 3) throw err;
      await new Promise(resolve => setTimeout(resolve, attempt * 1000));
    }
  }
  throw new Error('embedding failed after 3 attempts');
}
```

3 попытки с паузой 1-2с между ними.

## Правило

**HTTP 500 от Cloudflare AI Workers endpoint = транзиент, требует retry.**

Bail-out без retry при HTTP 500 от AI Workers = неверная обработка.

## Отличия от смежных FM

| FM | Уровень ошибки | Симптом |
|----|----------------|---------|
| DP.FM.278 (этот) | Cloudflare AI Workers: getEmbedding timeout | HTTP 500 от AI backend |
| DP.FM.275 | Neon serverless DB cold-start | «relation does not exist» |
| DP.FM.123 | Reverse proxy timeout | Truncated response |

## Применимость

Любой Cloudflare AI Workers вызов (`getEmbedding`, `run` с AI-моделью, `vectorize`).
Аналог: аналогичные serverless AI endpoints (AWS Bedrock при холодном старте, Vertex AI serverless).
