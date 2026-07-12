---
id: DP.FM.267
type: failure-mode
title: publish-job-in-mirror-not-source — CI/CD publish job размещён в downstream mirror вместо canonical source
trust: observed
epistemic_stage: confirmed
domains: [ci-cd, multi-repo, canonical-source, deployment-topology]
source_session: 2026-07-07 session-close (git diff FMT-exocortex-template, revert canon-sync)
valid_from: 2026-07-07
schema_version: 1
related:
  see_also: [DP.FM.202]
---

# DP.FM.267 — Publish job в downstream mirror вместо canonical source

## Симптом

CI/CD publish/deploy workflow добавлен в репо-зеркало (downstream mirror/consumer) вместо canonical source репозитория. Workflow срабатывает только при обновлении зеркала, а не при изменении источника. Публикация зависит от ручного обновления mirror.

## Корень

В multi-repo топологии с designated canonical source — разработчик добавляет job «там, где удобно» (в том репо, с которым работает в данный момент), а не «там, где это семантически верно» (в canonical source).

## Профилактика

**Правило:** перед добавлением publish/deploy job — проверить: этот репо = canonical source артефакта или downstream consumer/mirror?

- Canonical source → job здесь ✅
- Mirror/consumer → job должен быть в source repo ❌

**Тест:** «Если source изменится без коммита в этот репо — workflow сработает корректно?"
- Нет → job не в том месте.

**Обнаружение:** поиск workflow-файлов с `publish`/`deploy`/`release` ключами в зеркальных репо. Сигнал: workflow не имеет триггера на push в canonical source.

## Применимо к

- GitHub Actions publish/release workflows в multi-repo топологии
- Любые CI системы с зеркальными репо (template → distribution, canonical → mirror)
- IWE-специфично: FMT-exocortex-template → iwesys/iwe-template

## Связано

- DP.FM.202 — multiple-registries-one-entity-drift (смежный: несколько источников одной сущности)
