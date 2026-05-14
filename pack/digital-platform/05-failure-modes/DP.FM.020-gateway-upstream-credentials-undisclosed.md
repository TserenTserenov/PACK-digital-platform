---
id: DP.FM.020
name: Gateway SC без security disclosure для upstream credentials
category: security
severity: major
status: active
summary: "SC для Gateway-компонента с upstream-proxy не содержит явного раздела «Безопасность» с MITM-disclosure. Потребитель не знает, что Gateway видит его OAuth-токены при proxying. Нарушение принципа informed consent в security архитектуре."
created: 2026-05-11
valid_from: 2026-05-11
related:
  see_also: [DP.SC.034, DP.SC.035, DP.IWE.005]
tags: [security, gateway, oauth, disclosure, service-clause]
source: "WP-150 Ф6-Ф7, verifier поднял must-fix при review SC.034 (11 мая 2026)"
---

# [DP.FM.020] Gateway SC без security disclosure для upstream credentials

## Суть паттерна

SC для Gateway-компонента с upstream-proxy **не содержит явного раздела «Безопасность»** с предупреждением о том, что Gateway выступает как MITM для upstream credentials потребителя.

Потребитель (peer-агент) подключается к Gateway и направляет запросы к upstream MCP (Aisystant) через Gateway — не подозревая, что Gateway технически видит OAuth-токены в транзите.

## Механизм

1. **Gateway = MITM по дизайну.** Любой upstream-proxy видит credentials в транзите. Это неизбежно при proxy-архитектуре.
2. **SC без disclosure = скрытое ожидание.** Потребитель предполагает, что Gateway не видит его credentials. SC формирует это ожидание молчанием.
3. **Разрыв ожиданий проявляется в security-review** — не в разработке. К тому моменту SC уже принят, изменять его инвазивно.
4. **Паттерн повторяется** при создании новых Gateway SC (облачных, локальных) без системного правила.

## Где проявляется

| Ситуация | Риск |
|----------|------|
| SC для Local Gateway без security раздела | peer-агент не знает о MITM природе |
| SC для Cloud Gateway без disclosure | внешний AI-клиент не знает о видимости токенов |
| Любой SC с `uses: [gateway]` без security секции | цепочка disclosure обрывается |

## Диагностика

Признак наличия паттерна: SC для Gateway содержит `uses` или `extends` ссылку на Gateway-компонент, но **не имеет секции «Безопасность»** или «Security disclosure».

```bash
grep -L "Безопасность\|Security" pack/digital-platform/08-service-clauses/DP.SC.*.md
```

## Профилактика

**Правило:** Любой SC для Gateway-компонента с upstream-proxy обязан содержать явный раздел «Безопасность» с MITM-disclosure:

```markdown
## Безопасность

**MITM upstream credentials:** Gateway выступает как proxy-посредник. При upstream-запросах Gateway технически видит OAuth-токены потребителя в транзите. Это проектное свойство proxy-архитектуры внутри trust boundary одного пользователя (DP.IWE.005 §Трастовая граница). Потребитель должен явно принять это ограничение.
```

**Применить к существующим SC:** DP.SC.021 (если использует Gateway), DP.SC.023, любые будущие Gateway SC.

## Связи

- Выявлен при: DP.SC.034 (Local Gateway), DP.SC.035 (peer-agent choreography)
- Предотвращает: нарушение informed consent в security архитектуре
- Связан с: DP.IWE.005 §Трастовая граница, DP.IWE.003
