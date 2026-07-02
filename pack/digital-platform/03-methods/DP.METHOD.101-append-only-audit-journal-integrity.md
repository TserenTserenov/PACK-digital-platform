---
id: DP.METHOD.101
type: method
domain: digital-platform
pack: PACK-digital-platform
status: draft
summary: "Hash-chain trigger + SELECT FOR UPDATE на stream_tip + JCS + OTS anchors = дизайн append-only аудит-журнала с tamper-evidence и serialized writes без гонки хешей. Применимо к любому журналу с требованием non-repudiation."
created: 2026-07-01
trust:
  F: 3
  G: domain
  R: 0.87
epistemic_stage: evidence
related:
  see_also: [DP.M.331]
tags: [audit, append-only, hash-chain, postgresql, tamper-evidence, ots, select-for-update, jcs]
wp: WP-455
---

# Append-only аудит-журнал с tamper-evidence (DP.METHOD.101)

## Описание

Паттерн проектирования append-only журнала событий с криптографической защитой от подделки. Применимо к любому журналу с требованием tamper-evidence: аудит действий агента, финансовый лог, журнал безопасности.

## Компоненты

| Компонент | Роль |
|-----------|------|
| **Hash-chain trigger** | PostgreSQL trigger вычисляет `hash(prev_hash \|\| row_data)` при каждой вставке. Подделка строки → нарушение цепочки, обнаруживается при верификации |
| **SELECT FOR UPDATE на stream_tip** | Сериализует конкурентные вставки: каждая транзакция захватывает lock на последней записи перед вставкой новой. Без lock → гонка хешей → сломанная цепочка |
| **JCS (JSON Canonicalization Scheme)** | Детерминированная сериализация JSON перед хешированием. Без канонизации порядок ключей влияет на хеш → верификация нестабильна |
| **OTS anchors (OpenTimestamps)** | Внешняя временная привязка: хеш блока цепочки → Bitcoin blockchain. Non-repudiation: нельзя задним числом переписать время |

## IPO

**Вход:** событие (dict / JSON-объект)

**Действие:**
1. Canonicalize event через JCS
2. `SELECT FOR UPDATE stream_tip` (lock последней записи)
3. Вычислить `new_hash = hash(prev_hash || canonical_event)`
4. INSERT (event, new_hash, timestamp)
5. Периодически: anchor хеш блока → OTS

**Выход:** tamper-evident запись с cryptographic linkage к предыдущей

## Инварианты

**SELECT FOR UPDATE обязателен при конкурентных вставках** — без него hash conflict при параллельных транзакциях.

**JCS обязателен для стабильного хеша** — без него один и тот же объект даёт разные хеши в разных средах.

## Связи

- DP.M.331 (agent-audit-trail-append-only-sidecar) — смежный паттерн для file-based агентского журнала (нет DB, нет hash-chain)
- WP-455 Ф1 — первый прецедент реализации
