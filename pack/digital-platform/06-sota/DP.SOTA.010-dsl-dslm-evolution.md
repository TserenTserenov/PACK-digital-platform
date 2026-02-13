---
id: DP.SOTA.010
name: DSL → DSLM Evolution
type: sota
status: active
summary: "Бифуркация DSL: классические domain-specific languages стабильны, фронтир ушёл в Domain-Specific Language Models (DSLM)"
created: 2026-02-13
edition: "2026-02"
trust:
  F: 3
  G: external
  R: 0.6
related:
  integrates_with: [DP.SOTA.001, DP.SOTA.007]
  enables: [DP.D.019]
tags: [dsl, dslm, domain-specific, language-models, fine-tuning, enterprise]
---

# DSL → DSLM Evolution

## 1. Определение

**DSL (Domain-Specific Languages)** — языки, ограниченные конкретным доменом (SQL, Terraform, GraphQL). **DSLM (Domain-Specific Language Models)** — LLM, fine-tuned на доменном корпусе, понимающие специфику области без явного программирования DSL.

Бифуркация: классические DSL остаются полезными для конфигурации и infrastructure-as-code, но фронтир энергии сместился в DSLM.

## 2. Статус SOTA (февраль 2026)

**Evolving, bifurcating.**

- **Классические DSL:** стабильны, не фронтир. SQL, Terraform, HCL, Markdown — устоявшиеся инструменты.
- **DSLM (2026):** 68% manufacturers ожидают measurable efficiency gains от DSLM (Cogent/Gartner). Сдвиг от «one-model-fits-all» к специализированным моделям.
- **Для SPF:** принцип DSL (вторые принципы → машинно-проверяемые) сохраняет ценность. Инструментарий меняется.

## 3. SPF-интеграция

| DSL-принцип | SPF-реализация |
|-------------|---------------|
| Формализация доменных правил | Pack entity frontmatter = domain-specific schema |
| Machine-readable constraints | Typed `related:`, summary ≤150 chars, F-G-R trust |
| Validation | generate-map.py checks, KE validation step |
| Domain vocabulary | Ubiquitous Language через ontology.md |

## 4. Правила применения

1. **Не создавать полноценный DSL** для Pack — frontmatter YAML + markdown достаточны.
2. **Использовать DSL-принцип:** вторые принципы должны быть machine-checkable (автотесты, validators).
3. **При масштабировании** — рассмотреть DSLM: fine-tuned модель на корпусе Pack для более точного retrieval.
4. **Различать DSL и DSLM** (DP.D.019): DSL = явный язык, DSLM = implicit language в weights модели.

## 5. Источники

- Cogent/Gartner. «DSLMs: End of General-Purpose LLM Hype» (2026)
- Fowler M. «Domain-Specific Languages» (Addison-Wesley, 2010) — классика
