---
id: DP.D.154
name: "Топология орг-структуры IWE: iwesys ≠ aisystant ≠ mimecosys"
type: distinction
domain: PACK-digital-platform
pack: DP
status: draft
valid_from: 2026-06-19
source: "session 2026-06-18-19; peer-session 2026-06-19-inbox-check"
related:
  see_also: [DP.ARCH.002, DP.D.144, DP.D.145]
tags: [github-org, topology, iwesys, aisystant, mimecosys, track-a, track-b, product]
---

# DP.D.154 — Топология GitHub-орг: iwesys ≠ aisystant ≠ MimEcoSys

## Три GitHub-организации и их роль

| Организация | Тип | Назначение | Трек |
|-------------|-----|-----------|------|
| **iwesys** | Product org | IWE-шаблон, Pack'и, скиллы, агентские правила — то, что ставится к пользователям через `update.sh`. Артефакты-дистрибутивы, не сервисы с данными. | Product (оба трека) |
| **aisystant** | Platform org (мир) | Облачная платформа Aisystant: сервисы, хранящие данные пользователей (GKE, Нью-Йорк). Мировой контур, jurisdiction-free от России. | Трек B |
| **MimEcoSys** | Platform org (Россия) | Российский мировой контур: сервисы на VK Cloud, хранящие данные российских пользователей. | Трек A |

## Ось тира (iwesys) ≠ Ось юрисдикции (aisystant vs MimEcoSys)

- **iwesys** — не делится по юрисдикции: IWE-дистрибутив один (платформенно-агностичный артефакт, копируется к пользователю). Нет «российского iwesys» и «мирового iwesys» — нарушение OwnerIntegrity.
- **aisystant** / **MimEcoSys** — два инстанса платформы, разные юрисдикции. Данные хранятся по юрисдикции; руководства — из мирового (canonical), российский = downstream-проекция + PD-данные.

## Применение

При проектировании нового репо: «Это артефакт (скилл, Pack, скрипт) или сервис с данными?»
- Артефакт → `iwesys`
- Сервис для мирового контура → `aisystant`
- Сервис для российского контура → `MimEcoSys`

## Тест

«Объект копируется к пользователю (как пакет) или к нему подключаются (как сервис)?»
- Копируется → `iwesys`
- Подключаются + хранит данные → `aisystant` или `MimEcoSys` по юрисдикции

## Источник

session 2026-06-18-19; уточнение директивой пилота к различению DP.D.144 (Трек = география, не поколение).
