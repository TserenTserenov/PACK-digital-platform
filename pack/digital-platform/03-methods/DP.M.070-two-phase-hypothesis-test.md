---
id: DP.M.070
name: Двухфазный тест гипотезы (baseline → parameterized)
kind: Method
status: active
created: 2026-05-17
sources:
  - WP-316 commits 61a4c50e (Ф6 параметризация + Ф8 baseline Honcho r=0.612), e43750ce (Ф8 Honcho r=0.761)
related:
  complements: [DP.M.057]  # ML A/B evaluation
  applies_to: [WP-218, WP-316]
---

# DP.M.070: Двухфазный тест гипотезы (baseline → parameterized)

## Определение

Методическое требование для валидации гипотез корреляции / эффективности признака:
два явно разнесённых этапа измерения.

1. **Phase 1 — baseline (raw):** быстрый proof-of-concept с **дефолтными параметрами**,
   без оптимизации. Цель — получить опорный сигнал за минимальный бюджет (<1ч), чтобы
   принять решение «продолжать или останавливаться» по явному reject-criterion.
2. **Phase 2 — parameterized (optimized):** настройка ключевых гиперпараметров (веса,
   prompt-template, sample size, агрегация) поверх baseline. Цель — confirm at peak,
   когда baseline уже преодолел порог.

## Тест применимости

> «Можно ли провести гипотезу через 2 phase'а с явным reject-criterion после phase 1?»

- **Да** → применять 2-phase pattern.
- **Нет** (нет дешёвого baseline; нет осмысленного reject-criterion) → пересмотреть гипотезу
  или ожидать full-budget эксперимента.

## Что даёт каждая фаза

| Фаза | Promise | Тип решения после фазы |
|------|---------|------------------------|
| Baseline | early-reject opportunity | продолжать / стоп |
| Parameterized | confirm at peak | вкладываться в production / отложить |

## Пример: WP-316 Honcho-производный proxy

- Phase 1 baseline (Ф8): r = 0.612, дефолтные веса, <1ч → выше threshold (0.5).
- Phase 2 parameterized (Ф8 продолжение): r = 0.761, настроенные веса и
  prompt-template → гипотеза подтверждена.

Если бы baseline дал r = 0.20 — гипотеза была бы reject'нута после Phase 1, экономия
2-3 рабочих дней на parameterized.

## Анти-паттерн

«Сразу parameterized (одна фаза)» — теряется early-reject opportunity, бюджет тратится
на мёртвые гипотезы, обнаружение которых возможно за час.

## Связи

- DP.M.057 (ML A/B Evaluation) — про **сравнение моделей**; этот метод — про **фазирование
  одиночного эксперимента**.
- DP.M.012 (Machine-Check Postcondition) — каждая фаза имеет своё машинно-проверяемое
  постусловие (reject_threshold для baseline, confirm_threshold для parameterized).
- `feedback_archgate_independent_review.md` — review **после** архитектуры; этот метод —
  **фазирование** внутри одной валидации, ортогонально review.

## Применимо к

- Correlation studies для indicators Память.Derived (Honcho-style proxy validation, IND.* calibration).
- Composite indicator weights calibration (см. line 1809 captures «Composite indicator»).
- WP-218 multiplier validation (IND.3.2.04).
- Любые ML-experiments с явным reject-threshold.
