---
id: DP.D.116
name: "Semantic compiler ≠ Static site generator (SSG)"
type: distinction
domain: digital-platform
pack_refs: []
trust: high
epistemic_stage: empirical
status: active
valid_from: 2026-05-29
schema_version: 1
source: "peer-сессия 2026-05-29-14-mim-as-it-company, Тема 1 «Разделение семантического компилятора и SSG»"
---

# DP.D.116 Semantic compiler ≠ Static site generator (SSG)

## Различение

| | Semantic compiler | SSG |
|---|------------------|-----|
| **Вход** | source + user-state-config (cp-profile, bottleneck, reflection) | source markdown |
| **Выход** | Уникальный персональный артефакт | Multi-target structural output (HTML/TG/PDF) |
| **Детерминизм** | Один input → разный output для разных user-state | Один input → один output (детерминированно) |
| **Проверки** | Семантическая (правила домена + LLM-инференс) | Структурная (синтаксис, шаблоны) |
| **Пример** | Портной (WP-149): CAT + cp-profile + bottleneck → персональное руководство | VitePress + porter (WP-355 / WP-322): markdown → HTML, TG, PDF |
| **Аналогия** | gcc (с семантическим анализом) | Jekyll / Hugo / VitePress |

## Тест

«Один input даёт детерминированный output, или разный для разных user-state?»
- Детерминированный → SSG
- Зависит от user-state → semantic compiler

## Применение

В системах доставки knowledge-артефактов два контура сосуществуют независимо:
- semantic compiler производит **что** доставлять (персональный контент)
- SSG производит **как** доставлять (HTML, TG, PDF)

Свёртывание ролей в одну сущность («единый pipeline») приглашает либо ошибочное ожидание семантической проверки в SSG, либо растворение персонализации в шаблонной системе.

## Антипаттерн

Называть SSG-pipeline «компилятором» (porter, VitePress) → пользователь ожидает семантической проверки, которой нет → доверие к выходу выше, чем должно быть.
