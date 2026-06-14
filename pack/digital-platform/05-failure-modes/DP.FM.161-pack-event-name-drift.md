---
id: DP.FM.161
name: "pack-event-name-drift: Pack документирует выдуманное имя события, в коде другое имя"
type: failure-mode
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: formalized
category: pack-code-consistency
severity: high
valid_from: 2026-06-11
related:
  see_also: [DP.FM.096]
tags: [pack, domain-event, naming, drift, anti-dup-audit, documentation]
source: "git diff PACK-digital-platform (1fd1b73, P1-P9-calibration-matrix.md), session 2026-06-11"
schema_version: 1
---

# DP.FM.161 — pack-event-name-drift

## Описание

Pack документирует доменное событие под одним именем (например, `TierUpgraded`), тогда как реальный код эмитит событие под другим именем (`tier_changed`). Расхождение обнаруживается только через anti-duplication audit или при попытке подписки на событие по имени из Pack.

## Контекст возникновения

- Pack заполняется «сверху вниз» (design-first): архитектор придумывает имя до реализации
- После реализации имя меняется (snake_case vs PascalCase, сокращение, рефакторинг), но Pack не обновляется
- Anti-dup audit сравнивает имена в Pack с именами в коде → находит расхождение

## Профилактика

**Перед записью имени события в Pack — grep-верификация:**
```bash
grep -rn "emit\|publish\|dispatch" <code-dirs> | grep -i "<event_name>"
```

Правило: имя события в Pack = точное строковое значение из кода, не концептуальное описание.

## Фикс при обнаружении

1. Найти фактическое имя в коде: `grep -rn "tier_changed" db/ engines/ workers/`
2. Обновить Pack: заменить `TierUpgraded` → `tier_changed` с пометкой источника
3. Открыть tracked item: «возможны другие расхождения — запустить полный anti-dup audit»

**Источник антипаттерна:** Design-first без feedback-loop к коду. Решение — Code-first для имён событий: извлекать имя из кода в Pack, не наоборот.
