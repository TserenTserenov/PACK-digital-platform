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

## 4. Артефакты

- **Dashboard:** `DS-ecosystem-development/.../security-posture.md`
- **Чеклист:** `.../Identity-and-Access/B7.4-external-audit-checklist.md`

## 5. Связи

- **DP.M.008** — правила работы IWE-агентов (операционный контекст)
- **DP.ARCH.001** — характеристика Безопасность (Security, «С» в ЭМОГССБ)
- **VR.R.002** — роль Аудитор, исполнитель daily и monthly уровней
