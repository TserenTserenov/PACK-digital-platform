---
id: DP.METHOD.144
name: "Feature flag вместо удаления кода при dual-write cutover"
type: method
pack: PACK-digital-platform
domain: digital-platform / migration
trust: confirmed
epistemic_stage: observed
valid_from: 2026-06-09
source: "session-transcript 2026-06-09 + git diff DS-my-strategy (WP-392-b1-tier-source.md, commit cb48fd55b)"
schema_version: 1
---

# DP.METHOD.144 — Feature flag вместо удаления кода при dual-write cutover

## Описание

При миграции dual-write → single canonical writer немедленное удаление старого кода опасно: если новый writer молча не работает, поле в проде перестанет обновляться без каких-либо ошибок.

## Безопасный паттерн

1. Обернуть старый writer в feature flag `DISABLE_*` (default = OFF, т.е. дублёр активен).
2. Задеплоить как есть — прод без изменений.
3. Верифицировать нового writer через эндпоинт `/health/deep` (курсор не отстаёт, правило enabled).
4. Выставить флаг = ON в проде → наблюдать N дней → удалить код.

## Отличие от dual-write safety net

Смежный паттерн «когда использовать dual-write» отвечает на вопрос старта миграции. Этот метод — про конкретный механизм обратимого cutover в конце миграции, через флаг, а не про сам dual-write.

## Применение

Любой cutover с одного writer'а на другой, где молчаливый отказ нового writer'а незаметен без явной проверки.

## Источник

session-transcript 2026-06-09; git diff DS-my-strategy (WP-392-b1-tier-source.md, commit cb48fd55b, `DISABLE_BOT_TIER_SYNC`)
