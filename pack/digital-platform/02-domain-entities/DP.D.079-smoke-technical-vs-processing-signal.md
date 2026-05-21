---
id: DP.D.079
title: Технический vs обрабатывающий сигнал smoke-теста
type: distinction
domain: pack/digital-platform
trust: high
epistemic_stage: candidate
status: active
valid_from: 2026-05-20
source: captures.md L3137 (feed:session-close 2026-05-20, WP-346 ba3b936)
---

# DP.D.079: Технический vs обрабатывающий сигнал smoke-теста

> Smoke-тест R-сервиса (обходчик, контролёр, аудитор) разделяется на **два ортогональных уровня сигнала**. Зелёный технический ≠ зелёная работа; отсутствие subjects ≠ отказ контракта.

## Различение

| | (а) Технический | (б) Обработка |
|---|---|---|
| **Что подтверждает** | Процесс запущен, прочитал config, подключился к источникам, не упал | Выполнено ≥1 действие над реальными subjects |
| **Сигнал FAIL** | Crash / unreadable config / DSN unreachable | Subjects есть, но 0 обработано (баг в фильтре) |
| **Сигнал PASS** | Exit=0, лог содержит «started» | `processed > 0` OR `processed == 0 AND expected_subjects == 0` |
| **Опасная зона** | — | Маскировка «0 processed = success» когда expected > 0 |

## Контракт smoke-runner

Возвращать structured-report с обоими сигналами раздельно:

```json
{
  "technical": "pass",
  "processed": 0,
  "expected_subjects": 0,
  "verdict": "pass-empty-cohort"
}
```

`verdict` различает:
- `pass-with-work` — processed > 0
- `pass-empty-cohort` — processed = 0 ∧ expected = 0 (валидно)
- `fail-zero-processed` — processed = 0 ∧ expected > 0 (баг)
- `fail-technical` — crash/DSN/config

## Тест применимости

«Когорта в этом сервисе ещё может быть пустой по дизайну (новый R-сервис до GA, wave-1 не запущена)?» → Да → различай (а) и (б). Иначе достаточно общего PASS.

## Связи

- **Расширяет:** `memory/feedback_solo_smoke_blind_spots.md` (ортогональная ось: blind_spots — про pre-flight vs UX, здесь — про пустую когорту)
- **Применимо к:** WP-346 (R46 Контролёр развития), WP-188 wave-rollout, любому новому R-сервису до GA
- **Источник:** WP-346 commit ba9b936 «Smoke DRY_RUN=1: 0 pilots R2 (ожидаемо, когорта ещё не наполнена)»
