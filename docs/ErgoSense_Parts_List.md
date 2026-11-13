# ErgoSense: Parts List

*A single upper-back posture and break-reminder wearable. Prepared 23 October 2026. Capacities and prices are estimates to confirm with local suppliers.*

---

## 1. Core chips

| # | Part | What it does |
|---|---|---|
| 1 | **XIAO ESP32-C3** | The brain: reads the sensor, runs the slouch and movement logic, and sends data over Wi-Fi/MQTT. It also charges the battery and has BLE. |
| 2 | **MPU6050 breakout** (prototype IMU) | Measures tilt and movement of the upper back (accelerometer plus gyroscope). |
| 3 | **Coin vibration motor (ERM), 3 V** | Gives the discreet buzz that tells the user to sit up or take a break. |
| 4 | **NPN transistor** (2N2222, BC547 or S8050) | Switches the motor, because the ESP32 pin cannot supply the motor's current directly. |

## 2. Supporting components

| # | Part | What it does |
|---|---|---|
| 5 | **Flyback diode** (1N4001 or similar) | Protects the transistor and the board from voltage spikes when the motor switches off. |
| 6 | **Base resistor** (about 1 kΩ) | Limits the current from the ESP32 pin into the transistor. |
| 7 | **LiPo battery, 3.7 V, 250 to 400 mAh, with protection circuit** | Powers the device for a work day. The capacity range is an estimate, so test it. |
| 8 | **One push button** | Handles calibration and mode changes, with no phone needed. |
| 9 | **One LED (with resistor)** | Shows the current mode, battery state and calibration, so the user always knows what state the device is in. |
| 10 | **USB-C cable** | Charges the battery through the XIAO's built-in charger and programs the board. |

## 3. Mechanical

| # | Part | What it does |
|---|---|---|
| 11 | **Small enclosure** (3D-printed or off-the-shelf case) | Holds the board, battery and sensor in one wearable piece. |
| 12 | **Wearing method:** skin-safe adhesive patches and a cord or pendant strap | Keeps the sensor fixed on the upper back, and lets you compare both methods in testing. |

## 4. Software side (no chip, but required)

| # | Part | What it does |
|---|---|---|
| 13 | **Laptop or Raspberry Pi running an MQTT broker** (e.g. Mosquitto) | Receives the session data from the device. |
| 14 | **Dashboard tool** (Node-RED, Grafana or ThingsBoard) | Shows posture score, breaks and trends. |

## 5. Optional upgrades

| Part | What it does |
|---|---|
| **DRV2605L haptic driver** | Gives richer, clearly different vibration patterns if the simple buzz is hard to tell apart. |
| **BMI270 or ST LSM6DSO breakout** | A lower-power replacement for the MPU6050 if the battery target fails. |
| **Second IMU** | Future work only, for neck sensing. |

---

## Buying notes

- For the MPU6050, buy a breakout that already has pull-up resistors and a voltage regulator, which most do.
- Check local prices and delivery for items 1, 2 and 7 first, because they are the most likely to be slow to arrive.
- The MPU6050 is discontinued by its maker, so write the sensor driver so the part can be swapped later.
- Battery life is not guaranteed: measure it, and use the 250 to 400 mAh range only as a starting point.
