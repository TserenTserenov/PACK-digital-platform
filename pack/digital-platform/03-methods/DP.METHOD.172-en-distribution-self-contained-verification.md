---
id: DP.METHOD.172
name: "Верификация самодостаточности EN-дистрибуции: три оси"
type: method
pack: PACK-digital-platform
domain: digital-platform / international-distribution
trust: confirmed
epistemic_stage: observed
valid_from: 2026-07-11
source: "FMT-exocortex-template commit 7eeee9e; DS-my-strategy commit dc852177d (WP-401 Ф5)"
related:
  concepts: [DP.D.056]
schema_version: 1
---

# DP.METHOD.172 — Верификация самодостаточности EN-дистрибуции: три оси

## Описание

Чеклист верификации международной (EN) дистрибуции перед публикацией. Критерий: EN-репо не создаёт зависимость от исходного (RU) репо ни в одной точке.

## Три оси верификации

1. **Текст**: нулевые упоминания исходного репо в README / docs.
   - Команда: `grep -r "<source-repo-url>" README* docs/` → нулевой результат = pass.
2. **Сообщество**: собственные Issues и Discussions в целевом репо (не redirect на источник).
   - Проверить: Settings → Features → Issues включены + ни одна ссылка не ведёт к source Issues.
3. **Установка**: команда install/fork указывает на целевой EN-репо, не на исходный.
   - Команда: `grep -r "<source-install-cmd>" README* docs/` → нулевой результат = pass.

## Антипаттерн

Оставить ссылки на issue-tracker или установку исходного репо «потому что они корректные» — международный пользователь попадает на другой язык, friction и confusion.

## Применение

Любой multilingual open-source проект или SaaS с языковой сегментацией дистрибуции: при первой публикации и при каждой major миграции.

## Источник

WP-401 Ф5.3-Ф5.6. Session-close 2026-07-10.
