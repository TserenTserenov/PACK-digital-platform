---
id: DP.M.064
name: Manual smoke + analogous-pattern coverage как substitute полной автоматизации
name_ru: Manual smoke + analogous-pattern coverage как substitute полной автоматизации
name_en: Manual Smoke + Analogous-Pattern Coverage as Validation Substitute
type: method
status: active
summary: "Когда full-automation smoke заблокирован внешним фактором (scheduling, deploy infrastructure, vendor bug) — DoD фазы можно закрыть не пустым deferral, а зачётом manual smoke + analogous-pattern coverage. Тест применимости: «можно ли доказать, что execution-path работает, через два независимых способа использования, оба не зависящие от заблокированного компонента?» Да → architecture validation done, automation defer как отдельная фаза."
created: 2026-05-17
trust:
  F: 3
  G: domain
  R: 0.80
epistemic_stage: emerging
related:
  uses: [DP.M.059]
  references: []
  realized_by: []
tags: [validation, smoke-test, automation, blocked-deploy, dod, integration-gate, phase-closure]
wp: WP-324
---

# Manual Smoke + Analogous-Pattern Coverage как Substitute Полной Автоматизации (DP.M.064)

## 1. Контекст

DoD фазы часто требует «full-automation smoke на расписании». Когда это заблокировано внешним фактором — типичный анти-паттерн: defer всей фазы целиком, без разделения «что доказано» и «что отложено». Через 2 недели не помнишь — теряется audit-trail.

## Forces

_Какие конкурирующие давления удерживает метод._

| Force | Tension |
|-------|---------|
| Скорость закрытия DoD фазы ↔ строгость двух-компонентного критерия (§3) | Соблазн закрыть фазу после одного успешного manual smoke конкурирует с требованием §3.2 — independent driver для architecture-validation, не одно только «у меня заработало» |
| Полнота manual smoke coverage (§3.1: «N≥рекомендованного, 4/4 или весь набор») ↔ скорость получения «зелёного» статуса | Меньшее N быстрее показывает результат, но не даёт полного task-coverage, требуемого для architecture-validation зачёта |
| Разделение architecture/scheduling-validation (§2) ↔ единство восприятия «готово/не готово» | Разделение технически честнее (см. §7 связь с IntegrationGate шаг 4), но снаружи выглядит как «наполовину сделано» — давление либо смешать критерии, либо занизить требования к substitute |

## 2. Правило: разделение architecture-validation и scheduling-validation

DoD фазы декомпозируется на два независимых критерия:

| Критерий | Substitute при блокировке |
|----------|---------------------------|
| **Architecture-validation** — execution-path работает корректно | Manual smoke + analogous-pattern coverage (см. §3) |
| **Scheduling-validation** — automation на cron/timer/scheduler | Defer как отдельная фаза с явным trigger'ом разблокировки |

## 3. Критерии substitute для architecture-validation

Зачёт architecture-критерия требует **оба** компонента одновременно:

1. **Manual smoke** — запустить инструмент вручную, end-to-end, N≥рекомендованного task-coverage (4/4 или весь набор)
2. **Analogous-pattern coverage** — independent driver, использующий **тот же execution-path**, должен показать рабочие результаты (например: автоматический dispatcher заблокирован, но 5 CCR sections done на том же `claude -p analyze-section` паттерне — execution-path доказан, driver другой)

## 4. Тест применимости

«Можно ли доказать, что execution-path работает, через два независимых способа использования, оба не зависящие от заблокированного компонента?»

- **Да** → architecture validation done, automation defer как отдельная фаза
- **Нет** → defer всей фазы, не разделять; повторить попытку после unblock

## Bias-Annotation

_Куда систематически съезжает внимание практикующего. Статус карточки — `epistemic_stage: emerging`: пометка `tentative` по прецеденту WP-448 Ф12._

| Bias | Direction of distortion |
|------|--------------------------|
| _(tentative)_ Пример §8 копируется как шаблон, а не как иллюстрация принципа | При поиске independent driver для §3.2 внимание тянется к буквальному повторению конкретного паттерна из примера (`claude -p analyze-section`, 5 CCR sections), а не к вопросу «что в текущей блокировке — настоящий независимый способ использования того же execution-path»; прошлый кейс подменяет требование §4 вместо того, чтобы быть только иллюстрацией |
| _(tentative)_ «Оба компонента одновременно» (§3) читается как «оба со временем» | После прохождения manual smoke (§3.1) внимание переключается на следующую задачу, откладывая analogous-pattern coverage (§3.2) на «когда будет время» — хотя §3 явно требует зачёта не по факту выполнения каждого компонента порознь, а по их одновременному наличию на момент применения теста §4 |

## 5. Анти-паттерн

| Анти-паттерн | Симптом | Лечение |
|--------------|---------|---------|
| **Defer всей фазы без разделения architecture/scheduling** | Через 2 недели не помнишь, что доказано | §2 — разделить критерии |
| **«Закроем когда unblock»** | unblock может тянуться месяцами, теряется momentum | §3 — substitute через manual+analogous |
| **Manual smoke без analogous coverage** | Доказано «у меня работает», не «архитектура работает» | §3.2 — обязателен independent driver |
| **Считать substitute полным закрытием DoD** | Scheduling-критерий забыт, automation не появляется | §2 — scheduling-defer = отдельная фаза с trigger'ом |

## 6. Применимость

- **Phases с deploy-зависимостями** — NixOS systemd unit, Railway-deploy, CF Workers prod
- **IntegrationGate phases (3)→(4) реализация** — когда production-deploy блокирован, можно закрыть architecture через staging-smoke + analogous
- **Proof-of-concept до prod-rollout** — POC закрывается architecture-validation, prod-rollout отдельной фазой
- **Vendor-blocked features** — manual + analogous пока vendor fix не доехал

## 7. Связь с другими методами

- **DP.M.059 (Phase-Closure Triad)** — этот метод даёт критерий, ПРИ КОТОРОМ фаза может быть закрыта; DP.M.059 описывает, КАК оформить commit закрытия (триада артефактов)
- **CLAUDE.md §2 IntegrationGate** — методы 1-4: обещание → сценарии → роль → реализация. Этот метод применяется на шаге (4) реализация, когда часть DoD заблокирована

## 8. Пример (WP-324 Ф9, 17 мая 2026)

DoD Ф9: «full-automation smoke 4 real-tasks через own dispatcher на расписании». Заблокировано: tsekh-1 не имеет Nix systemd unit для headless `claude -p`.

Применён substitute:
1. **Manual smoke 4/4** — dispatcher запущен вручную, 4 task'а end-to-end успешно
2. **Analogous-pattern coverage** — 5 CCR sections done на том же `claude -p analyze-section` паттерне (тот же execution-path, driver Cloud Code Runtime вместо own dispatcher)

Результат: architecture validation ✅, scheduling-validation defer (отдельная фаза с trigger'ом «Nix systemd unit готов на tsekh-1»).

---

> 2026-07-27 — миграция на обогащённый формат карточки (Forces + Bias-Annotation), WP-448 Ф12 Батч 3 (суб-батч 3). Эталон формата: `SPF/pack-template/03-methods/_method-card-template.md`.
