---
id: DP.FM.054
type: failure-mode
name: "Linter-зелёный ≠ структура body-текста"
domain: content-pipeline
epistemic_stage: empirical
trust: verified
valid_from: 2026-05-20
source: "WP-300 Guide 2 critique (commit 191fc233)"
---

# DP.FM.054 — Linter-зелёный ≠ структура body-текста

**Паттерн:** Content-pipeline linter, проверяющий только frontmatter/metadata, выдаёт `errors=0` при наличии structural drift в body-тексте. Авторы получают ложное подтверждение целостности.

**Прецедент:** WP-300 Guide 2 (70 файлов): 28 файлов (S7–S10) используют H2-заголовки вместо bold-блоков. v4-lint `errors=0` для всех 70. Обнаружено только при ручном ревью.

**Механизм:** Linter проверяет структуру frontmatter (ключи, типы, enum-значения), но не проверяет формат первого блока body-текста. Оба формата (H2 и bold) являются валидным markdown → linter молчит.

**Последствие:** Производная работа (Guide N+1) наследует template drift умноженно (70 → 99 файлов при тех же 40% drift).

**Детектор:**
```bash
# Найти файлы с H2 в первых строках body (drift от bold-шаблона)
grep -rl "^## " path/to/guides/ | head -10
# Найти файлы с bold-блоком в первых строках body (канонический шаблон)
grep -rl "^\*\*" path/to/guides/ | wc -l
```

**Фикс:** Добавить в lint-pipeline отдельный body-structure check. `errors=0` = только frontmatter валиден, не body.

**Связи:**
- **see_also:** PD.METHOD.045 (gate перед следующим руководством)
- **see_also:** DP.FM.050 (markdown-bold-regex — смежный class в content-pipeline)
- **source_wp:** WP-300
