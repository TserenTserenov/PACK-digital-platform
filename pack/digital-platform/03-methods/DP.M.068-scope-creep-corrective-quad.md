---
id: DP.M.068
name: Scope-creep corrective quad — 4 действия в один fix-pass
kind: Method
status: active
created: 2026-05-17
sources:
  - WP-316 commit d1e5465e «откат scope creep + IntegrationGate + audit cognitive_proxy + hard opt-in»
related:
  complements: [DP.M.062]
  applies_to: [IntegrationGate, ArchGate]
---

# DP.M.068: Scope-creep corrective quad

## Определение

При обнаружении scope creep в активном РП (через subagent-review, self-revisit или audit) недостаточно «убрать лишний scope». Обязательно выполнить **четыре corrective action в один fix-pass**, не piecewise:

1. **Rollback creep** — вернуть scope к исходной фокусной обещательной границе.
2. **Retrofit IntegrationGate** — если creep произошёл из-за пропуска фаз (1)-(3) обещание→сценарий→роль, retro-применить эти фазы к расширенному scope (если он остаётся) или зачеркнуть.
3. **Audit affected entity** — entity, обросшая лишней логикой, пересматривается под новый scope (обязанности, инварианты, связи).
4. **Harden security at boundary** — если creep потянул PII или права в новую плоскость, обновить consent-gate, hard opt-in, B7.3 чеклист.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Единая точка решения (все 4 action в один fix-pass) ↔ размер и сложность одного коммита | Объединение rollback + IntegrationGate-retrofit + entity audit + security-hardening в один коммит даёт «одну точку решения» вместо дробления, но такой коммит закономерно крупнее и сложнее для ревью, чем последовательность из 4 маленьких |
| Скорость закрытия РП ↔ полнота покрытия всех 4 осей | Обнаружив creep, есть соблазн закрыть РП сразу после отката (action 1), не проверяя, действительно ли задеты остальные 3 оси (IntegrationGate retrofit, entity audit, security boundary) — метод явно требует проверить все 4, даже если по ощущениям достаточно только отката |

## Тест применимости

> «Обнаружен ли scope creep ≥1 уровня в активном РП?»

Да → все 4 actions проверяются в одном fix-pass.

## Зачем «в один fix-pass»

Антипаттерн: «откатим creep, security и IntegrationGate сделаем потом». **Потом не делается** — открытое РП закрывается с дырой по 2-3 из 4 осей. Один fix-pass = одна точка решения, либо все 4 закрыты, либо РП ещё не close-ready.

## Прецедент

WP-316 d1e5465e: subagent C-review обнаружил scope creep (cognitive_proxy обросла обязанностями двух разных entity). Один fix-commit:

- (1) rollback: убран кросс-доменный proxy.
- (2) retrofit IntegrationGate: дополнены SC + ROLE для оставшейся части scope.
- (3) audit: cognitive_proxy переописана с новыми границами.
- (4) hard opt-in: PII-границы пересмотрены, consent-gate ужесточён.

## Связь

- [[DP.M.062]] Two-pass review — даёт момент обнаружения creep (subagent или self-revisit).
- IntegrationGate (CLAUDE.md §2) — corrective quad является retrofit-протоколом для нарушения IntegrationGate.
- B7.3 PII checklist — action (4) обращается к нему.

## Антипаттерн

- **Piecewise fix.** Откат creep сегодня, IntegrationGate-retrofit завтра, security-audit «когда-нибудь». РП закрывается «по факту отката» без проверки трёх остальных осей.
- **«Это был bugfix, не scope creep».** Если расширение scope без формального WP-amendment произошло — это всегда creep, даже если оправдано. Quad применять.

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| Ощущение завершённости после отката | Как только action (1) rollback creep выполнен и код снова выглядит «чистым», внимание практикующего снижается для action (2)-(4) — retrofit/audit/hardening проверяются формально «для галочки», а не с той же тщательностью, с которой был обнаружен сам creep |
| Action (4) security недооценивается, если creep не выглядел PII-связанным на поверхности | Внимание фокусируется на очевидной части отката (структура/код), недооценивая вопрос, не потянул ли расширенный scope новые PII/права попутно — граница consent-gate проверяется только если creep изначально «выглядел» security-relevant, а не по умолчанию для каждого случая quad |

## Источник

WP-316 (2026-05-17), close-pass с обнаруженным scope creep после Ф8 review.

---

> 2026-07-27 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 3). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
