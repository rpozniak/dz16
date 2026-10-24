# BUG-W01 — Хибний connected після втрати AP

**Версії:** 1.3 / 1.4 / 1.5.  
**Severity / Priority:** Minor / Medium.  
**PRD:** [FR-W4](../reference/PRD.md#fr-w4).

**Передумови:** Пристрій підключений до хотспота та має IP.

**Кроки:**

1. Запустити OTA: `ota update` на 1.3, `ota update beta` на 1.4, `ota install 1.5.0 force` на 1.5.
2. Під час progress вимкнути хотспот, дочекатися download interrupted і невдалого реконекту.
3. Виконати `status`.

**Очікувано:** `WiFi: disconnected`.

**Фактично:**

`connected` з порожнім SSID, IP 0.0.0.0, RSSI 0. Поточна прошивка працює; після успішного реконекту статус коректний.

```text
WiFi: connected
  SSID:
  IP:   0.0.0.0
  RSSI: 0 dBm
```

**Відтворюваність:** Два спостереження на 1.3, по одному на 1.4/1.5; загальну частоту не визначено.

**Докази:** [1.3](../logs/04_v1.3.0_ota_interrupted.log), [1.4](../logs/42_v1.4.0_beta_interrupted_status_bug.log), [1.5](../logs/62_v1.5.0_force_ota_interrupted_and_wifi_status_bug.log). 
