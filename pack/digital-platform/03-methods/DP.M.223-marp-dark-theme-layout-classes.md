---
id: DP.M.223
name: Marp тёмная тема — layout-классы для структурированных презентаций
type: method
domain: digital-platform
prefix: DP
trust: evidence-based
epistemic_stage: established
valid_from: 2026-05-29
source: DS-my-strategy/inbox/captures.md (session WP-351, 2026-05-29)
related:
  - role: DP.ROLE.060
---

# DP.M.223 — Marp тёмная тема: layout-классы

## Входы

- Marp-презентация с тёмным фоном
- CSS-тема (базовая или с нуля)

## Процесс

1. **Background fix:** `tbody td` требует явного `background: #1e293b` — без него текст невидим на тёмном фоне (наследуется прозрачный фон).
2. **Контрастные метки:** класс `.big-label` (font-size: 2em, font-weight: 800) для визуального акцента в слайдах «Было/Стало».
3. **Layout-классы через `_class:`:** разделяют semantic structure от visual style. Примеры: `hook`, `split`, `cards-2`, `cards-3`, `with-eyebrow`, `center`.
4. **Audience-check:** audience-facing slides ≠ team-facing slides. Служебные подписи/метки убираются перед публикацией.

## Выходы

- CSS-тема с layout-классами
- Marp-презентация с раздельным управлением структурой и стилем

## Инвариант

`_class:` layout = semantic structure управляется отдельно от visual CSS. Один CSS → множество layouts.

## Связи

- Роль: DP.ROLE.060 (Presentator)