# BUG-L01 — Переплутані фізичні кольори

**Версії:** 1.3; на 1.4/1.5 не відтворено.  
**Severity / Priority:** Minor / Medium.  
**PRD:** [FR-L2](../reference/PRD.md#fr-l2), [FR-L3](../reference/PRD.md#fr-l3), [FR-L4](../reference/PRD.md#fr-l4).

**Передумови:** 1.3, lamp on, solid, brightness 50, timer 0.

**Кроки:**

1. По черзі виконати `lamp color red`, `lamp color green`, `lamp color purple`, `lamp color cyan`; після кожної — `lamp status` і візуальна перевірка.
2. Повторити red/green числовими RGB `255 0 0` / `0 255 0`.

**Очікувано:** Фізичний колір відповідає назві/RGB.

**Фактично:**

UART/JSON правильні; red світить зеленим, green — червоним, purple — блакитним, cyan — фіолетовим. Числові red/green також переплутані. На 1.4/1.5 ретести названих кольорів і числових red/green пройшли.

```text
[LAMP] color set: red (255,0,0)
```

**Відтворюваність:** Підтверджено названим і числовим вводом; незалежну серію не проводили.

**Докази:** [UART 1.3](../logs/02_color_recheck_sanitized.log), [ретест 1.4](../logs/17_v1.4.0_numeric_rgb.log), [ретест 1.5](../logs/73_v1.5.0_numeric_red_green_black.log). [Фото red → green](../logs/evidence/v1.3.0_L02_red_initial_photo.jpg).
