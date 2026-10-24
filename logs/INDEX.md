# Додаткові докази

Основні логи відкриваються безпосередньо зі [звіту](../REPORT.md) та [карток багів](../REPORT.md#2-матриця-багів). Тут лише додаткові перевірки. Нумерація файлів збережена з робочої сесії; перекриття контексту не є новими повторами. Час у дужках UART — uptime, після reboot починається заново.

## Лампа 1.4

- [red/green](../logs/15_v1.4.0_red_green_retest.log)
- [solid / помилки яскравості](../logs/19_v1.4.0_brightness_solid_and_invalid.log)
- [скасування таймера](../logs/25_v1.4.0_timer_cancel.log)
- [перерваний Wi-Fi-пріоритет](../logs/30_v1.4.0_wifi_priority_interrupted_resets.log)

## Лампа 1.5

- [RGB / помилки / solid](../logs/46_v1.5.0_rgb_errors_and_solid_brightness.log)
- [режими / яскравість](../logs/47_v1.5.0_modes_and_brightness.log)
- [таймер off/cancel](../logs/50_v1.5.0_timer_off_and_cancel.log)
- [межі таймера](../logs/51_v1.5.0_timer_invalid_and_upper_bound.log)
- [порожні сцени](../logs/53_v1.5.0_empty_scenes_and_manual_off.log)
- [номери сцен](../logs/54_v1.5.0_invalid_scene_numbers.log)
- [scene1](../logs/55_v1.5.0_scene_1_save_load.log)
- [scene2/3](../logs/56_v1.5.0_scenes_2_3_and_slot_independence.log)
- [reboot / сцени](../logs/57_v1.5.0_reboot_settings_timer_and_scenes.log)
- [фізичний load сцен](../logs/59_scenes_visual_after_usb_reconnect_2026-10-24.log)
- [led on/off/auto](../logs/75_v1.5.0_led_off_on_auto_lamp_unchanged.log)
- [перерваний Wi-Fi-пріоритет](../logs/76_v1.5.0_wifi_priority_brownout_then_reenroll.log)

## Додаткові OTA

- [stable already current 1.4](../logs/13_v1.4.0_channels_already_up_to_date.log)
- [offline 1.4](../logs/28_v1.4.0_ota_without_wifi.log)
- [offline 1.5](../logs/71_v1.5.0_ota_without_wifi.log)
- [stable на 1.5 / saved Wi-Fi](../logs/78_v1.5.0_rollback_saved_wifi_and_stable_downgrade_blocked.log)
- [обрив URL 1.5](../logs/86_v1.5.0_url_ota_interrupted_at_20_no_reboot.log)
- [повтор без reboot 1.5](../logs/87_v1.5.0_url_retry_already_in_progress_without_reboot.log)
- [invalid image 1.4](../logs/89_v1.4.0_saved_wifi_and_url_invalid_image.log)
- [прямий URL 1.4](../logs/90_v1.4.0_direct_url_broken_rollback.log)
- [симуляція 1.4](../logs/91_v1.4.0_ota_test_server_simulation.log)
- [invalid image 1.3](../logs/94_v1.3.0_url_invalid_image_rejected.log)
- [симуляція 1.3](../logs/96_v1.3.0_ota_test_server_and_rollback_saved_wifi.log)
- [прямий URL 1.3→1.5](../logs/97_v1.3.0_to_v1.5.0_direct_url_settings_reset.log)

## Повний UART

[Початкове прошивання](../logs/00_initial_flash_transcript.txt), [початковий запуск](../logs/01_v1.3.0_initial_sanitized.log), [повна санітизована сесія](../logs/02_color_recheck_sanitized.log).

Непечатні байти подані як `\xNN`, некоректний UTF-8 — `�`; паролі замінені на `***`. Сирий лог із паролями не призначений для здачі або Git.
