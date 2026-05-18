---
id: DP.M.085
name: "Онбординг пилота: Персональное руководство"
type: method
status: active
created: 2026-05-15
renamed_from: DP.M.038
---

# DP.M.085 — Онбординг пилота: Персональное руководство

> Source-of-truth процесса. Принцип ВДВ: выход этапа = вход следующего. Каждый вход и выход — физический артефакт.
> Связанные документы: `DP.M.037` (lifecycle репо), `DP.ROLE.041` (Аттестатор), `personal-guide-channels.md`.
> Скиллы: `/personal-guide-start`, `/personal-guide-render`, `/connect-guide`, `/consent`.
> Реализация: WP-309 (инфраструктура репо), WP-310 (Аттестатор + downstream).

---

## Два режима работы

**Первичный онбординг (однократно):** Этапы 0 → 1 → 2 → 3 → 4.
**Пересборка руководства (автоматически при смене ступени):** stage_transition_listener → Этап 3.

---

## Этап 0. Предусловия

| | |
|---|---|
| **Вход** | Новый пилот без аккаунта на платформе |
| **Действие** | Пилот регистрируется на платформе → создаёт GitHub-аккаунт (если нет) → оформляет подписку → даёт согласие на обработку персональных данных → платформа связывает идентификаторы в `identity_map` |
| **Выход** | 5 записей в Neon (`learning` DB): |

| Артефакт | Физическое подтверждение | Таблица |
|---|---|---|
| Аккаунт платформы | Строка в `users` | `learning.users` |
| GitHub-аккаунт | `github_login` заполнен | `learning.identity_map` |
| Подписка | Строка активной подписки | `learning.subscriptions` / `contracts` |
| Согласие на персданные | `opt_in=true, scope=stage_evaluation` | `learning.tracking_consent` |
| Связка identity | `(ory_uuid, github_id, telegram_id)` | `learning.identity_map` |

**Gate:** все 5 записей существуют → Этап 1. Отсутствует любая → процесс останавливается.

---

## Этап 1. Создание репо

| | |
|---|---|
| **Вход** | 5 записей в Neon (выход Этапа 0) + репо `personal-guide` отсутствует на GitHub пилота |
| **Действие** | Агент в роли **Оркестратора** вызывает `create_repository(name: "personal-guide")` → GitHub API |
| **Выход** | Пустой репо `github.com/<pilot>/personal-guide` (0 файлов) + `personal_guide_repo_url` в `identity_map` (Neon) |

**Подтверждение:** GitHub — репо существует; Neon — `identity_map.personal_guide_repo_url` заполнен.
**Идемпотентность:** HTTP 409 → записать URL в `identity_map`, перейти к Этапу 2.

---

## Этап 2. Регистрация GitHub App (webhook)

> Webhook регистрируется до доставки контента — тогда первый же push на Этапе 3 сразу подхватывается reindex.

| | |
|---|---|
| **Вход** | Пустой репо (выход Этапа 1) + GitHub App «Aisystant Personal Guide» не установлена на репо пилота |
| **Действие** | Агент в роли **Оркестратора** вызывает `/connect-guide` → строит URL установки → пилот открывает браузер, выбирает репо `personal-guide`, нажимает «Install» → агент polling `github_status` до подтверждения |
| **Выход** | Webhook-запись на сервере платформы `(repo=personal-guide, pilot_id, events=[push])` + `webhook_registered=true` в `identity_map` (Neon) |

**Подтверждение:** сервер — webhook активен; Neon — `identity_map.webhook_registered=true`.
**Трение:** установка App — ручное действие пилота (OAuth GitHub, 3-5 шагов). Минимизация: предзаполненный URL репо + пошаговая инструкция.

---

## Этап 3. Доставка контента

> Этот этап выполняется дважды в жизни пилота: (А) при первичном онбординге — Оркестратором; (Б) автоматически при каждой смене ступени — `stage_transition_listener` (WP-310).

| | |
|---|---|
| **Вход** | Пустой репо с активным webhook (выход Этапов 1-2) **ИЛИ** запись в `learning.stage_transitions` (при пересборке) |
| **Действие** | Агент в роли **Навигатора (R27)** вызывает `/personal-guide-render`: читает RCS из `Память.Derived` → вычисляет ступень `stage_raw = min(W, M1, M2, M4)` и bottleneck → выбирает заготовку `stage-N × domain-X` из `PACK-personal/personal-guide-seeds/` → подставляет плейсхолдеры → записывает файлы через `personal_write` → commit + push |
| **Выход** | 8 артефактов в репо + commit → webhook → reindex в `Память.Derived` (≤30 сек) |

| Файл | Содержимое |
|---|---|
| `README.md` | Навигация, дата сборки, ступень, домен |
| `profile.md` | RCS-профиль, bottleneck, неудовлетворённости, целевой ритм |
| `worldview.md` | Фаза мировоззренческой дуги (FORM.087), мемы |
| `methods.md` | Методы под bottleneck из доменной вставки |
| `weekly/YYYY-Www.md` | Гипотеза недели, слоты, ДЗ |
| `daily/YYYY-MM-DD.md` | Тактика дня, критерий закрытия, вопрос для чата |
| `history/reflection-template.md` | Шаблон рефлексии (5 вопросов, 5 мин) — только при первом запуске |
| `.claude/skills/` | 5 скиллов для работы в любом канале — только при первом запуске |

**Штатный путь для нового пилота:** RCS отсутствует → fallback `stage-1-random.md` → только `profile.md` с одним вопросом. RCS появляется после нескольких уроков → Аттестатор фиксирует переход → автоматическая пересборка.

**При пересборке (смена ступени):** прежние `weekly/daily` архивируются в `history/` → перезаписываются 6 основных файлов (без `.claude/skills/` и `reflection-template.md`).

---

## Этап 4. Работа с руководством

| | |
|---|---|
| **Вход** | Репо с 8 файлами + активный webhook (выходы Этапов 2-3) |
| **Действие** | Пилот работает через один из трёх интерфейсов. Агент в роли **Навигатора (R27)** читает `profile.md`, `methods.md` → ведёт занятие. Агент в роли **Фиксатора** пишет `daily/` и `history/` → commit + push → webhook → reindex `Память.Derived` (≤30 сек) |
| **Выход** | Заполненные `daily/YYYY-MM-DD.md`, `weekly/YYYY-Www.md`, `history/*.md` в репо + обновлённые агрегаты в `Память.Derived` (Neon) |

**Интерфейсы:**

| Интерфейс | Читает | Пишет |
|---|---|---|
| Бот (Telegram) | `profile.md`, `methods.md`, `weekly/` | `daily/`, `history/` |
| Браузер (claude.ai/code) | Все файлы | Все файлы |
| VS Code | Все файлы | Все файлы (прямое редактирование + скиллы) |

---

## Цикл Аттестатора (автоматический, WP-310)

После каждого занятия события попадают в Activity Hub → Аттестатор (`stage_evaluator.py`) пересчитывает показатели → при росте ступени пишет запись в `learning.stage_transitions` → `stage_transition_listener.py` подхватывает → 4 downstream-эффекта:

```
stage_transitions INSERT
    ├─→ digital_twins.stage_id UPDATE        (Neon indicators)
    ├─→ Уведомление пилоту в бот             (TG Bot API)
    ├─→ Триггер Портному → Этап 3 (пересборка)   ⚠️ STUB до WP-309 Ф7
    └─→ Целевой ритм                         (вложен в бот-уведомление)
```

**Источники событий для Аттестатора:**

| Источник | Примеры событий |
|---|---|
| IWE (VS Code / claude.ai) | `day_open`, `day_close`, `week_plan_closed`, `knowledge_extracted`, `pack_updated` |
| Бот | `slot_logged`, `wp_closed`, `strategy_session_completed` |
| LMS | `lesson_completed` |
| WakaTime | `coding_time` |
| Клуб | `post_published` (🔴 ждёт ORY-SSO, WP-296) |

---

## Сводная схема

```
Этап 0         →  Этап 1     →  Этап 2      →  Этап 3        →  Этап 4
Предусловия       Репо           Webhook         Контент           Работа
5 записей Neon    Пустой репо    Webhook активен  8 файлов + push   daily/weekly/history
                  + URL в Neon   + флаг в Neon    + reindex         + агрегаты в Neon
                                                       ↑
                                           Аттестатор (автопересборка
                                           при смене ступени)
```

---

## Статус реализации

| Этап | Статус | РП |
|---|---|---|
| Этап 0 — Gate предусловий | 🔴 не реализован | WP-309 Ф9 |
| Этап 1 — create_repository | ✅ готово | WP-309 Ф1-Ф6 |
| Этап 2 — Webhook, polling после установки | 🟡 частично (URL есть, polling отсутствует) | WP-309 Ф11 |
| Этап 3 — Первичный render | ✅ готово | WP-309 |
| Этап 3 — Автопересборка (Портной trigger) | 🔴 STUB | WP-309 Ф7 (16-17 мая) |
| Этап 4 — Работа, 3 интерфейса | ✅ готово | WP-309 |
| Цикл Аттестатора Ф1-Ф6 | ✅ готово | WP-310 |
| Аттестатор Gap-А (quality поле) | 🟡 частично | WP-310 backlog |
| Клуб как источник событий | 🔴 ждёт ORY-SSO | WP-296 |
