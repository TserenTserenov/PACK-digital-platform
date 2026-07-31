---
id: DP.D.102
name: "Четыре канала событий IWE по семантике"
type: distinction
pack: digital-platform
layer: L4-Personal
status: active
valid_from: 2026-05-28
source: peer-session 2026-05-28-01 (WP-358 Ф6 анализ)
---

# DP.D.102: Четыре канала событий IWE по семантике

**Различение:** Календарь IWE Platform Ops ≠ process-catalog/ledger/watchdog ≠ inbox/reminder-*.md ≠ личный Google Calendar

## Описание

Четыре семантически разные зоны хранения и обработки событий в IWE. Смешение каналов ведёт к потере напоминаний (OwnerIntegrity-нарушение) или поломке derived-артефактов (запись в readonly ledger).

| Канал | Тип событий | Автоматика | Владелец |
|-------|-------------|------------|----------|
| **Google Calendar «IWE Platform Ops»** | One-shot дедлайны РП, встречи с мобильным push | Нет (ручной ввод) | Пилот |
| **process-catalog → ledger → watchdog** | Ритмические процессы (day-open, week-close, marathon) | Да (launchd/systemd + SLA) | Платформа |
| **inbox/reminder-\*.md** | Дискретные напоминания без автоматики | Нет (ScheduleWakeup) | Пилот (governance-репо) |
| **Личный Google Calendar** | Персональные события (встречи, личное) | Нет | Пилот |

## Тест

«Это one-shot дедлайн РП?» → Calendar. «Это ритмический процесс?» → process-catalog. «Это напоминание без автоматики?» → inbox/reminder-*. «Это личное событие?» → личный Calendar.

## Антипаттерн

Запись WP-358 Ф6 напоминания в `date-ledger.yaml` = нарушение OwnerIntegrity (ledger — derived artifact, перезаписывается при регенерации).

## Контекст

Возникло как решение конфликта в peer-session 2026-05-28-01: оба агента предложили date-ledger.yaml для WP-358 Ф6 reminder, но пилот указал на Google Calendar как правильный канал. Анализ routing-vocab + process-catalog header выявил четыре семантических зоны.
