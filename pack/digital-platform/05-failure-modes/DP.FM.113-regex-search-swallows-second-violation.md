---
id: DP.FM.113
name: "Regex `search()` глотает второе нарушение в multi-violation validators"
type: failure-mode
domain: digital-platform
pack: PACK-digital-platform
trust: observed
epistemic_stage: formalized
category: tooling
severity: major
valid_from: 2026-05-30
related:
  see_also: [DP.FM.038, DP.FM.040]
  remediated_by: [DP.M.239]
tags: [validator, regex, false-negative, silent-miss, policy-enforcement]
source: "WP-7 RPA close (peer-session 2026-05-30-31, multi_domain_projection_worker/db.py:112)"
schema_version: 1
---

# DP.FM.113 — Regex `search()` глотает второе нарушение в multi-violation validators

## Описание

Класс-уязвимость для валидаторов, линтеров, policy-checker'ов на основе regex. `re.search()` возвращает **первое** совпадение и останавливается. Если правило валидации формулируется как «во входе не должно быть НИ ОДНОГО из набора X» — и в источнике два разных нарушения, `search()` поймает только первое; reviewer фиксирует «есть один баг, fix → release», а второе нарушение остаётся в production.

Failure mode **асимметричен:** false-negative на legitimate-выглядящих случаях, не false-positive. Поэтому в pre-commit / CI / security-scanner не виден как «ложное срабатывание».

## Симптом

- Policy-валидатор пропускает источник, в котором при ручном grep обнаруживаются 2+ нарушений.
- После fix первого нарушения вторая копия паттерна остаётся в проде, проявляется как production bug через дни/недели.
- Reviewer и автор fix-а уверены, что validator покрывает класс целиком.

## Механизм

1. Validation rule: «класс нарушений X не должен встречаться» (negative invariant).
2. Реализация: `if PATTERN.search(src): report_violation(first_match)`.
3. На входе src содержит ≥2 matches PATTERN.
4. `search()` возвращает первый match, validator репортит его, выходит.
5. Reviewer fixит первое нарушение, validator зеленеет.
6. Второе нарушение проходит gate невидимо.

## Тест применимости

«Должен ли валидатор поймать **все** нарушения данного класса в одном входе?»
- **Да** → требуется `findall()` или `finditer()` + явный for-loop по результатам.
- **Нет (early-exit достаточно)** → `search()` корректен (например, «достаточно поймать любое — release блокируется»).

## Remediation

См. DP.M.239 (defense-in-depth bail-out при refactor regex single→multi):
- Замена `search()` → `findall()` / `finditer()` с aggregation по всем results.
- При миграции существующего кода — bail-out branch на `len(matches) > 1` с CRITICAL log + preservation original.

## Прецеденты

- WP-7 RPA close (2026-05-30): `_STATIC_FUNC_RE.search(src)` в `multi_domain_projection_worker/db.py:112` пропускал второе нарушение `rewards.compute_b` после ранее зафиксированного `public.compute_a`. Fix через `findall()` + for-loop.

## Связанные паттерны

- **DP.FM.038** — silent-pass validator на отсутствующем входе. Этот FM — silent-miss на multiple matches. Оба класса дают false-green в CI, но через разные механизмы.
- **DP.FM.040** — silent-null-parser. Семейство silent-* failure modes.
- **HD #51** — HTTP 200 + 0 bytes = false-green. Обобщённая форма «успешный exit без полного покрытия».

## Эвристика для reviewer

При ревью regex-based validator всегда задать вопрос: «А если matches несколько?». Если ответ «search() возьмёт первый» — это red flag для negative-invariant policy.
