---
id: DP.M.153
name: Scaffold Fallback — Minimal Valid Document (не пустой файл)
type: method
status: draft
valid_from: 2026-05-22
summary: "Else-ветка guard-блока в cascaded scaffold-системе создаёт минимальный валидный документ (frontmatter + комментарий generated_by: fallback), а не пустой файл через touch. Downstream-парсеры получают рабочую оболочку, а не падают на отсутствующем YAML-блоке."
related:
  prevents: [DP.FM.038]
  see_also: [DP.M.018, DP.M.024]
tags: [scaffold, fallback, frontmatter, pipeline, defensive-design]
source: "FMT-exocortex-template commit 9dfbfaa — fix в day-plan.md guard-else (2026-05-22)"
schema_version: 1
---

# DP.M.153 — Scaffold Fallback — Minimal Valid Document

## Суть метода

В cascaded scaffold-системах (DayPlan, WP-context, Extraction Report и пр.) первый шаг создаёт «оболочку», которую заполняют downstream-шаги (LLM-извлекатели, frontmatter-readers, парсеры метаданных). Если основной scaffold-скрипт недоступен (отсутствует, упал, не имеет прав), для else-ветки guard'а нужен fallback.

**Антипаттерн:** `touch $TARGET` создаёт пустой файл. Downstream-парсеры падают на «нет YAML-блока» или возвращают пустые значения.

**Корректный fallback:** heredoc с минимальным валидным frontmatter:

```yaml
---
type: day-plan
date: 2026-05-22
status: scaffold
agent: claude
generated_by: fallback
---

# DayPlan 2026-05-22

(заполните вручную или повторно вызовите scaffold)
```

## Тест применимости

«Упадёт ли следующий шаг pipeline на этом fallback-файле?» Если да — fallback недостаточен. Минимальный набор полей frontmatter определяется требованиями downstream-парсеров, не общими соглашениями.

## Когда применять

- Cascaded scaffold-системы с двумя или более этапами создания/наполнения документа.
- Любой guard-else, где альтернативой является «упасть» или «создать пустой файл».
- Системы, где LLM/пользователь дополняет оставшиеся поля документа после scaffold.

## Когда НЕ применять

- Однократные scaffold-скрипты без downstream-парсеров (тогда fail-loud предпочтительнее silent-fallback).
- Системы, где наличие пустого файла — намеренный сигнал (touch как marker).

## Ссылки

- Источник: FMT-exocortex-template commit 9dfbfaa
- Предотвращает: DP.FM.038 (Silent-Pass Validator on Missing Input) — расширение принципа «не отдавай пустоту даунстриму» с валидаторов на scaffold-генераторы
