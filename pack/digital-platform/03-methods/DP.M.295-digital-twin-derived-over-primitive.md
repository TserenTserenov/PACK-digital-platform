---
id: DP.M.295
title: "Приоритет Derived-значения над пересчётом из примитивов при чтении цифрового двойника"
type: method
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
source: "session-transcript 2026-06-08 + git diff personal-guide-render/SKILL.md"
valid_from: 2026-06-08
---

# Приоритет Derived-значения над пересчётом из примитивов при чтении цифрового двойника

## Суть

При чтении цифрового двойника через Aisystant MCP: если `path:3_derived → 3_4_qualification.stage` содержит непустое значение ≥ 2 — оно авторитетнее, чем пересчёт из отдельных RCS-срезов (W/M1/M2/M4).

## Алгоритм

1. Читать `3_derived`, не `2_collected` — только Derived-путь содержит агрегированные значения.
2. Если `stage` заполнен и ≥ 2 → записать как `stage_confirmed`, пропустить диалог уточнения.
3. Нулевые поля `marathon_steps_total=0`, `training_passed_total=0` — реальные нули, не признак отсутствия профиля. Не снижать ступень на их основании.
4. Пересчёт из примитивов — только когда Derived отсутствует.

## Обобщение

Когда Derived-значение уже существует в хранилище — не пересчитывать из примитивов. Derived — кэш с гарантиями платформы. Аналогия: если в реестре есть итоговая оценка, не суммировать её компоненты заново.

## Связи

- Digital Twin: `Память.Derived` (HD #27, DP.D.052)
- Использует: Портной (DP.ROLE.030), diagnose-iwe skill, personal-guide-render skill
