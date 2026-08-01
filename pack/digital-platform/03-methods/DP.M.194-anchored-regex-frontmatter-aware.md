---
id: DP.M.194
name: Anchored regex для frontmatter-aware матчинга
type: method
domain: DP
status: draft
valid_from: 2026-05-27
source_session: peer-сессия 20 (wp358-f9-status-regex-uniform, Тема 2 + review-01 H1)
---

# DP.M.194: Anchored regex (^...$ + MULTILINE) для frontmatter-aware матчинга

## Контекст

Regex'ы для YAML / Markdown frontmatter и других line-oriented форматов часто пишут как substring-match: `r'status: processing'`. Это ловит false-positive: `status: processing-failed` тоже matches. На больших файлах с trailing whitespace + multi-line YAML простой regex даёт скрытые баги.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Краткость substring-match (`status: processing`) ↔ защита от false-positive | Три обязательных компонента (`^...$`, `re.MULTILINE`, `\s*$`) удлиняют и усложняют regex — метод платит читаемостью паттерна за гарантию, что `processing-failed` и строка с trailing whitespace не пройдут за валидный матч |
| Быстрый regex-guard ↔ полноценный парсер формата | Метод сознательно ограничивает anchored regex ролью fast-path guard'а и не подменяет им YAML-парсер: выигрыш в скорости и простоте проверки оплачен тем, что извлечение значений остаётся за парсером |

## Алгоритм

Безопасный regex для line-oriented форматов состоит из трёх обязательных компонентов:

1. **Якоря `^...$`.** Защита от substring-матчинга. Контрпример: `r'status: processing'` matches `status: processing-extra`. Правильно: `r'^status:\s*"?processing"?\s*$'`.
2. **Флаг `re.MULTILINE`.** Без него `^` и `$` работают только на границах всего текста, а не на границах строк. На multi-line YAML/Markdown якоря без MULTILINE бесполезны.
3. **`\s*$` в конце.** Защита от trailing whitespace, который часто остаётся после copy-paste или редактирования IDE.

## Пример

```python
import re
# Правильно: matches "status: processing" только как целую строку
pattern = re.compile(r'^status:\s*"?processing"?\s*$', re.MULTILINE)

# Неправильно: matches "processing-extra", "processing-failed"
pattern_bad = re.compile(r'status: processing')
```

## Граница применимости

- Применять **профилактически**, даже когда текущий вход чист — защита от будущих мутаций входного формата.
- Подходит для любых text-pattern guard'ов в multi-line формате: YAML frontmatter, Markdown headings, log files, CSV headers.
- Не заменяет YAML-парсер для извлечения значений — только для guard'ов / fast-path checks.

## Bias-Annotation

_Куда систематически съезжает внимание практикующего._

| Bias | Direction of distortion |
|------|--------------------------|
| «Работает на текущем входе» читается как «защита не нужна» | Внимание приковано к сегодняшним данным: якоря и `\s*$` добавляют post-factum, после первого пойманного false-positive, а профилактическое применение (граница применимости: «даже когда вход чист») откладывается — защита появляется ровно на один инцидент позже, чем могла бы |
| Из трёх компонентов запоминается самый заметный — якоря `^...$` | Практик ставит `^` и `$`, но забывает `re.MULTILINE` и `\s*$`: на multi-line YAML якоря без флага работают на границах всего текста и бесполезны, а trailing whitespace после copy-paste молча ломает матч — формально regex «с якорями», фактически без защиты |

## Источник

Peer-сессия 20 «wp358-f9-status-regex-uniform» (2026-05-27), Тема 2 + review-01 H1.


---

> 2026-08-01 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 5). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
