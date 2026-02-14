---
id: DP.SOTA.011
name: Coupling Model (Khononov 2024)
type: sota
status: active
summary: "Многомерная модель связанности: knowledge coupling, distance coupling, volatility coupling — 4 уровня интеграции"
created: 2026-02-13
edition: "2026-02"
trust:
  F: 3
  G: external
  R: 0.6
related:
  integrates_with: [DP.SOTA.001, DP.SOTA.006]
  extends: [DP.ARCH.001]
tags: [coupling, khononov, architecture, integration, knowledge, distance, volatility]
---

# Coupling Model (Khononov 2024)

## 1. Определение

**Coupling Model** — многомерная модель оценки связанности между компонентами системы по трём измерениям: knowledge coupling (сколько нужно знать о другом компоненте), distance coupling (физическое/логическое расстояние), volatility coupling (как часто меняется контракт). Определяет 4 уровня интеграции от intrusive до message-based.

## 2. Статус SOTA (февраль 2026)

**SOTA, новейшее теоретическое расширение DDD.**

- **Khononov V. «Balancing Coupling in Software Design» (Pearson, 2024)** — фундаментальная работа.
- **SE Radio 662 (April 2025):** подробное интервью о модели.
- **NDC Oslo 2025:** keynote presentation.
- Расширяет стратегический DDD: не просто «границы контекстов», а количественная оценка связей.

## 3. Три измерения coupling

| Измерение | Вопрос | Пример в экзокортексе |
|-----------|--------|----------------------|
| **Knowledge** | Сколько А знает о внутренностях B? | Бот знает API синхронизатора, не его реализацию |
| **Distance** | Насколько далеко A от B? (процесс, сервис, сеть) | Claude Code (локально) ↔ knowledge-mcp (Cloudflare) |
| **Volatility** | Как часто меняется контракт между A и B? | Pack entities (стабильные) vs bot UI (volatile) |

## 4. 4 уровня интеграции

| Уровень | Описание | Coupling | Пример |
|---------|----------|---------|--------|
| **Intrusive** | Прямой доступ к internal state | Максимальный | (антипаттерн) |
| **Functional** | Вызов функции/API | Высокий | MCP tool calls |
| **Model** | Общая модель данных | Средний | Pack frontmatter schema |
| **Message** | Только сообщения/события | Минимальный | TG notifications, webhooks |

## 5. SPF-интеграция

| SPF-механизм | Coupling-аспект |
|-------------|-----------------|
| Boundary Statements | Определяют knowledge coupling — что видно снаружи |
| typed `related:` | Описывают distance coupling — как далеко связанные сущности |
| Pack (source-of-truth) vs Downstream | Volatility: Pack стабилен, Downstream — проекция (может меняться) |
| 5 характеристик (ЭМОГС) | Coupling model = инструмент оценки эволюционируемости |

## 6. Правила применения

1. **При архитектурном решении** — оценивать coupling по 3 измерениям, не только «связано/не связано».
2. **Стремиться к message-level coupling** между системами (TG, webhooks, events).
3. **Knowledge coupling = главный враг эволюционируемости.** Если A знает внутренности B → изменение B ломает A.
4. **Pack → Downstream = model-level coupling** (общий frontmatter schema). Это оптимальный баланс.

## 7. Источники

- Khononov V. «Balancing Coupling in Software Design» (Pearson, 2024)
- SE Radio 662: Khononov on Coupling (April 2025)
- NDC Oslo 2025: Keynote
