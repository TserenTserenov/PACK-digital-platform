---
id: DP.D.109
name: "TOC Bottleneck (вклад в потерю Throughput) ≠ Readiness Gap (разрыв готовности)"
type: distinction
domain: digital-platform
pack_refs: []
status: active
valid_from: 2026-05-28
schema_version: 1
source: "session-transcript 2026-05-28 (peer-session 15 WP-250 — diagnostics-stage1.md)"
---

# DP.D.109 TOC Bottleneck ≠ Readiness Gap

| Аспект | Bottleneck (TOC) | Readiness Gap |
|--------|-----------------|---------------|
| **Определение** | Звено, через которое проходят 80-100% потерь Throughput | Компонент с низким % готовности по плану |
| **Критерий** | «Снять ограничение → вырастет общий выход?» | «Есть delta между целевым и текущим состоянием?» |
| **Пример** | zero_meaningful_activity у 11/11 пилотов | Observability: 30% readiness gap |
| **Действие** | Устранить первым | Улучшать в рабочем порядке |

## Ошибка путаницы

Компонент с большим readiness gap (напр. Observability) кажется главной болью — выглядит как «много работы предстоит». Но если его улучшение не увеличивает Throughput → не bottleneck, это вспомогательный инструмент.

## Тест TOC

«Если снять ограничение этого звена — вырастет ли общий Throughput системы?» Нет → не bottleneck.

## Аналогия

У завода сломана система мониторинга (Observability) и не работает главный станок (Activity). Починить мониторинг не увеличит выпуск продукции.

## Контекст

Выявлено в peer-session WP-250 (2026-05-28): при анализе первой когорты ошибочно выделялась Observability как bottleneck по readiness gap вместо отсутствия активности α-кластера.

## Связи

- Метод: DP.M.210 α/β/γ сегментация застрявших пользователей
- Источник: WP-250 bottleneck analysis, 2026-05-28
