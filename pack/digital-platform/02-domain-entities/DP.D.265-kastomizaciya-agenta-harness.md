---
id: DP.D.265
name: "Кастомизация агента (Harness Engineering) ≠ Дообучение (Fine-Tuning)"
type: distinction
pack: PACK-digital-platform
domain: digital-platform
trust: confirmed
epistemic_stage: observed
status: active
valid_from: 2026-02-24
created: 2026-07-19
renamed_from: DP.D.037
source: "миграция из 01B-distinctions.md, коллизия разрешена в пользу файла (РП170 Ф3, 2026-07-19)"
see_also: [DP.D.037]
schema_version: 1
---

# DP.D.265: Кастомизация агента (Harness Engineering) ≠ Дообучение (Fine-Tuning)

> **Перенумеровано при разрешении коллизии (2026-07-19, РП170 Ф3): DP.D.037 → DP.D.265.** Номер 037 оставлен за одноимённым файлом (решение по анализу контекстов ссылок — превосходство за файлом; реестр: `DS-my-strategy/inbox/bugs/drift-2026-07-18-dpd-numbering-collisions.md`). Ссылки на «DP.D.037» до 2026-07-19 могли означать эту сущность.

| Harness Engineering (кастомизация) | Fine-Tuning (дообучение модели) |
|--------------------------------------|-------------------------------|
| Меняет промпты, инструменты, данные, ограничения | Меняет веса модели |
| Мгновенно, обратимо, дёшево | Долго, необратимо, дорого |
| Пользователь может сам (L4 Personal) | Требует ML-инфраструктуру |
| Подход IWE: L2 → L3 → L4 (DP.D.025) | Не наш подход |
| Результат: тот же Base LLM, другое поведение | Результат: другая модель |

**Почему важно**: «Настроить агента под себя» в архитектуре IWE = harness engineering (DP.D.025), НЕ fine-tuning LLM. Pipeline кастомизации по контурам: Base LLM → +L2 (API config, system prompt) → +L3 (CLAUDE.md, роли, скрипты) → +L4 (personal CLAUDE.md, ЦД, личные Pack). Каждый контур добавляет слой обвязки (harness), не меняя модель.

**Тест**: Меняются веса модели? → Fine-tuning. Меняются промпты, инструменты, контекст, ограничения? → Harness engineering (кастомизация).

> Связь: DP.D.025 (Harness ≠ Agent), DP.D.034 (L2/L3/L4 контуры), DP.ROLE.001 §2.1 (Agent Customization Pipeline).
