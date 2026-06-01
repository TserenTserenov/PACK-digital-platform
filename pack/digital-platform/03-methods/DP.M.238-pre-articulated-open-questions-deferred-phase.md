---
id: DP.M.238
name: "Pre-articulated open questions в отложенной problem-framing фазе"
type: method
status: draft
valid_from: 2026-05-30
pack_refs:
  - DP.M.158  # archgate-defer-pattern — отложенный ArchGate
  - DP.M.163  # deferred-phase-finalization-checkpoint — checkpoint при отложении
  - DP.M.236  # phase-split-by-verification-class — родительский split
domains: [peer-session-prep, problem-framing, deferred-decision]
---

# DP.M.238 — Pre-articulated open questions в отложенной problem-framing фазе

## Описание

Метод подготовки отложенного problem-framing решения к peer-session / ArchGate. При выделении фазы problem-framing класса (см. DP.M.236) фиксируется ≥3 **конкретных** открытых вопроса с явными критериями качества, а не общая формулировка «обсудить тему».

## Когда применять

- Problem-framing под-задача выделена в отдельную фазу (см. DP.M.236)
- Решение откладывается на peer-session / ArchGate / следующий цикл стратегирования
- Возврат к решению ожидается через ≥1 неделю

## Критерии хорошего вопроса

1. **Бинарный или choice between explicit options** — не open-ended «обсудить»
2. **Ссылается на конкретный артефакт** — функция, строка кода, файл, метрика, ID
3. **Определяет, что зависит от ответа** — что изменится в системе в зависимости от choice

## Антипаттерн

«Обсудить с peer policy projection-rules validation» — что обсуждать конкретно? Через 2 недели peer-session начинается с реконструкции контекста, формулировки вопросов, обнаружения что 2 из 4 вопросов уже решены другим способом.

## Алгоритм

1. При выделении problem-framing фазы (DP.M.236, шаг 4) — пройти список под-задач этой фазы
2. Для каждой сформулировать ≥1 конкретный открытый вопрос по 3 критериям выше
3. Фиксировать вопросы в WP-context файле под секцией `## Открытые вопросы для <фаза>`
4. Маркер фазы: `target_close: TBD` + ссылка на список вопросов
5. При возобновлении (peer-session / ArchGate) — стартовать с готовых вопросов, не с реконструкции

## Тест качества

«Через 2 недели смогу ли я вспомнить, ЧТО именно надо решить, без re-reading контекста?» Да → вопросы артикулированы. Нет → артикулировать конкретнее.

## Эффект

Peer-session начинается с прицельной дискуссии, экономит ~30 мин reconstruction, decision-density выше.

## Применимо к

- Deferred ArchGate decisions
- Retrospective backlog items
- Любое отложенное решение, возобновляющееся через ≥1 неделю

## Источник

WP-7 RPA-policy phase сессия 2026-05-30. 4 нумерованных открытых вопроса записаны при выделении фазы — образец артикуляции:
1. RPA1 покрыт `compute_effective_amount(_v4)` — нужно ли проактивно extend split для `compute_typing_points` (mig 241) или ждать первого падения?
2. Как обработать situations когда в одной БД функция STABLE, в другой VOLATILE (cross-DB rules via FDW)?
3. Полезен ли dynamic pg_proc check at startup (нагрузка + cross-DB access) vs static maintainership?
4. CI gate location: neon-migrations pre-commit vs PR validation workflow?
