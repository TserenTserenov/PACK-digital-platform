---
id: DP.METHOD.065
type: method
domain: DP
status: draft
summary: "Verifier-before-assembly: explicit source availability check before content generation. Returns missing_source:<name> flags instead of silently falling back to defaults."
created: 2026-06-24
valid_from: 2026-06-24
version: v1.0
source: "session-transcript 2026-06-24 peer-02 WP-149; verify_rung_sources() implementation"
related:
  see_also: [DP.METHOD.064, DP.FM.169]
tags: [content-pipeline, multi-source, explicit-error, anti-silent-fallback]
---

# DP.METHOD.065: Verifier-before-assembly

## Назначение

Паттерн для multi-source content generators: явно проверить наличие каждого нужного источника ДО начала сборки. При отсутствии — выдать явный флаг (`missing_source:<name>`), не тихий fallback к дефолту.

Предотвращает DP.FM.169: acceptance-тест видит «файл создан» (зелёный), но содержание деградировало незаметно.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Явная блокировка сборки (`raise SourceMissingError`) ↔ непрерывность content pipeline | Явный отказ при `missing_source` делает деградацию видимой сразу, но останавливает выдачу контента там, где тихий fallback к `DEFAULT_DATA` продолжил бы pipeline — именно эта непрерывность и создаёт незаметный green (DP.FM.169), который метод устраняет ценой доступности |
| Полнота проверки (`programs_with_weight_gt_0`) ↔ точность метаданных весов | `verify_sources()` проверяет наличие источника только для программ с весом > 0 — полнота проверки прямо зависит от корректности отдельного поля `weight`; ошибочно заниженный вес молча выводит нужный источник из-под проверки |

## Алгоритм

```python
# было: тихий fallback — дефект невидим
def build_content(rung, sources):
    data = sources.get(rung.program) or DEFAULT_DATA  # silent fallback
    return render(data)

# стало: verifier-before-assembly
def verify_sources(rung, sources) -> list[str]:
    missing = []
    for prog in rung.programs_with_weight_gt_0():
        if prog not in sources:
            missing.append(f"missing_source:{prog}")
    return missing

def build_content(rung, sources):
    errors = verify_sources(rung, sources)
    if errors:
        raise SourceMissingError(errors)   # или WARNING + log — но явно
    data = sources[rung.program]
    return render(data)
```

## Применять когда

- Content pipeline работает с N источниками с разными весами
- Отсутствие источника снижает качество, но технически не ломает pipeline
- Acceptance-criterion может пропустить деградацию (проверяет наличие файла, не его соответствие)

## Bias-Annotation

_Куда систематически съезжает внимание практикующего. Статус карточки — `draft`: пометка `tentative`._

| Bias | Direction of distortion |
|------|--------------------------|
| _(tentative)_ Полировка fallback вместо его устранения | При переписывании `build_content()` внимание тянется назад к улучшению `DEFAULT_DATA` (сделать заглушку правдоподобнее), а не к вынесению `missing_source`-флага наружу — привычка «сгладить деградацию» подменяет цель метода «сделать её видимой» |
| _(tentative)_ Флаг сгенерирован — задача закрыта | Внимание останавливается на факте, что `missing_source:<name>` возвращается функцией `verify_sources()`, и не доходит до проверки, действительно ли этот флаг долетает до acceptance-теста или человека — тот же false-green, который метод должен предотвращать, может воспроизвестись на уровне самой проверки |

## Связи

- Предотвращает: DP.FM.169 (тихий fallback в content pipeline)
- Совместно: DP.METHOD.064 (gate:outcome-pending) для outcome-gated acceptance

---

> 2026-07-27 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 3). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
