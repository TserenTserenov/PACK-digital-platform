---
id: DP.M.065
name: 4 условия легитимации temporal-derivation routing
name_ru: 4 условия легитимации temporal-derivation routing
name_en: Four Conditions for Legitimate Temporal-Derivation Routing
type: method
status: active
summary: "Routing через изменяемую Карту (routing_key → path) — temporal fallback, по умолчанию FAIL conjunctive screening ЭМОГССБ по Стабильности. НО: при выполнении всех 4 условий одновременно паттерн становится допустимым: (1) нет override; (2) total pure derivation (каждый kind → ровно один target, нет default/wildcard); (3) freeze-at-assignment (path материализуется в task при pending→assigned); (4) раздельная Карта от справочника. Если хотя бы одно не выполнено → temporal fallback → FAIL."
created: 2026-05-17
trust:
  F: 3
  G: domain
  R: 0.80
epistemic_stage: emerging
related:
  uses: []
  references: []
  realized_by: []
tags: [routing, derivation, temporal-fallback, stability, archgate, determinism]
wp: WP-324
---

# 4 Условия Легитимации Temporal-Derivation Routing (DP.M.065)

## 1. Контекст

Routing через изменяемую Карту (`routing_key → derived path`) — temporal fallback того же класса, что explicit path-fallback (main → PR → branch). По умолчанию **FAIL conjunctive screening ЭМОГССБ по Стабильности**: между постановкой task'а и его исполнением Карта может измениться, target-path сместится, появляется недетерминизм (>1 возможный `result_location` для одного task'а).

НО: при выполнении **всех 4 условий одновременно** паттерн становится допустимым.

## 2. Правило: 4 условия (все одновременно)

| # | Условие | Что значит | Test |
|---|---------|------------|------|
| 1 | **Нет override** | Никаких manual path-args в task'е; routing только через Карту | grep на task-schema: есть ли `path`/`result_location_override`? Если да → FAIL |
| 2 | **Total pure derivation** | Каждый `kind` в Карте имеет ровно один target; нет default/wildcard ветвлений | Проверить Карту: есть ли `_default`, `*`, fallback-ветви? Если да → FAIL |
| 3 | **Freeze-at-assignment** | Диспетчер материализует derived path в task при переходе `pending→assigned`; последующие изменения Карты не влияют на frozen task'и | Прочитать диспетчер: записывает ли он `result_location` в task at assignment? Если нет → FAIL |
| 4 | **Раздельная Карта от справочника** | Карта (kind → path) хранится отдельно от справочника domain-entities (kind → семантика); изменение одного не дёргает другое | Прочитать структуру: один файл или два? Если один → FAIL (смешанная ответственность) |

## 3. Универсальный rule

«Если строится routing через изменяемую таблицу, проверить все 4 условия ДО ArchGate. Если ≥1 не выполнено — temporal fallback → FAIL Стабильность → отказ от паттерна, искать explicit path или freeze-at-creation-time».

## 4. Анти-паттерн

| Анти-паттерн | Симптом | Лечение |
|--------------|---------|---------|
| **Override + Карта одновременно** | task иногда идёт по Карте, иногда по explicit path | §2.1 — убрать override полностью или отказаться от Карты |
| **Wildcard в Карте** | Новые kind'ы автоматически попадают в default, undocumented routing | §2.2 — explicit kind → path mapping для каждого |
| **Карта читается at-execution-time** | Изменение Карты задним числом ломает уже poставленные task'и | §2.3 — freeze-at-assignment |
| **Карта внутри domain-entity файла** | Изменение семантики дёргает routing и наоборот | §2.4 — split файлов |

## 5. Применимость

- **Agent Inbox routing** — task → result_location через kind
- **KE-routing** — capture → target Pack/file через type
- **Pack-watcher targeting** — change → notification channel через scope
- **Любое derived-path вычисление** — где path не передаётся explicitly, а выводится из meta

## 6. Различение с distinction в Pack

Существующее различение «Однозначное result_location ≠ Fallback-цепочка» (warm-distinctions.md, WP-324 Ф1) фиксирует **negative-rule** (что НЕ делать). Этот method даёт **positive recipe** — при каких условиях derived routing всё-таки приемлем. Различение и method работают парой: distinction предупреждает, method легитимирует при выполнении условий.

## 7. Пример (WP-324 C-review verdict, 17 мая 2026)

Обсуждалось routing для Agent Inbox: `kind: rcs-snapshot-analysis` → derived `inbox/agent/results/{date}/{task_id}-analysis.md`. C-review verdict:

- ✅ Условие 1: нет override (task не передаёт path)
- ⚠️ Условие 2: kind → path mapping has implicit fallback на main branch — нужен fix
- ❌ Условие 3: диспетчер вычисляет path at-execution-time — не freeze-at-assignment
- ✅ Условие 4: Map в отдельном файле от domain registry

Итог: 2/4 условий выполнены → паттерн FAIL → принято: использовать explicit `result_location` в task'е, отложить derived routing до выполнения условий 2-3.
