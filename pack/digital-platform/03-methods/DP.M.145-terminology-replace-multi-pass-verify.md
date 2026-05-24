---
id: DP.M.145
name: "Terminology replace — multi-pass verify through peer agent"
name_ru: "Замена терминологии — multi-pass verify через peer-агента"
type: method
status: draft
created: 2026-05-22
trust:
  F: 3
  G: domain
  R: 0.9
epistemic_stage: established
related:
  uses: [DP.ROLE.039]
  see_also: [DP.M.140]
tags: [terminology, refactoring, peer-agent, verification]
wp: WP-327 Ф5b (commit 0b5eac2e) + WP-300 review (Кими, 16 мая)
sources:
  - memory/lessons_terminology_verify_loop.md
  - memory/feedback_peer_agent_partial_edit.md
---

# DP.M.145 — Замена терминологии: minimum 2-3 verify→fix цикла через peer-агента

## 1. Проблема

При замене термина по canonical DP.D.NNN различению (пример: «Баллы» → «Бонусы» по DP.D.050) первый проход замены **никогда** не покрывает все места. Inconsistency остаётся в:

- payment/checkout flow (workshop.py, showcase.py)
- multi-language tier_config (5 языков синхронно)
- docstrings команд (не видны пользователю, но влияют на следующих агентов)
- словоформы («из баллов / балл / начисления баллов»)

Источник эпизода: WP-327 Ф5b (22 мая) — 3 итерации, 12 мест найдено суммарно.

## 2. Метод

### Шаг 1 — Первый проход (writer)

Замена по очевидным местам через grep/sed по основной форме термина. Коммит.

### Шаг 2 — Verify пеер-агентом (round 1)

Peer-agent (Kimi/независимый Claude) делает grep по словоформам, smaller patterns, multi-language synonyms. Отдельная сессия, без контекста writer'а.

### Шаг 3 — Fix (round 2)

Writer применяет найденные изменения. Коммит.

### Шаг 4 — Verify пеер-агентом (round 2)

Тот же peer-agent повторно сканирует. Особо проверяет: payment flow, tier-config labels (multi-lang), docstrings.

### Шаг 5 — Возможно ещё цикл (round 3)

Останавливаться **только** на PASS с 0 несоответствий.

## 3. Invariants

1. **Multi-pass:** одного прохода недостаточно — норма 2-3 итерации.
2. **Peer-driven:** verify делает не writer (slip-blindness), а отдельный агент.
3. **Stop condition:** 0 несоответствий, не «достаточно».

## 4. Грейп-чеклист (для peer-agent verify)

- [ ] Основная форма термина (single grep)
- [ ] Словоформы (множественное число, родительный падеж: «из X», «X-ом»)
- [ ] Английские varianты (если есть bilingual labels)
- [ ] Multi-language tier_config / i18n-файлы (ru/en/es/ar/fr и т.п.)
- [ ] Payment / checkout flow
- [ ] Docstrings и комментарии (видны следующим агентам)
- [ ] Тест-фикстуры

## 5. Прецеденты

| Дата | Замена | Проходов | Где находили на последних проходах |
|------|--------|----------|------------------------------------|
| 2026-05-22 | Баллы → Бонусы (DP.D.050) | 3 | payment flow, docstrings, tier_config 5 lang |
| 2026-05-16 | review WP-300 (Кими) | 2 | 6 drift + 7 residual в одном проходе |

## 6. Anti-pattern

«Прошёл первый раз, дальше не надо» — гарантированно оставляет 30-50% inconsistency, всплывает у первого пользователя feature.

## 7. Связи

- DP.ROLE.039 (Peer Agent) — носитель verify-роли.
- memory/feedback_peer_agent_partial_edit.md — детектор partial replace (peer-agent после терминологической замены обязан grep всего файла).
- memory/lessons_terminology_verify_loop.md — source-of-truth lesson, промотируется методом.
