---
id: DP.M.087
name: SECRETS.md как обязательный артефакт перед deploy на новый хост
kind: Method
status: active
created: 2026-05-18
sources:
  - captures 2026-05-18 (session-close)
  - DS-my-strategy/exocortex/SECRETS.md (commit 11907cb3, tsekh-1 checklist)
  - lessons_secret_drift_detector_automation.md (runtime детектор drift)
related:
  complements: [lessons_secret_drift_detector_automation.md]  # planning vs runtime
  applies_to: ["development.host_provisioning"]
---

# DP.M.087: SECRETS.md как обязательный артефакт перед deploy на новый хост

## Определение

Перед добавлением нового исполнительного хоста (tsekh-1 для overnight-агентов, новый Railway-сервис, новый CF Worker, новый VM) — обязательный артефакт `SECRETS.md` с явной картой секретов. Создаётся в planning-фазе, до первого `systemctl start` / `wrangler deploy` / `railway up`.

## Тест применимости

> «Развёртываю код на хосте, где этот код ещё не работал?»

- **Да** → SECRETS.md обязателен до первого deploy
- **Нет** (обновление существующего хоста) → SECRETS.md уже должен существовать, если нет — backfill

## Структура SECRETS.md (5 полей на каждый секрет)

| Поле | Описание | Пример |
|------|----------|--------|
| **Какие секреты нужны** | env var / файл / API token | `NEON_PROD_BASE`, `~/.config/aist/env`, `OPENAI_API_KEY` |
| **Источник** | где взять текущее значение | Neon dashboard / Railway env / локальный keystore / 1Password vault |
| **Точка инъекции** | как попадает в runtime | `.env` файл / systemd `EnvironmentFile=` / GitHub Actions secret |
| **Кто ротирует** | роль + периодичность | `Платформа-Инженер, 90 дней` / `при компрометации` |
| **Checklist verify** | smoke-test чтения | `bash -c 'echo $NEON_PROD_BASE \| head -c 20'` |

## Anti-pattern

Ad-hoc копирование секретов в момент deploy → 1-2 секрета забываются → silent fail в первый час работы (paged through monitoring или после первого cron). Особенно болезненно для overnight-агентов и cron-тасков с длинным cycle time.

## Связь с другими артефактами

- **lessons_secret_drift_detector_automation.md** — runtime детектор drift между потребителями (что делать ПОСЛЕ deploy). SECRETS.md — planning-стадия (что сделать ДО deploy). Оба нужны, разные стадии.
- **B7 Security checklist** — SECRETS.md = частный случай deployment-manifest для security-критичных артефактов. При прохождении ArchGate §Б для нового хоста — SECRETS.md обязательная часть Definition of Done.

## Применимость

| Тип хоста | SECRETS.md обязателен? |
|-----------|------------------------|
| Новая VM / физ.сервер для overnight-агентов | Да |
| Новый Railway service | Да |
| Новый CF Worker (через `wrangler secret put`) | Да (включая список planned secrets перед `wrangler deploy`) |
| Новый GitHub Actions workflow | Да (список secrets в README workflow) |
| Локальный dev (только разработчик) | Нет (но рекомендуется как `.env.example`) |

## Шаги (минимальный workflow)

1. Создать `SECRETS.md` в репо хост-провизионинга (например, `DS-my-strategy/exocortex/SECRETS.md` для tsekh-1) с 5-колоночной таблицей.
2. Заполнить ВСЕ строки до первого `git push` с кодом, который полагается на секреты.
3. Запустить checklist verify локально / на хосте — каждый секрет читается.
4. Только после прохождения checklist — `systemctl start` / `wrangler deploy` / `railway up`.
5. При добавлении нового секрета в код — backfill строки в SECRETS.md одним коммитом.
