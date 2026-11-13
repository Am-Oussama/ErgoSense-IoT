# ErgoSense: Component Choices and Justification

*Research date: 23 October 2026. All figures come from vendor pages, datasheets and datasheet summaries, not from our own measurements. Confirm them against the exact board and breakout purchased.*

Each table compares the chosen part against realistic alternatives, and ends with an honest note on what would change the decision.

---

## Table 1: Development board

| Criterion | **XIAO ESP32-C3** (chosen) | Classic ESP32 dev kit | XIAO nRF52840 Sense |
|---|---|---|---|
| Wi-Fi (direct MQTT) | Yes, plus BLE 5.0 | Yes | No (BLE, NFC, Zigbee only) |
| Size | 21 x 17.5 mm | Larger dev-kit board (typical) | 21 x 17.5 mm |
| Onboard LiPo charging | Yes, 50/100 mA | Usually not (varies by board) | Yes (BQ25101) |
| Onboard IMU | No (external sensor) | No | Yes (LSM6DS3TR-C) |
| Wi-Fi light-sleep, chip level (Espressif) | About 130 uA | About 0.8 mA | n/a |
| Deep sleep / standby | 44 uA (board level) | Not compared | Under 5 uA |
| Board-level Wi-Fi figures | About 75 mA active, 25 mA modem-sleep, 4 mA light-sleep | n/a | n/a |
| Approximate price | Low (confirm locally) | Low | About EUR 14 to 20 seen at European shops |
| Fit with the IoT course goal | Best: Wi-Fi/MQTT without a gateway | Good, but bulkier | Needs a phone or gateway for MQTT |

**Why the ESP32-C3:** it is the smallest option that gives direct Wi-Fi/MQTT (the IoT requirement), onboard battery charging and BLE.

**Honest note:** the nRF52840 Sense is the better pure wearable (lower standby current, onboard IMU). We would switch to it if the battery test misses the 8-hour target, or if the Wi-Fi/MQTT requirement is dropped.

**Battery caution (our estimate):** with the CPU awake at roughly 20 to 25 mA plus the IMU, a 250 mAh cell lasts about 7 to 9 hours, so the 8-hour target is borderline. Duty-cycle Wi-Fi, batch uploads, use light sleep between samples, and measure real battery life.

---

## Table 2: Actuator (vibration)

| Criterion | **Coin ERM + transistor** (chosen) | ERM or LRA + DRV2605L driver | Buzzer / LED |
|---|---|---|---|
| Discreet | Yes | Yes | No (audible or visible) |
| Cost | Low | Higher (extra IC and board) | Very low |
| Pattern quality | Simple on/off timing | 123 built-in effects, auto-calibration, supports ERM and LRA | Tones or blinks |
| Response | 20 to 50 ms to reach strength, even with overdrive | LRA about 10 ms to start, up to 300 ms to stop | Instant |
| Power | About 60 to 100 mA at full speed, so it needs a transistor | Lower for the same feel (LRA) | Low |
| Complexity | Lowest: one GPIO, transistor and flyback diode | I2C driver and library | Lowest |

**Why ERM + transistor:** it is the cheapest discreet option, and three patterns made from timing (slouch, lean, break reminder) are enough for the first version. The Core Posture project uses the same approach.

**Honest note:** we would move to the DRV2605L if user testing shows the patterns are hard to tell apart.

**Design lesson:** vibration shakes the IMU. Pause sensor reads during each buzz and wait a short settling time afterward. Core Posture does this with a roughly 200 ms pause plus about 50 ms settling.

---

## Table 3: Sensor (IMU)

| Criterion | **MPU6050** (chosen for prototype) | BMI270 | LSM6DSO-class (ST) |
|---|---|---|---|
| Type | 6-axis IMU | 6-axis IMU | 6-axis IMU |
| Operating current | About 3.9 mA | 685 uA typical | About 0.55 mA (LSM6DSO32) |
| Built-in motion detection | Basic (verify in datasheet) | Any-motion, no-motion, stationary detect | Embedded machine-learning core |
| Lifecycle | Discontinued, not recommended for new designs | Current | ST's family is still recommended for new designs (per ST community) |
| Setup effort | Easiest: libraries and tutorials everywhere; used by every DIY project we reviewed | Needs an 8 kB firmware image loaded at every power-on | Standard libraries |
| Local availability | Very common | Check | Check |

**Why MPU6050 for the prototype:** it is the fastest route to a working, tested algorithm, with the most tutorials. Our needs are modest (tilt and movement).

**Honest note:** it is discontinued and uses the most current of the three. By our arithmetic, 3.9 mA is roughly 16% of an estimated 24 mA total budget, so a swap to a lower-power part could save about 3 mA (around 13%). Write the sensor driver so it can be swapped.

**We would switch** to the BMI270 or an ST part if the battery target fails, or if the angle-accuracy test (target within 3 to 5 degrees) shows the MPU6050 is not good enough.

---

## Sources

- Seeed Studio XIAO nRF52840 Sense: https://wiki.seeedstudio.com/XIAO_BLE/ and retailer pages (Pimoroni, Kiwi Electronics, RobotShop)
- Seeed Studio XIAO ESP32-C3 product and wiki pages (power figures)
- Espressif ESP-AT sleep examples (ESP32-C3): https://docs.espressif.com/projects/esp-at/en/release-v2.3.0.0_esp32c3/AT_Command_Examples/sleep_at_examples.html
- Texas Instruments haptics notes: https://ti.com/lit/SLOA194 and https://ti.com/lit/pdf/ssztap9
- Haptic motor overview with ERM current figures: https://zbotic.in/haptic-motor-erm-lra-vibration-feedback-in-arduino-projects/
- TDK InvenSense MPU-6050 lifecycle notice: https://adm.invensense.tdk.com/mpu-6050-not-new-design
- Bosch BMI270 datasheet: https://cdn.sparkfun.com/assets/9/a/2/9/6/bst-bmi270-ds000.pdf
- IMU comparison summary (MPU-6050, LSM6DSO32 currents): https://www.semicone.com/products/bosch-sensortec-bmi270.html
- Core Posture project: https://circuitdigest.com/microcontroller-projects/core-posture-smart-iot-posture-corrector-and-hydration-assistant
