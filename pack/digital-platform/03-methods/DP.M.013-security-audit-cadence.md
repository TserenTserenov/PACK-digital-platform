---
id: DP.M.013
name: Security Audit Cadence
name_ru: Трёхуровневый ритм аудита безопасности
name_en: Security Audit Cadence (3-tier governance)
type: method
status: active
summary: "Метод управления аудитом безопасности платформы через три уровня периодичности: event-driven (каждое архитектурное решение, ~0 ₽), weekly light-check (2 мин, ~0 ₽), daily automated deep-scan (systemd-timer + subagent с context isolation, ~$1.5/день). Архетип применим к любой platform с security-требованиями."
created: 2026-05-08
trust:
  F: 4
  G: domain
  R: 0.85
epistemic_stage: evidence
related:
  uses: [DP.M.008, DP.ARCH.001]
  references: [DP.SC.033]
  role: VR.R.002
tags: [security, audit, governance, archgate, weekly, daily, automated]
wp: WP-212
---

# Трёхуровневый ритм аудита безопасности (DP.M.013)

## 1. Определение

**Security Audit Cadence** — метод распределения аудита безопасности по трём уровням периодичности с разными стоимостями и глубиной проверки.

**Принцип:** аудит безопасности — не одноразовое событие, а непрерывный ритм с разной интенсивностью на разных горизонтах.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Глубина проверки ↔ стоимость (§2 таблица) | Per-ArchGate инлайн-чеклист (~0 ₽) ловит риск только в момент архитектурного решения, Daily headless-скан (~$1.5/день) — систематическую регрессию между решениями, Month Close (~$10+/1ч) — полный B7.4 A-F; метод явно разносит три режима по стоимости вместо единого «максимально глубокого» аудита на каждом шаге |
| Event-driven триггер (Per-ArchGate, §2: «каждое архитектурное решение») ↔ calendar-driven триггер (Week/Month Close, фиксированное расписание) | Event-driven ловит риск нового решения, но ничего не делает с дрейфом существующей системы между решениями; calendar-driven закрывает этот пробел ценой фиксированной, не адаптивной частоты |
| Автоматизация Daily-уровня (systemd-timer + context isolation, §2) ↔ нагрузка на внимание человека | §3 Escalation: «Daily critical flags → Day Open «Требует внимания»» — ежедневный headless-скан снимает нагрузку с человека на исполнение, но каждый critical flag канализируется в ежедневный ритуал открытия дня, и при частых находках это давление накапливается |

## 2. Три уровня

| Уровень | Триггер | Исполнитель | Глубина | Стоимость |
|---------|---------|-------------|---------|-----------|
| **Per-ArchGate** | Каждое архитектурное решение | Агент (inline) | §Б чеклист + STRIDE для нового сервиса | ~0 ₽ |
| **Week Close** | Еженедельно | Агент (spot-check) | Проверить `security-posture.md §3`: open_critical_count > 0? | ~0 ₽ |
| **Daily** | 04:45 МСК (systemd-timer) | VR.R.002 (headless, context isolation) | B7.4 разделы A–D (~10–15 мин) | ~$1.5 |
| **Month Close** | Первый Пн месяца | VR.R.002 (Sonnet, ~1ч) | B7.4 разделы A–F → обновить security-posture.md | ~$10+ |

## 3. Escalation

- Daily critical flags → Day Open «Требует внимания»
- Week Close: open_critical_count > 0 → добавить WP-212 в следующий WeekPlan
- Month Close → коммит `docs(WP-212): security audit YYYY-MM`

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| Daily как «настоящий» аудит | Внимание смещается на Daily-уровень (единственный автоматизированный, systemd-timer, §2) как на основной аудит, а Week Close spot-check (§2: «open_critical_count > 0?») недооценивается как формальность — хотя именно еженедельный уровень отвечает за эскалацию накопленных critical находок в WeekPlan (§3) |
| Per-ArchGate держится только на дисциплине | Внимание съезжает с per-ArchGate уровня (§2: «каждое архитектурное решение», инлайн, без таймера или дашборда-напоминания) в пользу Daily/Month — практик недооценивает именно инлайн-проверку под давлением скорости решения, потому что она, в отличие от остальных трёх уровней, не создаёт внешнего сигнала о пропуске |

## 4. Артефакты

- **Dashboard:** `DS-ecosystem-development/.../security-posture.md`
- **Чеклист:** `.../Identity-and-Access/B7.4-external-audit-checklist.md`

## 5. Связи

- **DP.M.008** — правила работы IWE-агентов (операционный контекст)
- **DP.ARCH.001** — характеристика Безопасность (Security, «С» в ЭМОГССБ)
- **VR.R.002** — роль Аудитор, исполнитель daily и monthly уровней

---

> 2026-07-27 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 2). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
