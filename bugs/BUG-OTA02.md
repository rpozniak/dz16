# BUG-OTA02 — OTA скидає параметри та saved Wi-Fi

**Версії:** 1.4→1.5, 1.5→1.5; також параметри при 1.3→1.5.  
**Severity / Priority:** Major / High.  
**PRD:** [OTA-8](../reference/PRD.md#ota-8), [FR-L9](../reference/PRD.md#fr-l9), [FR-W3](../reference/PRD.md#fr-w3).

**Передумови:** Основний прямий сценарій: 1.3/ota_1/valid, off/blue/50/blink/0, saved connect успішний. Інші шляхи наведені в таблиці.

**Кроки:**

1. Виконати `ota https://raw.githubusercontent.com/BohdanHorbanych/esp32-ota-files/main/firmware/v1.5.0/station_WiFi.bin`.
2. Дочекатися success → reboot → self-check / valid; виконати `lamp status`.
3. На додаткових шляхах 1.4→1.5 (`ota update beta` після обриву/реконекту) та 1.5→1.5 (`ota install 1.5.0 force`) також перевірено saved `connect` до введення нових даних.

**Очікувано:** Колір, яскравість, режим і saved Wi-Fi зберігаються.

**Фактично:**

| Перехід | До → після | Wi-Fi після |
|---|---|---|
| 1.4→1.5, beta після обриву/реконекту | blue/37/blink → white/50/solid | Немає saved вибору; Enter → SSID cannot be empty |
| 1.5→1.5, force | red/25/solid → white/50/solid | Те саме; потрібне повторне налаштування |
| 1.3→1.5, прямий URL | blue/50/blink → white/50/solid | Не перевірено |

Старі сцени 1/2/3 після послідовності OTA стали empty; точний етап втрати не локалізований. Окремий broken rollback зберіг нову scene1.

```text
{"lamp":"off","color":[255,255,255],"brightness":50,"mode":"solid","timer_s":0}
```

**Відтворюваність:** Три різні переходи зі скиданням параметрів, не серія однакового сценарію. Внутрішню причину не встановлено.

**Докази:** [OTA 1.4→1.5](../logs/43_v1.4.0_beta_retry_without_reboot_to_v1.5.0.log), [параметри/Wi-Fi](../logs/44_v1.5.0_settings_loss_and_wifi_reenrollment.log), [force reinstall](../logs/67_v1.5.0_force_reinstall_success_settings_reset.log), [saved Wi-Fi після](../logs/70_v1.5.0_saved_wifi_missing_after_reinstall.log), [прямий 1.3→1.5](../logs/97_v1.3.0_to_v1.5.0_direct_url_settings_reset.log), [сцени empty](../logs/69_v1.5.0_all_scenes_empty_after_reinstall.log), [контроль rollback](../logs/77_v1.5.0_broken_rollback_scene_1_preserved.log). 
