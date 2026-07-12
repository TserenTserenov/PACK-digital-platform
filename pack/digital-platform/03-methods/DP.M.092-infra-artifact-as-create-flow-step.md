---
id: DP.M.092
title: Инфраструктурный артефакт как обязательный шаг create-flow
type: method
domain: digital-platform
tags: [pack, ci, governance, create-flow, infrastructure]
status: superseded
superseded_by: DP.M.093
superseded_date: 2026-07-12
valid_from: 2026-05-18
schema_version: 1
---

> **SUPERSEDED 2026-07-12 → [DP.M.093](DP.M.093-ci-artifact-in-create-flow.md).** Тот же метод, записан дважды в соседних KE-коммитах 18 мая (`7a89d39`, затем `a5096fd`). Связи этого файла (DP.M.078, AR.207, feedback_release_gates.md) перенесены в DP.M.093.

# DP.M.092 — Инфраструктурный артефакт как обязательный шаг create-flow

## Описание

Важные инфраструктурные артефакты (CI guard, линтер, security scan) встраиваются в create-flow нового репо как обязательный шаг — не как отдельная задача после создания.

## Вход

Запрос на создание нового репо определённого типа (напр. Pack via `/pack-new`)

## Выход

Репо с установленным CI guard с первого коммита

## Алгоритм

1. Create-flow skill вызывает `pack-ci-install.sh` после `git init + initial commit`
2. Скрипт добавляет `.github/workflows/pack-lint.yml` с R1-R4 проверками
3. CI guard коммитится как часть initial setup

## Критерий применимости

«Новый репо-тип создаётся несколько раз в год, и важный артефакт регулярно забывается?» → встрой в create-flow

## Failure mode без метода

При ручной установке CI guard: каждый новый репо-тип создаётся без охраны до момента, когда кто-то вспомнит; при параллельной работе peer-агентов без CI guard возникают ID-коллизии (48+ renumber за 8-18 мая).

## Применимо к

Любые шаблонные артефакты, обязательные для каждого репо типа: линтер, security scan, issue-template, dependabot, CODEOWNERS.

## Связи

- DP.M.078 (architectural-rule-propagation)
- AR.207 (three-cases-systemic-detector — системный детектор инвариантов)
- `feedback_release_gates.md` (валидатор без CI = в чужих руках)
