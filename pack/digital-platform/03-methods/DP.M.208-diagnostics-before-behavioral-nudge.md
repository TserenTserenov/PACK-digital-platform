---
id: DP.M.208
name: "Diagnostics до behavioral nudge при stuck-сегменте"
type: method
domain: retention / customer-success / lifecycle
trust: experiential
epistemic_stage: confirmed
status: active
valid_from: 2026-05-28
schema_version: 1
source: "session-close 2026-05-28 (peer-session 15: WP-250 bottleneck-tech)"
---

# DP.M.208 Diagnostics до behavioral nudge при stuck-сегменте

## Решение

Когда сегмент пользователей застрял на этапе воронки и поступила задача «разбудить нуджем» — сначала классифицировать причины застревания (минимум: zero-activity / есть активность но ниже порога / активность выше порога но event-pipeline не материализовал переход), и только потом таргетировать nudge под каждый кластер.

## Аргумент

Blind nudge для всего сегмента имеет несколько отказов:

1. **Demotivation активных.** Пользователи, чью активность система не видит из-за false-negative bug (поведенческий ≠ технический bottleneck — см. DP.D.108), получают сообщение «у вас нет активности» при наличии активности. Двойной репутационный ущерб: bug + spam.
2. **Asymmetry cost.** 1 день diagnostics-запроса = SQL + просмотр ≤30 пользователей сегмента. Стоимость задержки nudge на 1 день несравнима с откатом доверия активных.
3. **Wrong-treatment risk.** Поведенческий и технический bottleneck лечатся разными интервенциями. Nudge для технического кластера = ноль эффекта на цифры + потеря доверия.

## Алгоритм

1. **Classify segment by primary-event presence:**
   - **Zero-activity:** нет первичных событий в activity_log за окно
   - **Low-activity:** есть события, но ниже порога перехода
   - **High-activity-stuck:** есть события выше порога, но evaluator не материализовал переход
2. **Generate diagnostic SQL.** Для каждого кластера — конкретные критерии (event-counts, last-event-timestamp).
3. **Run on production / staging snapshot.**
4. **Targeted intervention per cluster:**
   - Zero → behavioral nudge (если репутационно безопасно)
   - Low → tutorial / UX-наращивание порога
   - High-stuck → technical bug-fix в evaluator (приоритет ↑↑↑)

## Гибрид

Если zero-activity кластер очевиден заранее (cohort с zero events) — immediate nudge параллельно с diagnostics для остальных. Не блокировать low-risk сегмент диагностикой high-risk.

## Граница применимости

- Retention / reactivation кампании
- Churn-prevention
- Funnel-unstuck (любой этап воронки)
- Programmatic outreach с риском восприятия как spam

## Анти-применение

- Случай, когда первичные события технически невозможны (например, новый функционал, никто ещё не пробовал) → nudge без диагностики OK, кластер однороден.
- Очень малый сегмент (≤3 пользователя) → ручной разбор каждого, без алгоритма.

## Связи

- Различение: DP.D.108 поведенческий ≠ технический bottleneck
- Источник: WP-250 bottleneck-tech, 2026-05-28
