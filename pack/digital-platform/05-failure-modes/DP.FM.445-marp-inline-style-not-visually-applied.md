---
id: DP.FM.445
name: "Inline style на обёрточном div в marp-слайде: DOM верный, визуально не применяется"
type: failure-mode
domain: digital-platform
pack: PACK-digital-platform
trust: observed
status: draft
valid_from: 2026-09-12
source: "session-transcript 2026-09-12 (подготовка presentation-слайдов, DS-ecosystem-development); упомянуто также в personal memory lessons_marp_scoped_style_over_inline_div.md (в Pack отсутствовало на момент этой записи)"
related:
  see_also: [DP.M.223]
tags: [marp, css, rendering, presentation, foreignObject, chromium]
schema_version: 1
---

# DP.FM.445 — Inline style на обёрточном div в marp-слайде: DOM верный, визуально не применяется

## Паттерн

Marp-cli (рендер через headless Chromium) собирает корректный DOM для инлайновых стилей на произвольных обёрточных элементах (`<div style="display:flex">`, `<div style="font-size:...">`), но визуально эти стили не применяются: колонки не выстраиваются во flex, размер шрифта не меняется, текст может «убегать» за край слайда. Симптом воспроизведён дважды в одной сессии на двух разных CSS-свойствах (layout и типографика) — один класс дефекта, не два случайных бага.

## Механизм

1. Marp рендерит слайд через SVG `<foreignObject>`, а не как обычную HTML-страницу в браузере.
2. Инлайновые стили на обёрточных элементах внутри `foreignObject` ненадёжны: DOM после сборки синтаксически и семантически корректен (проверяемо дампом HTML), но каскад не применяется визуально при растеризации.
3. Верификация правки только дампом HTML даёт ложное «готово» — дефект виден исключительно в отрендеренном PDF/изображении.

## Почему опасен

Стандартная интуиция «инлайн-стиль сильнее внешнего CSS» здесь не работает и не диагностируется чтением сгенерированного HTML — нужен визуальный/растровый тест.

## Correct Alternative

- `<style scoped>` внутри слайда — официальный механизм Marp: CSS ограничен секцией слайда, гарантированно применяется.
- Встроенный синтаксис `![bg fit right:N%](img)` для раскладки «текст + картинка» вместо ручного flex через inline-div.

## Тест

«Стиль на обёрточном `<div>` внутри marp-слайда — inline `style=` или `<style scoped>`/встроенный синтаксис?» Inline на обёртке → под подозрением, проверять визуально (не по дампу DOM), предпочесть `<style scoped>`.

## Связи

- **DP.M.223** (marp тёмная тема, layout-классы через `_class:`) — тот же инструмент и тот же принцип «использовать встроенные механизмы Marp вместо ad-hoc inline/CSS», другой конкретный дефект (background у `tbody td`, семантические layout-классы, не foreignObject/inline-style unreliability). Companion, не дубликат.
