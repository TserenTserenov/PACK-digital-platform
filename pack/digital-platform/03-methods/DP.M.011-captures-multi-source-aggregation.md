---
id: DP.M.011
name: Агрегация captures из множества источников
type: method
status: draft
summary: "Единый inbox-файл (captures.md) наполняется автоматически из 4 каналов с маркерами источника для идемпотентной обработки Экстрактором"
created: 2026-05-08
trust:
  F: 3
  G: domain
  R: 0.5
epistemic_stage: emerging
related:
  operationalizes: [DP.M.001]
  references: [WP-247]
---

# Агрегация captures из множества источников

## 1. Определение

**Multi-source captures aggregation** — архитектурный паттерн, при котором единый inbox-файл (`captures.md`) выступает точкой агрегации для captures из 4 каналов. Каждый feeder добавляет маркер источника `[feed:SOURCE YYYY-MM-DD]` в заголовок записи для идемпотентной обработки.

> Принцип: один файл — одна точка входа. R2 читает `captures.md`, не обходит N каналов по отдельности.

## 2. Проблема

При раздельном хранении captures по источникам Экстрактор (R2) вынужден читать N файлов и объединять контекст вручную. Это создаёт риск пропуска или двойной обработки одной записи.

## 3. Четыре канала

| Канал | Маркер | Источник |
|-------|--------|----------|
| Ручной (пользователь) | `[feed:manual YYYY-MM-DD]` | Пользователь напрямую в captures.md |
| Закрытие сессии | `[feed:session-close YYYY-MM-DD]` | Протокол Close, git diff |
| Git diff (автоматический) | `[feed:git-diff YYYY-MM-DD]` | Ночной агент / scout |
| Fleeting notes | `[feed:fleeting YYYY-MM-DD]` | Note-Review из fleeting-notes.md |

## 4. Инварианты

- Все feeders идемпотентны: повторная вставка одного capture не создаёт дубликат (проверка по заголовку)
- Маркер источника позволяет R15 при `/apply-captures` пометить правильный исходник маркером `[analyzed]`
- R2 читает **один файл** — принцип единого входа соблюдается

## 5. Связи

- Реализует: DP.M.001 (Извлечение знаний) — sub-метод этапа «Intake»
- Операционализируется: DP.AISYS.013 (Inbox-Check Service Clause)
- Контекст: WP-247 Ф-MULTI-SOURCE.1-5 (решение 2026-05-08)

## 6. Routing-правило: session-internal vs external-source

Captures приходят из двух классов источников. Канал доставки и инвариант «один файл» применяются по-разному.

| Класс источника | Происхождение | Канал | Артефакт-приёмник |
|-----------------|---------------|-------|-------------------|
| **Session-internal** | Claude писал в сессии (transcript, git diff) | один из 4 feeders §3 | `inbox/captures.md` (###-блок) |
| **External-source** | Claude читал внешний артефакт (видео, PDF, статья, семинар, разговор) | прямая запись | `inbox/extraction-reports/<date>-<source>.md` напрямую |

### 6.1. Тест классификации

«Можно ли извлечь paragraph кандидата из git log session-периода?»

- **Да** → session-internal → captures.md.
- **Нет** (источник вне git, медиа, разговор, внешний документ) → external-source → extraction-report напрямую.

### 6.2. Почему external-source минует captures.md

- Внешний источник обычно даёт десятки кандидатов сразу → раздувание captures.md (1500+ строк) без структуры.
- Метаданные источника (file path, время записи, автор, контекст) теряются как ###-блоки.
- Verdict-механизм (`pending-review` / `accepted` / `rejected` / `deferred`) встроен во frontmatter extraction-report, не воспроизводится в captures.md.

### 6.3. Унификация downstream-pipeline

Оба маршрута сходятся в R15 (`/apply-captures`):
- session-internal: captures.md → inbox-check → extraction-report → apply.
- external-source: extraction-report напрямую → apply.

R15 не различает источник на этапе applying — frontmatter `source: ...` сохраняет провенанс.

### 6.4. Пример

WP-242 Ф-Семинар-Агроскина (17 мая 2026): транскрипт `FinanceSeminar160526.mp4.txt` (external) → `inbox/extraction-reports/2026-05-17-agroskin-seminar.md` с 13 кандидатами + frontmatter `source: external-seminar`, `source_files: [...]`. Не fed через captures.md.
