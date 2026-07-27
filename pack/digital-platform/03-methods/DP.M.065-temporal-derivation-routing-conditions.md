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

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Полнота проверки 4 условий одновременно (§2) ↔ скорость прохождения ArchGate | Проверка override / total pure derivation / freeze-at-assignment / раздельная Карта требует чтения диспетчера и структуры файлов (см. колонку Test §2), тогда как беглое «выглядит стабильным» прошло бы ArchGate быстрее без явного grep/чтения кода |
| Строгость conjunctive-правила «все 4, не по отдельности» (§2) ↔ практичность частичного прогресса | Команда может честно выполнить 2-3 условия из 4 (как в примере §7: 2/4), но правило не даёт частичного зачёта — это расходится с ощущением «мы ведь почти сделали» |
| Раздельность Карты и справочника domain-entities (условие 4) ↔ удобство держать routing-логику рядом с семантикой | Разделение на два файла архитектурно чище (изменение одного не дёргает другое), но повышает cognitive overhead поддержки двух мест вместо одного при добавлении нового kind |

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

## Bias-Annotation

_Куда систематически съезжает внимание практикующего. Статус карточки — `epistemic_stage: emerging`: пометка `tentative` по прецеденту WP-448 Ф12._

| Bias | Direction of distortion |
|------|--------------------------|
| _(tentative)_ Условие 3 (freeze-at-assignment) проверяется на слово, не по коду | При проверке условия 3 внимание тянется поверить диспетчеру на слово («наверное записывает path при assignment»), не читая реализацию буквально — это единственное из 4 условий, которое требует прочитать код диспетчера, а не структуру файла/схему, поэтому его чаще проверяют поверхностно (в примере §7 именно условие 3 оказалось FAIL) |
| _(tentative)_ Дробь «2/4» смягчает бинарность conjunctive-правила | При виде промежуточного результата вроде «2/4 условия выполнены» (§7) внимание тянется усреднить его в «половина — уже неплохо», прежде чем применить §3 универсальное правило — числовая дробь создаёт интуицию частичного успеха, которая противоречит бинарной природе screening (≥1 не выполнено → FAIL) |

## 7. Пример (WP-324 C-review verdict, 17 мая 2026)

Обсуждалось routing для Agent Inbox: `kind: rcs-snapshot-analysis` → derived `inbox/agent/results/{date}/{task_id}-analysis.md`. C-review verdict:

- ✅ Условие 1: нет override (task не передаёт path)
- ⚠️ Условие 2: kind → path mapping has implicit fallback на main branch — нужен fix
- ❌ Условие 3: диспетчер вычисляет path at-execution-time — не freeze-at-assignment
- ✅ Условие 4: Map в отдельном файле от domain registry

Итог: 2/4 условий выполнены → паттерн FAIL → принято: использовать explicit `result_location` в task'е, отложить derived routing до выполнения условий 2-3.

---

> 2026-07-27 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 3). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
