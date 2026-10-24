# HW-16 — Smart Lamp та OTA

**Pozniak · 24.10.2026 · ESP32-S3 DevKit · UART 115200 8N1 · Wi-Fi 2.4 GHz**

Стартову 1.3 прошито через USB; основний шлях **1.3 → stable 1.4 → beta 1.5** виконано OTA. Очікування — [PRD](reference/PRD.md). [Фізичну поведінку](logs/VISUAL_RESULTS.md) перевірено візуально, параметри й OTA — за UART. Паролі в логах замасковано.

## 1. Чекліст за версіями

Результати відкривають відповідні логи; ID багів у матриці — їхні картки. `pass` — очікувану поведінку підтверджено; `fail` — знайдено дефект; `частково` — є неперевірена частина; `N/A` — перевірка не застосовується до версії.

| ID | Перевірка | PRD | 1.3.0 | 1.4.0 | 1.5.0 |
|---|---|---|---|---|---|
| TC-L01 | on/off, фізичне світло | [FR-L1](reference/PRD.md#fr-l1), [FR-L8](reference/PRD.md#fr-l8) | [pass](logs/01_v1.3.0_initial_sanitized.log) | [pass](logs/14_v1.4.0_lamp_on_off.log) | [pass](logs/45_v1.5.0_on_off_and_named_colors.log) |
| TC-L02 | 7 названих кольорів | [FR-L2](reference/PRD.md#fr-l2), [FR-L4](reference/PRD.md#fr-l4) | [fail BUG-L01](logs/02_color_recheck_sanitized.log) | [pass](logs/16_v1.4.0_remaining_named_colors.log) | [pass](logs/45_v1.5.0_on_off_and_named_colors.log) |
| TC-L03 | RGB, межі 0/255 | [FR-L3](reference/PRD.md#fr-l3), [FR-L4](reference/PRD.md#fr-l4) | [fail BUG-L01](logs/02_color_recheck_sanitized.log) | [pass](logs/17_v1.4.0_numeric_rgb.log) | [pass](logs/73_v1.5.0_numeric_red_green_black.log) |
| TC-L04 | Невідомий колір / RGB поза межами | [FR-L2](reference/PRD.md#fr-l2), [FR-L3](reference/PRD.md#fr-l3) | [pass](logs/02_color_recheck_sanitized.log) | [pass](logs/18_v1.4.0_invalid_colors.log) | [pass](logs/46_v1.5.0_rgb_errors_and_solid_brightness.log) |
| TC-L05 | Яскравість у режимах; -5/150 | [FR-L5](reference/PRD.md#fr-l5) | [fail BUG-L02](logs/02_color_recheck_sanitized.log) | [pass: solid 0/10/100, blink/breathe 10/100](logs/20_v1.4.0_blink_breathe_brightness.log) | [fail BUG-L04](logs/47_v1.5.0_modes_and_brightness.log) |
| TC-L06 | solid / blink / breathe | [FR-L6](reference/PRD.md#fr-l6) | [pass](logs/02_color_recheck_sanitized.log) | [pass](logs/20_v1.4.0_blink_breathe_brightness.log) | [pass при 50%](logs/47_v1.5.0_modes_and_brightness.log) |
| TC-L07 | Режим rainbow | [FR-L6](reference/PRD.md#fr-l6) | N/A: немає у версії | N/A: немає у версії | [pass](logs/47_v1.5.0_modes_and_brightness.log) |
| TC-L08 | Таймер 5 с: expiry та фізичне вимкнення | [FR-L7](reference/PRD.md#fr-l7), [FR-L8](reference/PRD.md#fr-l8) | [pass](logs/02_color_recheck_sanitized.log) | [pass](logs/24_v1.4.0_timer_5s_pass.log) | [fail BUG-L05](logs/48_v1.5.0_timer_expired_white_light_persists.log) |
| TC-L09 | Таймер: cancel, межі, помилки та стан off | [FR-L7](reference/PRD.md#fr-l7) | [частково: timer 1 — лише UART](logs/02_color_recheck_sanitized.log) | [pass](logs/26_v1.4.0_timer_bounds.log) | [fail BUG-L05: timer 1; інші перевірки pass](logs/52_v1.5.0_timer_1s_white_light_persists.log) |
| TC-L10 | Reboot: параметри, off, timer=0 | [FR-L1](reference/PRD.md#fr-l1), [FR-L7](reference/PRD.md#fr-l7), [FR-L9](reference/PRD.md#fr-l9) | [pass](logs/02_color_recheck_sanitized.log) | [pass](logs/27_v1.4.0_reboot_persistence.log) | [pass](logs/57_v1.5.0_reboot_settings_timer_and_scenes.log) |
| TC-L11 | Сцени 1–3: save/load | [FR-L10](reference/PRD.md#fr-l10) | N/A: немає у версії | N/A: немає у версії | [pass](logs/56_v1.5.0_scenes_2_3_and_slot_independence.log) |
| TC-L12 | Сцени: помилки, reboot і збереження після OTA | [FR-L10](reference/PRD.md#fr-l10), [OTA-8](reference/PRD.md#ota-8) | N/A | N/A | [помилки/reboot pass; після OTA fail BUG-OTA02](logs/69_v1.5.0_all_scenes_empty_after_reinstall.log) |
| TC-L13 | Невідома lamp-команда та невідомий режим | [FR-L11](reference/PRD.md#fr-l11), [FR-L6](reference/PRD.md#fr-l6) | [pass](logs/02_color_recheck_sanitized.log) | [pass](logs/21_v1.4.0_modes_scenes_unknown_errors.log) | [pass: lamp-команда](logs/72_v1.5.0_unknown_lamp_command_unchanged.log), [pass: режим](logs/47_v1.5.0_modes_and_brightness.log) |
| TC-L14 | Пріоритет лампи щодо led rgb | [PRD §4](reference/PRD.md#4-smart-lamp) | [fail BUG-L03](logs/02_color_recheck_sanitized.log) | [fail BUG-L03](logs/29_v1.4.0_led_priority_rmt.log) | [fail BUG-L03](logs/74_v1.5.0_led_rgb_priority_and_rmt.log) |
| TC-L15 | Команди rainbow/scene для недоступних функцій → not implemented | [PRD §1](reference/PRD.md#1-версії-та-канали-оновлень) | [pass](logs/02_color_recheck_sanitized.log) | [pass](logs/21_v1.4.0_modes_scenes_unknown_errors.log) | N/A: функції реалізовані |
| TC-W01 | connect, saved Enter, reboot | [FR-W1](reference/PRD.md#fr-w1), [FR-W2](reference/PRD.md#fr-w2), [FR-W3](reference/PRD.md#fr-w3) | [fail BUG-W02](logs/07_v1.3.0_connect_panic_and_recovery.log) | [pass](logs/12_v1.4.0_saved_wifi_reconnect.log) | [pass; OTA окремо TC-O10](logs/60_v1.5.0_saved_wifi_after_restarts_and_ota_check.log) |
| TC-O01 | version / boot / ota check узгоджені | [OTA-1](reference/PRD.md#ota-1) | [pass](logs/02_color_recheck_sanitized.log) | [pass](logs/13_v1.4.0_channels_already_up_to_date.log) | [pass](logs/60_v1.5.0_saved_wifi_after_restarts_and_ota_check.log) |
| TC-O02 | ota check: current/stable/beta | [OTA-2](reference/PRD.md#ota-2) | [pass](logs/02_color_recheck_sanitized.log) | [pass](logs/13_v1.4.0_channels_already_up_to_date.log) | [pass](logs/60_v1.5.0_saved_wifi_after_restarts_and_ota_check.log) |
| TC-O03 | stable/beta, already up to date | [OTA-3](reference/PRD.md#ota-3) | [pass](logs/11_v1.3.0_to_v1.4.0_success.log) | [pass](logs/43_v1.4.0_beta_retry_without_reboot_to_v1.5.0.log) | [pass; stable downgrade blocked](logs/61_v1.5.0_beta_already_current_and_downgrade_blocked.log) |
| TC-O04 | Встановлення версії; захист від downgrade без force | [OTA-4](reference/PRD.md#ota-4) | [pass: reinstall 1.3; downgrade нижче 1.3 не перевірено](logs/93_v1.3.0_same_version_install_without_force.log) | [pass: downgrade заблоковано](logs/33_v1.4.0_downgrade_blocked.log) | [pass: downgrade заблоковано](logs/61_v1.5.0_beta_already_current_and_downgrade_blocked.log) |
| TC-O05 | force reinstall / downgrade | [OTA-4](reference/PRD.md#ota-4) | [pass](logs/37_v1.3.0_force_reinstall_success.log) | [pass](logs/34_v1.4.0_to_v1.3.0_force_success.log) | [fail BUG-OTA03; наступна спроба успішна](logs/66_v1.5.0_force_reinstall_cache_panic.log) |
| TC-O06 | Завершене OTA: неактивний слот, прогрес, success/boot | [OTA-5](reference/PRD.md#ota-5) | [pass](logs/11_v1.3.0_to_v1.4.0_success.log) | [pass](logs/43_v1.4.0_beta_retry_without_reboot_to_v1.5.0.log) | [pass](logs/67_v1.5.0_force_reinstall_success_settings_reset.log) |
| TC-O07 | Обрив OTA: поточна прошивка залишається працездатною | [OTA-6](reference/PRD.md#ota-6) | [pass](logs/04_v1.3.0_ota_interrupted.log) | [pass](logs/42_v1.4.0_beta_interrupted_status_bug.log) | [pass](logs/62_v1.5.0_force_ota_interrupted_and_wifi_status_bug.log) |
| TC-O08 | Реконект → повтор без reboot | [OTA-6](reference/PRD.md#ota-6) | [fail BUG-OTA01](logs/10_v1.3.0_ota_retry_busy_without_reboot.log) | [pass](logs/43_v1.4.0_beta_retry_without_reboot_to_v1.5.0.log) | [fail BUG-OTA01](logs/87_v1.5.0_url_retry_already_in_progress_without_reboot.log) |
| TC-O09 | 7 OTA-команд без Wi-Fi | [OTA-7](reference/PRD.md#ota-7) | [pass](logs/02_color_recheck_sanitized.log) | [pass](logs/28_v1.4.0_ota_without_wifi.log) | [pass](logs/71_v1.5.0_ota_without_wifi.log) |
| TC-O10 | Збереження даних: вихідні переходи з кожної версії | [OTA-8](reference/PRD.md#ota-8) | [→1.4 pass; →1.5 fail BUG-OTA02](logs/97_v1.3.0_to_v1.5.0_direct_url_settings_reset.log) | [fail →1.5: BUG-OTA02](logs/44_v1.5.0_settings_loss_and_wifi_reenrollment.log) | [fail BUG-OTA02](logs/67_v1.5.0_force_reinstall_success_settings_reset.log) |
| TC-O11 | PENDING_VERIFY → self-check → VALID | [OTA-9](reference/PRD.md#ota-9) | [pass](logs/37_v1.3.0_force_reinstall_success.log) | [pass](logs/11_v1.3.0_to_v1.4.0_success.log) | [pass](logs/97_v1.3.0_to_v1.5.0_direct_url_settings_reset.log) |
| TC-O12 | ota broken → rollback, дані/valid | [OTA-10](reference/PRD.md#ota-10) | [pass](logs/95_v1.3.0_ota_broken_rollback.log) | [pass](logs/41_v1.4.0_broken_rollback_and_saved_wifi.log) | [pass, нова scene1 збережена](logs/77_v1.5.0_broken_rollback_scene_1_preserved.log) |
| TC-O13 | Невалідний образ: текстовий файл | [OTA-11](reference/PRD.md#ota-11) | [pass](logs/94_v1.3.0_url_invalid_image_rejected.log) | [pass](logs/89_v1.4.0_saved_wifi_and_url_invalid_image.log) | [pass](logs/79_v1.5.0_url_non_firmware_invalid_image_rejected.log) |
| TC-O14 | Прямий URL; ota-test-server | [OTA-12](reference/PRD.md#ota-12) | [pass](logs/96_v1.3.0_ota_test_server_and_rollback_saved_wifi.log) | [pass](logs/91_v1.4.0_ota_test_server_simulation.log) | [pass](logs/80_v1.5.0_ota_test_server_simulation.log) |

На 1.3/1.4 rainbow та сцени відсутні за PRD: робота функцій — `N/A`, перевірка відповіді `not implemented` — окремий TC-L15. Порожні/невалідні сцени 1.5 та reboot перевірені; точний етап втрати старих сцен після послідовності OTA не локалізований. Образів нижче 1.3 не надано.

## 2. Матриця багів

**10 багів: 5 Major/High, 5 Minor/Medium.** ID відкриває картку з кроками й доказами. «Відтворено» — дефект підтверджено; «не відтворено» — виконаний ретест пройшов. Регресія — дефект, який з’явився або повернувся в новішій версії.

| Баг | Severity / Priority | 1.3 | 1.4 | 1.5 | Регресія |
|---|---|---|---|---|---|
| [BUG-L01](bugs/BUG-L01.md) — Кольори | Minor/Medium | Відтворено | Не відтворено | Не відтворено | Ні |
| [BUG-L02](bugs/BUG-L02.md) — Невалідна яскравість | Minor/Medium | Відтворено | Не відтворено | Не відтворено | Ні |
| [BUG-L03](bugs/BUG-L03.md) — Вплив led rgb | Minor/Medium | Відтворено | Відтворено | Відтворено | Ні |
| [BUG-L04](bugs/BUG-L04.md) — Яскравість blink | Minor/Medium | 10/100 працює | 10/100 працює | Відтворено | Так: на 1.4 blink 10/100 працював |
| [BUG-L05](bugs/BUG-L05.md) — Автовимкнення | Major/High | Не відтворено | Не відтворено | Відтворено | Так: timer 5 працював на 1.4 |
| [BUG-W01](bugs/BUG-W01.md) — Хибний Wi-Fi status | Minor/Medium | Відтворено | Відтворено | Відтворено | Ні |
| [BUG-W02](bugs/BUG-W02.md) — StoreProhibited | Major/High | Одне виникнення | Не перевірено | Не перевірено | Не визначено |
| [BUG-OTA01](bugs/BUG-OTA01.md) — Повтор після обриву | Major/High | Відтворено | Не відтворено | Відтворено | Симптом повернувся на 1.5; OTA-команди різні |
| [BUG-OTA02](bugs/BUG-OTA02.md) — Втрата даних | Major/High | 1.3→1.4: не відтворено | 1.3↔1.4: не відтворено | Відтворено при прийманні / reinstall 1.5 | Так: приймання 1.5 |
| [BUG-OTA03](bugs/BUG-OTA03.md) — Cache panic | Major/High | Не перевірено | Не перевірено | Одне виникнення | Не визначено |

## 3. OTA-переходи

| Перехід / сценарій | Результат | Повний доказ |
|---|---|---|
| 1.3→1.4 stable | pass, valid; параметри/Wi-Fi збережені | [OTA](logs/11_v1.3.0_to_v1.4.0_success.log), [Wi-Fi](logs/12_v1.4.0_saved_wifi_reconnect.log) |
| 1.4→1.3 force | pass, valid; дані збережені | [OTA](logs/34_v1.4.0_to_v1.3.0_force_success.log), [Wi-Fi](logs/35_v1.3.0_saved_wifi_after_downgrade.log) |
| 1.3→1.3 force; повернення →1.4 | pass, valid; дані збережені | [reinstall](logs/37_v1.3.0_force_reinstall_success.log), [повернення](logs/38_v1.3.0_to_v1.4.0_return_success.log), [Wi-Fi](logs/40_v1.4.0_saved_wifi_and_30s_control.log) |
| 1.4→1.5 beta після обриву | Повтор без reboot успішний; збереження даних fail BUG-OTA02 | [OTA](logs/43_v1.4.0_beta_retry_without_reboot_to_v1.5.0.log), [дані](logs/44_v1.5.0_settings_loss_and_wifi_reenrollment.log) |
| 1.5→1.5 force | Аварія BUG-OTA03; повтор успішний/valid, дані втрачено — BUG-OTA02 | [аварія](logs/66_v1.5.0_force_reinstall_cache_panic.log), [повтор](logs/67_v1.5.0_force_reinstall_success_settings_reset.log), [Wi-Fi](logs/70_v1.5.0_saved_wifi_missing_after_reinstall.log) |
| 1.5→1.4 force після reboot | pass, valid; параметри/Wi-Fi збережені | [OTA](logs/88_v1.5.0_to_v1.4.0_force_after_explicit_reboot.log), [Wi-Fi](logs/89_v1.4.0_saved_wifi_and_url_invalid_image.log) |
| 1.4→1.3 force; 1.3 reinstall без force | pass, valid; параметри/Wi-Fi збережені | [downgrade](logs/92_v1.4.0_to_v1.3.0_force_for_remaining_ota_tests.log), [reinstall](logs/93_v1.3.0_same_version_install_without_force.log), [Wi-Fi](logs/94_v1.3.0_url_invalid_image_rejected.log) |
| ota broken: rollback у 1.3 / 1.4 / 1.5 | pass, попередній слот/valid; параметри/Wi-Fi збережені; нова scene1 на 1.5 збережена | [1.3](logs/95_v1.3.0_ota_broken_rollback.log), [Wi-Fi 1.3](logs/96_v1.3.0_ota_test_server_and_rollback_saved_wifi.log), [1.4](logs/41_v1.4.0_broken_rollback_and_saved_wifi.log), [1.5](logs/77_v1.5.0_broken_rollback_scene_1_preserved.log), [Wi-Fi 1.5](logs/78_v1.5.0_rollback_saved_wifi_and_stable_downgrade_blocked.log) |
| Прямий URL broken: rollback у 1.4 / 1.5 | pass; очікуваний self-check FAILED і rollback | [1.4](logs/90_v1.4.0_direct_url_broken_rollback.log), [1.5](logs/84_v1.5.0_direct_url_success_and_broken_rollback_no_interrupt.log) |
| Прямий URL 1.3→1.5 | Оновлення успішне/valid; колір і режим втрачено — BUG-OTA02 | [OTA](logs/97_v1.3.0_to_v1.5.0_direct_url_settings_reset.log) |

## 4. Висновок

**1.5.0 залишити beta:** таймер не вимикає світло (BUG-L05), blink ігнорує яскравість (BUG-L04), OTA втрачає дані (BUG-OTA02), повтор після обриву блокується (BUG-OTA01), зафіксований Cache panic (BUG-OTA03). BUG-L03 і BUG-W01 зберігаються на трьох версіях.

Обмеження: немає незалежних серій 3/3, інструментальних вимірів і годинного відліку 3600 с. Пріоритет активної лампи щодо Wi-Fi не оцінено: на 1.4/1.5 спроби переривали BOD/reset; причину reset не встановлено. Після останнього 1.3→1.5 Wi-Fi/сцени не перевірені. Фото/скриншоти є для частини багів; решта фізичних результатів підтверджена спостереженнями користувача. Повний перелік додаткових доказів — [у logs/INDEX.md](logs/INDEX.md).
