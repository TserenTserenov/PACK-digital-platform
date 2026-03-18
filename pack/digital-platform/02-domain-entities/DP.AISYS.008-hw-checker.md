---
id: DP.AISYS.008
name: ДЗ-чекер
type: ai-system-passport
status: active
summary: "ИИ-система автоматической проверки домашних заданий учеников по нормативам из руководств"
created: 2026-03-02
trust:
  F: 3
  G: domain
  R: 0.5
epistemic_stage: emerging
orientation: system
initiative: by-request
interface: api
related:
  uses: [DP.ROLE.001]
  part_of: [DP.AISYS.014]
  realizes: [DP.SC.003, DP.SC.117]
---

# ДЗ-чекер (HW Checker)

> **Implementation Note.** §§1-3 (определение, классификация, IPO) — домен. §§4-6 (n8n, Claude Haiku, SurrealDB, OpenAI, guides-mcp) — текущая реализация. Детали: [C2.IT-Platform / System-Implementations](../../../DS-ecosystem-development/C.IT-Platform/C2.IT-Platform/C2.2.Architecture/System-Implementations/).

## 1. Определение

**ДЗ-чекер** — ИИ-система, которая автоматически проверяет ответы учеников на домашние задания, сравнивая их с нормативами из руководств через семантический поиск.

> Отличие от Оценщика (evaluator): Оценщик проверяет тренировочные ответы в боте по Bloom-рубрикам. ДЗ-чекер проверяет ДЗ в LMS Aisystant по нормативам из руководств. TODO: объединить в единую систему.

## 2. Классификация

| Характеристика | Значение | Пояснение |
|----------------|----------|-----------|
| **Ориентация** | system | Вызывается из LMS Aisystant, не прямой диалог с человеком |
| **Инициатива** | by-request | Запускается при отправке ДЗ учеником |
| **Интерфейс** | api | Webhook POST /check |
| **Grade** | 2 | LLM + tool use (semantic_search), решения в рамках |

## 3. IPO-паттерн

### 3.1. Входы (Inputs)

| Вход | Источник | Обязательный |
|------|----------|-------------|
| `question_text` | LMS Aisystant | Да |
| `answer_text` | LMS Aisystant | Да |
| `course_name` | LMS Aisystant | Нет |
| `section_name` | LMS Aisystant | Нет |

### 3.2. Обработка (Process)

1. Валидация входных полей
2. Формирование промпта с инструкцией для AI Agent
3. AI Agent (Claude Haiku 4.5) вызывает `semantic_search` по руководствам
4. Сравнение ответа ученика с найденным нормативом по критериям:
   - Полнота (все ли ключевые идеи из норматива отражены)
   - Корректность (нет ли противоречий с нормативом)
   - Терминология (используется ли терминология из норматива)
   - Структура (логичен ли ответ)
5. Генерация JSON-вердикта с обратной связью

### 3.3. Выходы (Outputs)

| Выход | Получатель | Формат |
|-------|-----------|--------|
| `verdict` | LMS Aisystant | `accepted` / `needs_revision` / `rejected` |
| `score` | LMS Aisystant | 0-100 |
| `strengths` | LMS Aisystant | string[] |
| `issues` | LMS Aisystant | `[{criterion, issue, suggestion}]` |
| `comment` | LMS Aisystant | HTML-formatted для отображения ученику |

## 4. Архитектура развёртывания (implementation)

| Компонент | Технология | Владелец |
|-----------|-----------|----------|
| Workflow | n8n (hw-checker-v2) | Ильшат Габдуллин |
| LLM | Claude Haiku 4.5 (Anthropic API) | Anthropic |
| Семантический поиск | guides-mcp (Cloudflare Worker) | DS-MCP |
| Vector store | SurrealDB | DS-MCP |
| Embeddings | OpenAI text-embedding-3-small | OpenAI |

## 5. Зависимости (implementation)

- **guides-mcp** (`https://guides-mcp.aisystant.workers.dev/mcp`) — MCP-сервер для доступа к руководствам. Исходники: `DS-MCP/guides-mcp/`.
- **SurrealDB** — хранилище чанков руководств с векторными эмбеддингами.
- **OpenAI Embeddings** — генерация векторов запросов для семантического поиска.

## 6. Описание системы (DS)

Подробное техническое описание: [DS-ai-systems/hw-checker/](../../../DS-IT-systems/DS-ai-systems/hw-checker/)

---

*Создан: 2026-03-02*
