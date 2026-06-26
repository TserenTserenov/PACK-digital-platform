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

## Связи

- Предотвращает: DP.FM.169 (тихий fallback в content pipeline)
- Совместно: DP.METHOD.064 (gate:outcome-pending) для outcome-gated acceptance
