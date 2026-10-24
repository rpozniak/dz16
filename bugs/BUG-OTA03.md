# BUG-OTA03 — Cache panic під час force reinstall

**Версії:** 1.5; інші версії контрольовано не ретестовано.  
**Severity / Priority:** Major / High.  
**PRD:** [OTA-4](../reference/PRD.md#ota-4), [OTA-5](../reference/PRD.md#ota-5).

**Передумови:** 1.5/ota_1/valid після попередніх перерваних OTA й reset; saved connect успішний, lamp on/red/25/solid.

**Кроки:**

1. Ввести `ota install 1.5.0 force`, хотспот залишити ввімкненим.
2. Дочекатися результату; після аварійного boot перевірити `version`.

**Очікувано:** Запис у ota_0, progress 10–100%, success та штатний reboot.

**Фактично:**

Після Starting OTA / Writing to ota_0 і до першого progress — Cache error, reboot у попередню 1.5/ota_1/valid. BOD у цій OTA-спробі немає. Наступний контрольний reinstall завершився успішно.

```text
Guru Meditation Error: Core  0 panic'ed (Cache error).
Cache disabled but cached memory region accessed
Rebooting...
```

**Відтворюваність:** Одне виникнення; наступна завершена спроба без Cache error. Регресію не визначено.

**Докази:** [panic, backtrace та boot](../logs/66_v1.5.0_force_reinstall_cache_panic.log), [успішний повтор](../logs/67_v1.5.0_force_reinstall_success_settings_reset.log). 
