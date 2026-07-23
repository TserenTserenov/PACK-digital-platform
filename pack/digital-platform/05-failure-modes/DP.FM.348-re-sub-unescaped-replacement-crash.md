---
id: DP.FM.348
name: "re.sub: пользовательский текст с backslash в replacement → crash (invalid backreference)"
type: failure-mode
domain: DP
status: draft
valid_from: 2026-07-11
created: 2026-07-22
source: "session-transcript 2026-07-11 + git diff PACK-digital-platform (docs(bugs): generate-map.py bad regex escape)"
related:
  see_also: [DP.FM.276]
tags: [python, re.sub, regex, backreference, re.escape, yaml, user-content, crash]
---

# DP.FM.348 — re.sub: пользовательский текст с backslash в replacement → crash

## Паттерн

Пользовательский текст (из YAML-поля, имени файла, описания) передаётся вторым аргументом в `re.sub(pattern, replacement, string)` без предварительного вызова `re.escape()`. Если текст содержит `\d`, `\n`, `\1` или аналогичные backslash-последовательности, Python интерпретирует их как backreference-группы — если группа не существует, генерируется `re.error: invalid group reference`.

## Пример

```python
# Поле summary из YAML-файла содержит текст с regex-паттерном:
summary = "Matches WP-\\d+ format"  # из DP.D.205

# Передача в re.sub без экранирования:
content = re.sub(pattern, summary, content)  # CRASH: \\d интерпретируется как \d → backreference

# Правильно:
content = re.sub(pattern, re.escape(summary), content)
# Или через str.replace если pattern литеральный:
content = content.replace(match_text, summary)
```

## Механизм

1. `re.sub` второй аргумент (replacement) обрабатывается как шаблон: `\N` → backreference на группу N
2. Текст из YAML содержит `\d` (часть regex-паттерна), `\n` (перенос строки), `\1` (буквально)
3. Python ищет группу с номером `d` или `n` → группы нет → `re.error: invalid group reference`
4. Ошибка возникает только при определённом содержимом YAML-поля → не воспроизводится на «обычных» данных

## Почему опасен

- Скрипт работает корректно на всех «нормальных» данных и ломается только при специфичном содержимом
- Источник данных (YAML-поле Pack) — вводится KE-пайплайном, не пользователем напрямую
- Сообщение об ошибке указывает на `re.sub` в скрипте, не на поле YAML → диагностика нетривиальна

## Лечение

1. Любой пользовательский/файловый текст перед `re.sub` — `re.escape()`
2. Альтернатива: `str.replace` если pattern нужно заменить как литерал
3. Unit-тест: summary с `\d`, `\n`, `\1` → проверить, что скрипт не падает

## Связи

- DP.FM.276 (LLM-output regex punctuation variance) — смежный класс: пользовательский контент ломает regex-ожидания; другой механизм
