# BUG-L02 — Приймається яскравість поза 0–100

**Версії:** 1.3; на 1.4/1.5 не відтворено.  
**Severity / Priority:** Minor / Medium.  
**PRD:** [FR-L5](../reference/PRD.md#fr-l5).

**Передумови:** 1.3, white/solid/on, brightness 100.

**Кроки:**

1. Ввести `lamp brightness 150`, потім `lamp status`.
2. Ввести `lamp brightness -5`, потім `lamp status`.

**Очікувано:** `error: brightness must be 0-100`; невалідні значення не зберігаються.

**Фактично:**

Обидві команди прийняті; JSON містить brightness 150, потім -5. Фізичне світло візуально не змінилося.

```text
[LAMP] brightness set: 150%
[LAMP] brightness set: -5%
```

**Відтворюваність:** Один послідовний прогін двох значень; -5 введено після прийнятого 150.

**Докази:** [UART 1.3](../logs/02_color_recheck_sanitized.log), [ретест 1.4](../logs/19_v1.4.0_brightness_solid_and_invalid.log), [ретест 1.5](../logs/46_v1.5.0_rgb_errors_and_solid_brightness.log). [Скриншот](../logs/evidence/v1.3.0_L05_invalid_brightness.png).
