---
id: DP.FM.291
name: "Промпт/скрипт не мигрирован в service-репо при переезде протокола — тихая развилка версий"
type: failure-mode
domain: DP
status: active
valid_from: 2026-07-14
source: "commit 0f18ee297 (DS-my-strategy, docs(bug): session-close-feed prompt не мигрирован в DS-ai-systems); session-close 2026-07-10"
related:
  see_also:
    - "DP.FM.263: git-copy-instead-of-move (смежно: cross-repo; другое: операция, не семантика)"
    - "DP.FM.290: Apple Health multiple rows (смежный pattern тихой ошибки без сигнала)"
tags: [cross-repo, deployment, migration, prompt, protocol, silent-divergence, governance-vs-service]
---

# DP.FM.291 — Промпт не мигрирован в service-репо: тихая развилка версий

## Паттерн

Протокол/промпт разработан или обновлён в governance-репо (DS-my-strategy, DS-ai-systems/extractor/), но не перенесён в service-репо (DS-ai-systems production runner). Production-runner продолжает работать с устаревшей версией. Автор правит governance, не замечая расхождения — нет явного сигнала об ошибке.

## Диагностика

**Тест:** `diff <governance-prompts-dir> <service-prompts-dir>` — при ненулевом diff = протокол расходится.

Симптом обнаружения: поведение production-runner не соответствует ожидаемому по governance-документу, хотя governance обновлён.

## Корень

При разработке протокола в governance-репо не фиксируются целевые точки деплоя. «Написал промпт» ≠ «задеплоил промпт». Два репо — два независимых состояния без автоматической синхронизации.

## Профилактика

**Правило:** при создании нового протокола/скилла — в том же PR/коммите зафиксировать:
- `target_repos: [DS-ai-systems/extractor/prompts/, ...]` в frontmatter файла
- задача «перенести в service-repo» как явный шаг, а не follow-up

**Тест:** «скилл/промпт обновлён — кто ещё его читает и из какого репо?» → каждый из этих репо должен получить обновление.

## Отличия от смежных FM

| FM | Суть | Симптом |
|----|------|---------|
| DP.FM.291 (этот) | Семантическое несоответствие: governance обновлён, service — нет | Правильное поведение по документу, неправильное в проде |
| DP.FM.263 | Операционная ошибка: скопировал вместо переместил | Файлы в двух местах одновременно |

## Аналог

Аналог правила «правило в памяти ≠ правило закреплено в коде инструмента» (уже в captures), но на уровне cross-repo deployment, а не внутри одного репо.
