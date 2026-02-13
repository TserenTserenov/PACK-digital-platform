---
id: DP.SOTA.008
name: Real-Time Knowledge Capture
type: sota
status: active
summary: "Capture during work, not after: знания фиксируются в момент обнаружения, а не ретроспективно — консенсус KM 2026"
created: 2026-02-13
edition: "2026-02"
trust:
  F: 4
  G: external
  R: 0.7
related:
  integrates_with: [DP.SOTA.002, DP.SOTA.007]
  operationalized_by: [DP.M.001]
  enables: [DP.D.016]
tags: [knowledge-management, capture, real-time, km-2026, capture-to-pack]
---

# Real-Time Knowledge Capture

## 1. Определение

**Real-Time Knowledge Capture** — практика фиксации знаний в момент их обнаружения (during work), а не ретроспективно (after work). Индустриальный консенсус KM 2026: «AI does not replace knowledge management — it exposes whether you have it.»

## 2. Статус SOTA (февраль 2026)

**SOTA, industry consensus.**

- **Fire Oak Strategies (2026):** «Capture knowledge during work, not after it. AI copilots and agents are only as effective as the knowledge environment they sit on top of.»
- **Key insight:** Организации с плохим KM не получают пользы от AI. AI усиливает существующее знание, не создаёт его.
- **Сдвиг парадигмы:** от «documentation sprints» к «continuous capture» встроенному в рабочий процесс.

## 3. SPF-интеграция

Наш Capture-to-Pack протокол — **прямая реализация** этой SOTA-практики:

| KM 2026 принцип | SPF/Экзокортекс реализация |
|-----------------|---------------------------|
| Capture during work | Протокол Work: capture на каждом рубеже |
| Continuous, not batch | Анонс «Capture: X → Y» по ходу сессии |
| Embedded in workflow | WP Gate → Ritual → Work (capture) → Close (extraction) |
| AI-assisted | Knowledge Extractor (DP.AISYS.013) формализует captures |

## 4. Различение

**Capture ≠ Extraction** (DP.D.016):
- Capture = обнаружить кандидата на знание → анонсировать
- Extraction = превратить кандидата в формализованную сущность с ID, frontmatter, валидацией

Без Capture нечего экстрагировать. Без Extraction capture остаётся заметкой.

## 5. Правила применения

1. **На каждом рубеже работы** — проверять: есть ли знание для записи? (триггеры: завершена подзадача, обнаружен паттерн, принято решение).
2. **Не откладывать capture** — знание утрачивается exponentially со временем после обнаружения.
3. **Мелкое (1-3 строки)** → записать сразу в CLAUDE.md/memory. Крупное → анонсировать, отложить до Close → KE.
4. **Тест универсальности:** можно ли использовать в другом проекте? Да → capture. Нет → просто заметка.

## 6. Источники

- Fire Oak Strategies. «Knowledge Management in 2026» (2026)
- APQC. «KM Best Practices 2025-2026» (2025)
- Наш протокол: CLAUDE.md § 2 (Capture-to-Pack)
