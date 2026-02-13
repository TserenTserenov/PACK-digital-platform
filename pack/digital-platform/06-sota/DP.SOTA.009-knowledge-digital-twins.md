---
id: DP.SOTA.009
name: Knowledge-Based Digital Twins
type: sota
status: active
summary: "Персональные/enterprise knowledge graphs как digital twin: реплицируют перспективу, знания и предпочтения владельца"
created: 2026-02-13
edition: "2026-02"
trust:
  F: 3
  G: external
  R: 0.5
related:
  integrates_with: [DP.SOTA.004, DP.SOTA.002]
  enables: [DP.CONCEPT.001, DP.EXOCORTEX.001]
tags: [digital-twin, knowledge-graph, personal-ai, pkm, fused-knowledge-base]
---

# Knowledge-Based Digital Twins

## 1. Определение

**Knowledge-Based Digital Twins** — модели, объединяющие доменное знание (ontology, knowledge graph) с данными реального объекта (индикаторы, предпочтения, история) для создания «двойника», способного отвечать от лица владельца и принимать решения в его контексте.

## 2. Статус SOTA (февраль 2026)

**Emerging, pre-SOTA → быстро консолидируется.**

- **Personal.ai (2025-2026):** AI digital twins для personal knowledge management — реплицируют индивидуальную перспективу.
- **Lenovo Qira (CES 2026):** «Fused Knowledge Base» — digital twin жизни пользователя (cross-device, ambient AI).
- **Enterprise:** knowledge-enhanced digital twins для actionable intelligence в производстве и логистике.

## 3. SPF-интеграция

Наш цифровой двойник — ранняя реализация этого тренда:

| KBDT-компонент | Экзокортекс-аналог |
|----------------|-------------------|
| Knowledge Base | Pack (source-of-truth) |
| Personal Model | PACK-personal (индикаторы созидателя) |
| Inference Engine | ИИ-агенты (Стратег, Экстрактор, Проводник) |
| Interface | Бот (DP.AISYS.014), MCP-серверы |
| Memory | CLAUDE.md + memory/ (3-слойная архитектура) |

## 4. Правила применения

1. **Digital twin = Pack + данные + агенты**, не просто база данных.
2. **Разделять модель и данные** (DP.D.002): модель (Pack) версионируется, данные (индикаторы) накапливаются.
3. **Проекция, не копирование:** downstream показывает view двойника, не дублирует Pack.
4. **При проектировании DDT** — определить: что в platform-space (общее для всех), что в user-space (персональное)?

## 5. Источники

- World Scientific. «Knowledge-Enhanced Digital Twin Systems» (2026)
- Personal.ai. «AI Digital Twins: Future of PKM» (2025)
- Lenovo Qira. «Fused Knowledge Base» (CES 2026)
