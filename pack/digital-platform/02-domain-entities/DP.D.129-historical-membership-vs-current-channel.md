---
id: DP.D.129
type: distinction
domain: digital-platform
pack: PACK-digital-platform
trust: validated
epistemic_stage: 3
valid_from: 2026-06-05
source: WP-330 secret-rotation-marathon-migration, peer-session 16, commit 02d8b15
---

# DP.D.129 — Историческая принадлежность ≠ Текущий выбор канала

**Контекст:** bulk-миграция пользователей с одного формата (марафон) на другой (новый марафон). В исходной аудитории есть пользователи, которые **сами явно перешли** на другой канал (feed / Лента — ежедневный поток, не марафон).

**Различение:**

- **Историческая принадлежность** = когда-либо был записан на канал X. Свойство истории.
- **Текущий выбор канала** = на каком канале пользователь активно сейчас. Свойство настоящего.

**Правило default:** при scope bulk-операции — **текущий выбор приоритетнее исторической принадлежности**. Migration marathon → new-marathon **исключает** пользователей, ушедших на feed; включаются явным флагом `--include-feed` (оператор аргументирует решение).

**Анти-паттерн:** «они когда-то записывались на марафон → мигрируем всех» = нарушение пользовательского выбора, риск отписки.

**Тест применимости:** «migration target пересекается с пользователями, ушедшими на другой канал по собственному решению?» Да → exclude-by-default + explicit-opt-in flag.

**Применимо к:**
- Bulk notification re-targeting
- Format migration (марафон, серия писем, канал доставки)
- Consent-aware data operations
- Retention campaigns

**Источник:** WP-330 secret-rotation-marathon-migration, peer-session 16, commit `02d8b15` (migrate-old-marathon.py), 2026-06-05.

**Связь:** расширяет GDPR/consent-management на operational decisions — не только данные (что хранить), но и каналы доставки (куда писать).
