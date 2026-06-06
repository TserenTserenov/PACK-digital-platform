---
id: DP.M.277
title: "Single Pack method → N surfaces by 1:1 contract conformance"
type: method
pack: PACK-digital-platform
domain: digital-platform
epistemic_stage: confirmed
trust: high
valid_from: 2026-06-05
source: "session-close 2026-06-05 (peer-session 2026-06-05-09 outcome), git diff PACK-digital-platform DP.METHOD.053 + FMT discovery-session/SKILL.md + DS-my-strategy docs/discovery-prompt-portable.md"
related:
  - DP.METHOD.053
  - DP.D.058
---

# DP.M.277 — Single Pack method → N surfaces by 1:1 contract conformance

## Описание

Pack-метод (`DP.METHOD.NNN`, этапы 1-K) — единый источник. Surface-реализации (skill, портативный промпт, MCP-инструмент) обязаны иметь **построчное 1:1 соответствие этапов** с Pack-методом.

## Принцип

**Single source of truth:** один Pack-метод описывает этапы (1-K).
**N surfaces:** каждый канал реализации (Claude Code skill, ChatGPT-промпт, серверный multi-turn) ссылается на тот же Pack-метод через `realizes: DP.METHOD.NNN` в frontmatter.
**Contract conformance:** этапы surface совпадают с этапами Pack-метода 1:1 — по нумерации, составу, порядку.

## IPO

**Input:** Pack-метод `DP.METHOD.NNN` (этапы 1-K), требование выпустить surface-реализацию в новом канале
**Process:**
1. Создать surface (SKILL.md / prompt-snippet / MCP-tool описание)
2. В frontmatter surface зафиксировать `realizes: DP.METHOD.NNN`
3. Скопировать этапы 1-K из Pack-метода в surface (с учётом особенностей канала, но без потери/добавления этапов)
4. При ревью — построчная сверка «единый источник метод↔surface» (этап-к-этапу)
**Output:** surface с 1:1 conformance к Pack-методу

## Тест conformance

«Могу я в одну колонку выстроить этапы Pack-метода и этапы surface, чтобы они совпали по нумерации и составу?»
- Да → 1:1 conformance соблюдён
- Нет (этап добавлен/удалён/перенумерован) → drift, нарушение SoT

## Antipattern (drift)

Surface-реализация дрейфует от Pack-метода: этап добавлен в skill, удалён в промпте, перенумерован в MCP-tool. Drift невидим без явной сверки, пилот получает разные ответы из разных каналов на один и тот же запрос.

## Применение (пример WP-378)

`DP.METHOD.053` (NEP extraction, этапы 1-7) — единый источник в Pack. Three surfaces:
- (а) локальный навык `/discovery-session` (Claude Code, FMT extensions)
- (б) переносимый промпт для чужого LLM (`docs/discovery-prompt-portable.md`, interim-мост для пилота с ChatGPT)
- (в) будущий серверный multi-turn канал `run_planner` (MCP, гейт DP.SC.162)

R23-верификация требовала построчного 1:1 соответствия этапов 1-7 во всех трёх surfaces.

## Связи

- DP.METHOD.053 (NEP extraction) — образец Pack-метода с N surfaces
- DP.D.058 (Service Clause ≠ Carrier) — surface = носитель, метод = обещание
