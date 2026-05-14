---
id: DP.ROLE.041
name: Аттестатор
type: role-description
status: draft
valid_from: 2026-05-14
summary: "Роль автоматического вычислителя ступени Ученика: читает события из Activity Hub, считает 5 характеристик по двум осям (Мастерство × Мировоззрение), сравнивает с нормативной матрицей и записывает переход в learning.stage_transitions. Запускает downstream: обновление digital_twins, уведомление пилоту, триггер Портному."
related:
  specializes: [U.RoleAssignment]
  component_of: [DP.ROLE.001]
  realizes: [DP.SC.020]
  uses:
    - PD.FORM.089   # RCS-модель: 5 характеристик, матрица минимумов, accounting_period
    - PD.FORM.080   # Нарратив ступеней + двойной gate Д→П
    - DP.ROLE.032   # Event Ingester — поставляет события в domain_event
    - DP.ROLE.034   # Rewards Projector — downstream-параллель по баллам
  downstream_consumers:
    - Портной (re-render personal-guide при смене ступени)
    - Bot notification (пилот получает сообщение о новой ступени)
    - digital_twins.stage_id (единый профиль пользователя)
created: 2026-05-14
updated: 2026-05-14
wp: WP-310
---

# Аттестатор (DP.ROLE.041)

> **Kind:** Platform Evaluation Role (платформенная роль вычисления ступени).
> **Owner Role:** Платформа (aisystant) — исполнитель `stage_evaluator.py` (Railway/tsekh-1 cron).
> **Текущий исполнитель:** `stage_evaluator.py` в `DS-IT-systems/activity-hub/activity_hub/workers/` — запускается cron 04:35 МСК ежедневно (после profiler).

## 1. Миссия

Автоматически и воспроизводимо определять **текущую ступень Ученика** (1 Случайный → 5 Проактивный) по объективным поведенческим данным из Activity Hub. Устранять произвол ручной диагностики: два запуска на одних данных дают одинаковый результат.

Фиксировать **момент перехода** (stage_transitions) и запускать downstream-эффекты: уведомление пилоту, перегенерацию руководства, обновление профиля.

**Граница:** только инкрементальные переходы (ступень растёт, не падает) в MVP. Понижение ступени — отдельная задача (не реализована). Также не заменяет живую диагностику Диагноста (R28) — дополняет как baseline-proxy для новых пользователей.

---

## 2. Полный процесс: от действия пользователя до смены ступени

### 2.1 Источники событий (откуда приходят данные)

Пользователь действует в разных местах — каждое место эмитирует события в `learning.domain_event` (Activity Hub, Neon):

| Место действия | Что делает пользователь | Событие | Статус |
|---|---|---|---|
| **IWE (VS Code / claude.ai)** | Открывает день (Day Open) | `day_open`, `day_plan_opened` | ✅ ORZ hook |
| **IWE (VS Code / claude.ai)** | Закрывает день (Day Close) | `day_close`, `day_plan_closed` | ✅ ORZ hook |
| **IWE (VS Code / claude.ai)** | Закрывает неделю (Week Close) | `week_plan_closed` | ✅ ORZ hook |
| **IWE (VS Code / claude.ai)** | Закрывает месяц (Month Close) | `month_plan_closed` | ✅ ORZ hook |
| **IWE (VS Code / claude.ai)** | Проводит стратегическую сессию | `strategy_session_completed` | ✅ ORZ hook |
| **IWE (VS Code / claude.ai)** | Извлекает знание (/ke) | `knowledge_extracted` | ✅ ORZ hook |
| **IWE (VS Code / claude.ai)** | Обновляет Pack | `pack_updated` | ✅ git hook |
| **IWE (VS Code / claude.ai)** | Любая IWE-сессия | `iwe_session` | ✅ ORZ hook |
| **Бот (@aist_me_bot)** | Логирует слот (/slot) | `slot_logged` | ✅ бот |
| **Бот (@aist_me_bot)** | Закрывает РП | `wp_closed`, `wp_completed` | ✅ бот |
| **Бот (@aist_me_bot)** | Завершает стратегическую сессию | `strategy_session_completed` | ✅ бот (дубль с IWE) |
| **LMS (aisystant.com)** | Проходит урок | `lesson_completed` | ✅ bridge-2 |
| **LMS (aisystant.com)** | Получает квалификацию | `qualification_granted` | ⚠️ не реализовано (Gap-В, не блокер) |
| **WakaTime** | Кодирует в IDE/VS Code | `coding_time` | ✅ WakaTime adapter (iwe.py) |
| **Клуб (systemsworld.club)** | Публикует пост | `club_post_created` | 🔴 WP-296 ждёт ORY-SSO |
| **GitHub** | Коммитит в Pack | `pack_updated` | ✅ git hook (дубль с IWE) |

> **WakaTime и GitHub** — не «места» в UI-смысле, но реальные источники данных. WakaTime даёт Инвестированное время, GitHub-коммиты в Pack дают Методичность.

### 2.2 Характеристики: из каких событий и к каким осям относятся

Аттестатор группирует события по **5 характеристикам**, разбитым по двум осям:

**Ось: Мастерство** — операциональный уровень, как человек организован и сколько вкладывает.

| Характеристика | Показатель | Откуда события | Тип |
|---|---|---|---|
| **Систематичность** | `self_dev_days_per_week` | `SELF_DEV_EVENT_TYPES`: lesson_completed, knowledge_extracted, pack_updated, iwe_session, day_open/close, week/month_plan_closed, strategy_session_completed | обязательная — ноль блокирует ступень |
| **Инвестированное время** | `avg_hours_per_week` | `coding_time` (WakaTime) + `slot_logged` + `lesson_completed` | обязательная — ноль блокирует ступень |
| **Методичность мышления** | `methodical_events_per_month` | lesson_completed, knowledge_extracted, pack_updated, qualification_granted | компенсаторная |

**Ось: Мировоззрение** — рефлексивный уровень, как человек видит себя и систему.

| Характеристика | Показатель | Откуда события | Тип |
|---|---|---|---|
| **Системность мировоззрения** | `worldview_score` | week_plan_closed (q≥3 бонус ×4), month_plan_closed, strategy_session_completed, knowledge_extracted, pack_updated | основная |
| **Агентность** | `agency_score` | wp_created, wp_closed, wp_completed, strategy_session_completed | основная |

> **Почему две оси, а не одна?** Мастерство и Мировоззрение — **ортогональные** линии развития (FORM.080 §3). Можно быть очень систематичным (высокое Мастерство) и при этом не видеть себя как деятеля в системе (низкое Мировоззрение). Ступень = узкое место обеих осей. Это характеристики **человека как системы** — не произвольный каталог, а два независимых измерения одного объекта.

**Дополнительные показатели** (MVP: логируются, в расчёт не входят):

| Показатель | Смысл | Статус |
|---|---|---|
| `execution_stability` | Серийность — подряд-дни без разрыва (признак автоматизированного ритма) | 🔴 не реализован в rcs-collector (Ф4+) |
| `total_hours_cumulative` | Накопленные часы за всё время — hard gate: нельзя получить ступень без минимального опыта | ✅ реализован как кумулятив в stage_evaluator.py |

### 2.3 Нормативная матрица (пороги по характеристикам)

Аттестатор сравнивает вычисленные индексы (0–5) с матрицей минимумов:

| Характеристика | Ст.1 | Ст.2 | Ст.3 | Ст.4 | Ст.5 |
|---|:---:|:---:|:---:|:---:|:---:|
| Систематичность (дн/нед) | — | ≥3 | ≥5 | ≥6 | ≥6.7 |
| Инвестированное время (ч/нед) | — | ≥4 | ≥6 | ≥8 | ≥10 |
| Накоп. часы (hard gate) | — | ≥20ч | ≥48ч | ≥96ч | ≥161ч |
| Методичность мышления | — | — | ≥1 | ≥2 | ≥3 |
| Системность мировоззрения | — | — | ≥2 | ≥3 | ≥4 |
| Агентность | — | — | ≥1 | ≥2 | ≥4 |

**Окно расчёта растёт со ступенью** (`accounting_period` — ключевой параметр):

| Ступень | Окно | Смысл |
|---|---|---|
| Случайный (1) | 1 нед | Начинающий оценивается за последнюю неделю |
| Практикующий (2) | 4 нед | Нужна месячная устойчивость |
| Систематический (3) | 8 нед | Два месяца стабильной практики |
| Дисциплинированный (4) | 12 нед | Квартал |
| Проактивный (5) | 24 нед | Полгода — доказанная устойчивость |

**Управление нормативами.** Сейчас нормативы захардкожены в `stage_evaluator.py` и `compute_stage_mvp()` в FORM.089 §12. Целевой состав управляющих параметров (`i.accounting_period`, `i.min_hours_for_qualification`, пороги характеристик по ступеням, веса событий) — выводить в конфиг Pack (`iwe-actions-catalog.md §7`), чтобы агент-Аттестатор мог перечитывать нормативы без пересборки кода. Это задача Ф5+ (конфиг-driven).

### 2.4 Downstream: что происходит после перехода ступени

```
stage_transitions INSERT (Аттестатор)
    │
    ├─→ digital_twins.stage_id UPDATE      — профиль пользователя обновлён
    ├─→ Уведомление пилоту в бот           — «Вы перешли на ступень N»
    ├─→ Триггер Портному                   — re-render personal-guide под новую ступень
    └─→ Обновление целевого ритма          — норма часов/нед в weekly-plan.md
```

> **Статус Ф2-Ф3 (май 2026):** downstream не реализован. Аттестатор пишет в stage_transitions, но дальше ничего не происходит. Реализация — WP-310 Ф2-Ф3.

---

## 3. Обязанности

| Обязанность | Метод | Триггер |
|-------------|-------|---------|
| Загрузить список opt-in пользователей | `SELECT account_id FROM learning.tracking_consent WHERE opt_in=TRUE` | Каждый запуск |
| Прочитать события по каждому пользователю | SQL по `learning.domain_event` + `learning.w_reflections` | По каждому account_id |
| Вычислить индексы характеристик | `calc_rcs_indices(metrics)` → {M1, M2, M4, W} 0..5 | По каждому account_id |
| Вычислить ступень | `compute_stage(indices)` — FORM.089 §5 | По каждому account_id |
| Сравнить с текущей ступенью | `MAX(to_stage) FROM stage_transitions WHERE account_id=$1` | По каждому account_id |
| Записать переход | INSERT в `learning.stage_transitions` (from, to, triggered_by, evidence JSONB) | Только если computed > cur |
| Запустить downstream | listener/hook → digital_twins + бот + Портной + ритм | После каждого INSERT (Ф2-Ф3) |
| Логировать метрики | stage_transitions_inserted, users_processed, errors | Каждый запуск |

---

## 4. Полномочия

- **Читает** `learning.domain_event` (только WHERE account_id = $1 — L2-PRIVACY).
- **Читает** `learning.w_reflections`.
- **Читает** `learning.tracking_consent` (проверка opt-in).
- **Пишет** в `learning.stage_transitions` (INSERT, не UPDATE).
- **НЕ понижает** ступень (только инкремент в MVP).
- **НЕ читает** raw-текст пользователя — только числа и event_type.

---

## 5. Границы роли

| Делает | НЕ делает |
|--------|-----------|
| Считает ступень автоматически | Не ведёт диалог с пользователем (это R28 Диагност) |
| Фиксирует переход с evidence | Не объясняет пользователю почему (это R27 Навигатор) |
| Запускает downstream-эффекты | Не создаёт контент руководства (это Портной) |
| Работает по расписанию | Не является on-demand API (в MVP) |
| Пишет только вперёд (рост) | Не понижает ступень при деградации (будущая задача) |

---

## 6. Инварианты (что гарантируется)

1. **Idempotency:** повторный запуск на тех же данных не создаёт дублей. UNIQUE(account_id, to_stage) на `stage_transitions`.
2. **Privacy:** только числа/индексы в evidence JSONB — никакого raw-text, имён, email.
3. **Consent gate:** пользователь без opt-in пропускается (skipped_consent += 1).
4. **Монотонность:** `to_stage > from_stage` — DB CHECK constraint (миграция 114+).
5. **Воспроизводимость:** один и тот же алгоритм + данные → одна и та же ступень.

---

## 7. Режим отказа

| Отказ | Симптом | Реакция |
|---|---|---|
| DSN недоступен | `asyncpg.ConnectionError` | Завершить с exit(1), не писать |
| Метрики читаются, но вычисление упало | Exception в `evaluate_one` | errors += 1, пропустить пользователя, продолжить |
| INSERT отклонён (дубль) | ON CONFLICT DO NOTHING | Нормально — idempotent |
| Downstream не отвечает (Ф2+) | Timeout listener | Логировать, не блокировать следующий цикл |
| stage_evaluator не запускался N дней | — | Alert Ф6 (не реализован) |

---

## 8. Известные ограничения MVP

1. **A-слот = 0 хардкодом.** Агентность не вычисляется — gate Д→П (ст.4→5) автоматически недостижим. Реализация A — отдельная задача после WP-214 Ф10.5.
2. **Gap-Д:** M1 (Систематичность) использует широкий фильтр `activity_domain IN ('practice', 'learning')` вместо `SELF_DEV_EVENT_TYPES`. Завышает Систематичность. Фикс — Ф4 WP-310.
3. **Gap-А:** поле `quality` в `week_plan_closed` payload не заполняется → весовой бонус ×4 для Системности мировоззрения не работает. Фикс — Gap-А WP-310.
4. **Клуб не подключён:** `club_post_created` добавит вес в Системность мировоззрения — ждёт WP-296 (ORY-SSO).
5. **Нормативы захардкожены:** пороги и веса в коде, не в конфиге. Управление → конфиг-driven Ф5+.
6. **Downstream не реализован:** после записи stage_transitions ничего не происходит — Ф2-Ф3 WP-310.

---

## 9. Ссылки

| Документ | Где |
|---|---|
| Алгоритм вычисления ступени | `PD.FORM.089-learner-rcs.md` §5, §12 |
| Веса событий и конфигурация | `DS-ecosystem-development/.../Data-Governance/iwe-actions-catalog.md §7` |
| Код исполнителя | `DS-IT-systems/activity-hub/activity_hub/workers/stage_evaluator.py` |
| DB-схема stage_transitions | `DS-IT-systems/neon-migrations` (миграции 109-114) |
| Нарративы ступеней | `PD.FORM.080-stage-direction-normative-matrix.md` |
| РП реализации | `DS-my-strategy/inbox/WP-310-attestator.md` |
