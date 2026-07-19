---
id: DP.D.016
name: "Capture-to-Pack ≠ Knowledge Extraction"
type: distinction
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-02-10
created: 2026-07-19
source: "миграция из 01B-distinctions.md (РП170 Ф2, 2026-07-19)"
see_also: []
schema_version: 1
---

# DP.D.016: Capture-to-Pack ≠ Knowledge Extraction

| Capture-to-Pack | Knowledge Extraction |
|-----------------|---------------------|
| Примитив: «заметил → анонсировал → отложил/записал» | Полный pipeline: detect → classify → route → formalize → validate |
| Выполняется человеком или ИИ-ассистентом по ходу работы | Выполняется Knowledge Extractor (AISYS.013) |
| Вход: наблюдение в сессии | Вход: capture, документ, Pack-diff |
| Выход: анонс «Capture: X → Y» | Выход: формализованный файл в Pack с frontmatter и ID |
| Не требует проверки противоречий | Обязательна проверка противоречий |
| Часть протокола Work (CLAUDE.md § 2) | Часть протокола Close + отдельные сценарии |

**Почему важно**: Capture — это обнаружение кандидата на знание. Extraction — это превращение кандидата в формализованное знание. Без Capture нечего экстрагировать. Без Extraction capture остаётся заметкой.

**Тест**: Есть ли у результата ID, frontmatter, проверка противоречий и размещение в Pack? Да → Knowledge Extraction. Нет → Capture-to-Pack.
