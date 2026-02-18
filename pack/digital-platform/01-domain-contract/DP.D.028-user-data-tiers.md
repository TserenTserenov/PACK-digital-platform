---
id: DP.D.028
name: "User Data Tiers — тирование данных пользователя"
type: distinction
status: active
summary: "Объём и тип данных пользователя растёт с каждым тиром: T1 базовый профиль → T2 полный профиль + история → T3 цифровой двойник → T4 личная база знаний"
created: 2026-02-18
edition: "2026-02"
trust:
  F: 3
  G: internal
  R: 0.9
related:
  integrates_with: [DP.ARCH.002, DP.AISYS.014, DP.D.027]
tags: [tiers, user-data, privacy, transparency, personalization]
---

# User Data Tiers — тирование данных пользователя

## Проблема

Пользователь не понимает: (1) какие данные о нём собираются, (2) зачем они нужны, (3) как влияют на его опыт, (4) как их удалить. Без прозрачности нет доверия.

## Решение: данные растут с тиром

### Секции "Мои метрики" по тирам

| Секция /mydata | T1 | T2 | T3 | T4 |
|----------------|----|----|----|----|
| Профиль | Базовый (5 полей) | Расширенный (12 полей) | Полный ЦД | Полный ЦД |
| Активность | streak + days | + sessions | + sessions | + sessions |
| Марафон | да | да | да | да |
| Лента | — | да | да | да |
| Консультации | — | count + helpful% | + DT-контекст | + DT-контекст |
| GitHub | — | заметки (OAuth) | заметки (OAuth) | заметки + git |
| AI "Почему?" | — | Haiku | Haiku + DT | Haiku + DT + personal |
| AI "Как улучшить?" | — | Haiku | Haiku + DT | Haiku + DT |
| Мои знания | — | — | — | Pack + memory/ |

### Рост данных по тирам

| Тир | Объём данных | Источники |
|-----|-------------|-----------|
| T1 (Start) | ~15 полей | Профиль (онбординг) + марафон (бесплатный) |
| T2 (Learning) | ~40 полей | + feed, Q&A, sessions, notes, подписка |
| T3 (Personalization) | ~60 полей | + DT declarative, indicators, degree/stage |
| T4 (Creation) | ~100+ полей | + personal CLAUDE.md, memory/, Pack-извлечения |

### Что попадает в промпт Claude (System Prompt Assembly)

| Тир | Контекст Claude |
|-----|----------------|
| T1 | domain_prompt + limited_profile (name, occupation, complexity) |
| T2 | domain_prompt + full_profile (+ interests, goals, role, assessment) |
| T3 | T2 + standard/CLAUDE.md + standard/memory/* + DT (degree, stage, objective, roles, problems) |
| T4 | personal/CLAUDE.md + personal/memory/* + standard/memory/* + DT (нативно через Claude Code) |

### Политика приватности

1. **Никому не передаём.** Данные не продаются и не передаются третьим лицам.
2. **Claude API (Anthropic):** при генерации контента отправляется профиль + текст вопроса. Anthropic не использует данные API для обучения.
3. **GitHub API:** только при добровольном OAuth. Только для записи заметок пользователя.
4. **Агрегация:** DAU/MAU/retention — без идентификации конкретного пользователя.
5. **Удаление:** пользователь может удалить все данные через "Удалить. {Имя}".

### Протокол удаления

Подтверждение: пользователь вводит текст "Удалить. {своё_имя}". Каскадное удаление из всех таблиц по chat_id. После удаления бот переводит в стейт start (свежий пользователь).

## Обоснование

- **GDPR Art. 15** (right of access): /mydata показывает все собранные данные
- **GDPR Art. 17** (right to erasure): каскадное удаление через подтверждение
- **AI Act direction**: прозрачность алгоритмов — показываем что попадает в промпт
- **Доверие**: политика открытости = основа долгосрочных отношений с пользователем
