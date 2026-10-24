# BUG-OTA01 — Повтор OTA після обриву заблокований

**Версії:** 1.3 і 1.5; на 1.4 один сценарій пройшов.  
**Severity / Priority:** Major / High.  
**PRD:** [OTA-6](../reference/PRD.md#ota-6).

**Передумови:** Wi-Fi connected; 1.3 valid (ota update) або 1.5 valid (прямий URL broken bin).

**Кроки:**

1. Запустити OTA та вимкнути хотспот під час progress; дочекатися download interrupted.
2. Увімкнути хотспот, виконати saved connect і дочекатися IP.
3. Без reboot повторити ту саму OTA-команду.

**Очікувано:** Повторне завантаження починається без перезавантаження.

**Фактично:**

`update already in progress` після успішного реконекту; нового progress немає. Між обривом і повтором reboot відсутній. На 1.4 повтор beta OTA без reboot успішно встановив 1.5.

```text
I (469586) wifi station: got ip:172.20.10.7
E (490526) wifi station: [OTA] error: update already in progress
```

**Відтворюваність:** По одному завершеному негативному сценарію на 1.3/1.5; один позитивний на 1.4. Симптом повернувся на 1.5; команди й цілі відрізнялися.

**Докази:** [повний сценарій 1.3](../logs/10_v1.3.0_ota_retry_busy_without_reboot.log), [pass 1.4](../logs/43_v1.4.0_beta_retry_without_reboot_to_v1.5.0.log), [повний сценарій 1.5](../logs/87_v1.5.0_url_retry_already_in_progress_without_reboot.log). [Скриншот 1.3](../logs/evidence/v1.3.0_OTA01_retry_already_in_progress.png).
