# BUG-OTA03 — Аварійне перезавантаження на початку OTA

**Версії:** 1.5; інші версії контрольовано не ретестовано.  
**Severity / Priority:** Major / High.  
**PRD:** [OTA-4](../reference/PRD.md#ota-4), [OTA-5](../reference/PRD.md#ota-5).

**Передумови:** 1.5/ota_1/valid після попередніх перерваних OTA й reset; saved connect успішний, lamp on/red/25/solid.

**Кроки:**

1. Ввести `ota install 1.5.0 force`, хотспот залишити ввімкненим.
2. Після аварійного перезавантаження перевірити `version`.

**Очікувано:** Запис у ota_0, progress 10–100%, success та штатний reboot.

**Фактично:**

На початку запису в ota_0, до першого повідомлення про прогрес, пристрій аварійно перезавантажився. Запустилася попередня 1.5/ota_1/valid. У логу — помилка `Cache error`; BOD у цій спробі немає. Наступне перевстановлення завершилося успішно.

```text
Guru Meditation Error: Core  0 panic'ed (Cache error).
Cache disabled but cached memory region accessed
Rebooting...
```

**Відтворюваність:** Одне виникнення; наступна завершена спроба без аварійного перезавантаження. Регресію не визначено.

**Докази:** [помилка, повний backtrace і перезапуск](../logs/66_v1.5.0_force_reinstall_cache_panic.log), [успішний повтор](../logs/67_v1.5.0_force_reinstall_success_settings_reset.log). 
