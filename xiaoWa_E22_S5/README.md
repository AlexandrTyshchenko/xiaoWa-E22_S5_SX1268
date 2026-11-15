# xiaoWa E22 S5 — xiaoWa з модулем Ebyte E22 (стара ревізія, SX1268)

Прошивка 1 в 1 як для xiaoWa E22P (той самий код, сенсори, радіопараметри, GPS, AGC, `SX126X_REGISTER_PATCH`), з двома відмінностями:

| | E22P | E22 S5 |
|---|---|---|
| Потужність чипа за замовчуванням | 12 dBm | **22 dBm** |
| Обмеження `LORA_TX_POWER_MAX` | 12 | **22** |
| Дільник батареї | 1 МОм / 1 МОм, множник 1.758 | **10 кОм (B+) / 15 кОм (B−), множник 1.465** |

Модуль — стара ревізія Ebyte E22 з чипом SX1268 і підсилювачем.

Радіо: 433.650 МГц / 62.5 кГц / SF8 / CR8.

## Файли

| Файл | Роль |
|---|---|
| `Faketec_E22S5_repeater.*` | repeater, ім'я `E22 Repeater`, пароль адміністратора `password` (змініть) |
| `Faketec_E22S5_sensor.*` | sensor, ім'я `E22 Sensor`, пароль `password` (змініть) |
| `Faketec_E22S5_companion_radio_ble.*` | companion BLE, PIN `123456` |

`.uf2` — подвійний reset і копіювання на диск; `.zip` — DFU по USB або OTA (`start ota`); `.hex` — для програматора.

## Потужність

- За замовчуванням `LORA_TX_POWER=22`.
- `LORA_TX_POWER_MAX=22`: `set tx` понад 22 відповідає `Error, max is 22`, збережене раніше більше значення обрізається до 22.
- Якщо на платі раніше стояла прошивка E22P, у налаштуваннях збережено `tx 12`. Нова прошивка його не змінює: поставте `set tx 22` вручну.

## Батарея

Дільник 10 кОм до «+» і 15 кОм до «−» (маркування `103` / `153`): множник = 3600/4096 × (10 + 15) / 15 = **1.465**. Уточнити за тестером: `set adc.multiplier <поточний × тестер / показ ноди>`.

Дільник з малим опором постійно бере з батареї ≈ 0.17 мА (≈ 4 мА·год на добу).

Якщо раніше вручну задавали `set adc.multiplier`, збережене значення має пріоритет над значенням зі збірки: `set adc.multiplier 0` повертає 1.465.

## Збірка

Репозиторій: https://github.com/AlexandrTyshchenko/xiaoWa-E22_S5_SX1268, гілка `xiaowa-e22-s5` (на основі `xiaowa-e22p`). Збірки — у `variants/fakesolar_sx1268/platformio.ini`:

```
pio run -e Faketec_E22S5_repeater -e Faketec_E22S5_sensor -e Faketec_E22S5_companion_radio_ble
python bin/uf2conv/uf2conv.py .pio/build/<env>/firmware.hex -c -f 0xADA52840 -o <env>.uf2
```

## Перевірено

Лише збірка. На залізі не перевірено.
