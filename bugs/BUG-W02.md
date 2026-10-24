# BUG-W02 — connect: StoreProhibited і reboot

**Версії:** 1.3; на 1.4/1.5 контрольовано не ретестовано.  
**Severity / Priority:** Major / High.  
**PRD:** [FR-W1](../reference/PRD.md#fr-w1), [FR-W2](../reference/PRD.md#fr-w2).

**Передумови:** 1.3/ota_0, Wi-Fi disconnected, saved SSID наявний.

**Кроки:**

1. Після boot виконати `status`, потім `connect`.
2. Дочекатися результату scanning.

**Очікувано:** Список мереж і вибір SSID або контрольована помилка підключення.

**Фактично:**

Одразу після scanning — StoreProhibited і автоматичний reboot у 1.3. Наступний connect успішний.

```text
I (46390) wifi station: scanning...
Guru Meditation Error: Core  0 panic'ed (StoreProhibited). Exception was unhandled.
EXCVADDR: 0x00000064
Rebooting...
```

**Відтворюваність:** Одне виникнення; наступна спроба успішна. Регресію не визначено.

**Докази:** [panic, повний backtrace, boot і повтор](../logs/07_v1.3.0_connect_panic_and_recovery.log). 
