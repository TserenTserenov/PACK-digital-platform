---
id: DP.M.033
name: Matrix-CI по конфигурационному параметру шаблона
name_en: Matrix CI for Template Parameter Testing
type: method
status: active
summary: "CI-пайплайн для шаблонов запускается с матрицей значений ключевого конфигурационного параметра. Немедленно выявляет hardcoded константы, которые не проявляются у автора с дефолтным именем."
created: 2026-05-12
valid_from: 2026-05-12
trust:
  F: 3
  G: domain
  R: 0.85
epistemic_stage: evidence
related:
  see_also: [DP.FM.029]
tags: [testing, ci, template, matrix, automation, FMT, cross-platform, hardcoded]
wp: WP-5
---

# Matrix-CI по конфигурационному параметру шаблона (DP.M.033)

## Суть метода

Для шаблонов (FMT) с конфигурационными параметрами-заменителями: запускать CI не с одним значением, а с **матрицей** значений ключевого параметра.

**Пример (WP-5 Ф-CI, 2026-05-12):**

```yaml
strategy:
  matrix:
    governance_repo: [DS-strategy, DS-my-governance, DS-governance]
```

Тест выявил два hardcoded `"DS-strategy"` в `dt-collect.sh` и `repair-pass` в `update.sh` — баги, незаметные у автора (у него репо = DS-strategy = совпадает с дефолтом).

## Алгоритм

1. Определить ключевые параметры шаблона: `GOVERNANCE_REPO`, `WORKSPACE_DIR`, `USER_NAME`, `PACK_REPO`
2. Для каждого параметра добавить ≥2 нестандартных значения в matrix
3. CI должен проходить со всеми значениями матрицы
4. При failure: `grep -r "<hardcoded_value>" .` — найти и заменить на `params.yaml`-переменную

## Применимость

- FMT (шаблоны экзокортекса) с `{{PLACEHOLDER}}` → конкретное значение при инсталляции
- Dockerfile / Helm-чарты с параметрами `namespace`, `prefix`, `org`
- Bash-скрипты с заменяемыми конфигурационными значениями

## Связь с DP.FM.029

DP.FM.029 (Cross-Platform Path Leak) и DP.M.033 — дополняют друг друга: matrix-CI = статическая проверка параметров при сборке; smoke-test guard = runtime-защита от env-контаминации.
