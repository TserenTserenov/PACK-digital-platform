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
