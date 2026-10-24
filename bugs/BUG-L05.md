# BUG-L05 — Таймер не вимикає фізичне біле світло

**Версії:** 1.5; timer 5 на 1.3/1.4 пройшов.  
**Severity / Priority:** Major / High.  
**PRD:** [FR-L7](../reference/PRD.md#fr-l7), [FR-L8](../reference/PRD.md#fr-l8).

**Передумови:** 1.5, lamp on/white/50/solid; Wi-Fi disconnected.

**Кроки:**

1. Ввести `lamp timer 5`; дочекатися expiry; виконати `lamp status`.
2. У повторному сценарії ввімкнути лампу та виконати `lamp timer 1`.

**Очікувано:** Лампа фізично гасне, JSON off/0; залишається тьмяна червона Wi-Fi індикація.

**Фактично:**

Expiry через 5010 мс та 1000 мс; JSON off/0, але біле світло залишається як до таймера. Користувач: «Біле, як до завершення таймера». Ручне `lamp off` припиняє біле світіння.

```text
[LAMP] timer expired, lamp off
{"lamp":"off","color":[255,255,255],"brightness":50,"mode":"solid","timer_s":0}
```

**Відтворюваність:** По одному прогону timer 5 і timer 1. Регресія щодо пройденого timer 5 на 1.4.

**Докази:** [timer 5](../logs/48_v1.5.0_timer_expired_white_light_persists.log), [timer 1](../logs/52_v1.5.0_timer_1s_white_light_persists.log), [ручний off](../logs/49_v1.5.0_timer_bug_manual_off_recovery.log), [контроль 1.4](../logs/24_v1.4.0_timer_5s_pass.log). Фізичний результат підтверджений візуально; фото/відео немає.
