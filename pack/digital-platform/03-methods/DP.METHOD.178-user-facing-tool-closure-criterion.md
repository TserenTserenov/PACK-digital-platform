---
id: DP.METHOD.178
name: User-facing tool closure requires live acceptance test
type: method
status: active
scope: WP closure, user-acceptance verification
severity: high
introduced_date: 2026-07-10
valid_from: 2026-07-16
---

# DP.METHOD.178: User-facing tool closure requires live acceptance test

## Назначение

Установить разграничение: automated smoke tests (unit + dry-run + CI) НЕ РАВНЫ user acceptance test. Closure user-facing artifact требует явного живого прогона с участием целевого пользователя.

## Алгоритм

### Closure criteria (трёхслойная иерархия)

1. **Automated smoke** ✓
   - Unit tests зелёные
   - CI/linter прошёл
   - Dry-run на synthetic data прошёл

2. **Manual verification** ✓
   - Разработчик проверил основной сценарий
   - Граничные случаи протестированы

3. **User acceptance test** ✓ (обязателен для user-facing tools)
   - Целевой пользователь выполнил реальный рабочий сценарий
   - На реальных данных, в реальном контексте пользователя
   - Замечания и правки учтены

### Без шага (3) фаза closure преждевременна

Закрытие РП без user acceptance test создаёт обманчивое впечатление готовности. Интеграционные проблемы, workflow-specific issues, UX-friction обнаруживаются только при живом использовании.

## Примеры

**WP-474 (Pack-creation skill):** dry-run и autotests прошли (7 дефектов найдено, исправлено, CI зелёный). Но живого прогона `/pack-new` с пилотом не было → Ф7 остаётся открытой.

## Применимость

- Все интерактивные инструменты (CLI commands, skills, bots)
- Все workflows, затрагивающие user data
- Все customer/stakeholder-facing features

Исключения (когда acceptance не требуется):
- Внутренние библиотеки без user-facing interface
- Pure refactoring (нет изменения поведения)
- Infrastructure-only changes

## Связанные сущности

- WP Gate (п.1: разграничение артефактов по классу верификации)
- Различение «Почти готово ≠ Готово» (01B-distinctions)

## Антипаттерн

Полагаться на "если CI зелёный, значит ready" для user-facing artifact. CI проверяет внутреннюю корректность, не user experience и не интеграционную готовность.
