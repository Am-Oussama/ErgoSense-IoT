# ErgoSense: Full Project Description

*A small wearable that reminds you to sit upright and take breaks. Prepared 23 October 2026.*

**Status:** design document. Nothing is built or measured yet. Every number marked "proposed" is a starting value to tune during testing, not a result.

---

## 1. The idea in one paragraph

ErgoSense is a small device worn on the upper back, between the shoulder blades. It measures how far the upper back tilts forward or sideways, notices whether the person is sitting still or moving, and gives a discreet vibration when a slouch lasts too long or when it is time for a break. It works on its own, without a phone or account, and keeps a private log that can be viewed on a local dashboard. It is a posture-awareness and break-reminder tool, not a medical device.

## 2. Who it is for

- Students and desk workers who spend hours at a screen.
- People who want a low-cost reminder before long sitting turns into discomfort.
- Not for diagnosing or treating pain, and not for neck posture (see section 11).

## 3. What the final version looks like

See `ErgoSense_Concept_Illustration.svg`. It is an illustration, not a photograph.

- **Enclosure:** a small rounded case, target size about 45 x 32 x 12 mm (estimate).
- **On the case:** one status LED, one button, one USB-C port, and a small loop for an optional cord.
- **Inside:** XIAO ESP32-C3 board, motion sensor (IMU), LiPo battery, coin vibration motor, transistor and diode driving the motor.
- **Wearing:** two options to compare in testing: a replaceable skin-safe adhesive patch, or a cord and pendant strap.
- **Placement:** upper back, between the shoulder blades.

## 4. How it works (signal flow)

1. **Sense:** the IMU reports tilt and movement many times per second.
2. **Filter:** the firmware combines accelerometer and gyroscope data into a stable tilt angle (complementary filter).
3. **Understand the situation:** a small state machine decides whether the user is sitting still, moving, or reclined.
4. **Decide:** if the tilt stays past the threshold for the set time while the user is sitting still, an alert is due. If the user has been still for too long, a break reminder is due.
5. **Alert:** vibration pattern plus LED feedback.
6. **Log:** the device stores summaries, not raw sensor streams.
7. **Sync:** when Wi-Fi is available, it sends the summaries in short bursts over MQTT to a local dashboard.

Detection and alerts run entirely on the device. Wi-Fi is only for syncing, which also saves battery.

## 5. Modes

### 5.1 User modes (chosen with the button)

| Mode | Purpose | Slouch alert | Break reminder | Proposed start values |
|---|---|---|---|---|
| **Everyday** | General use, tolerant, few interruptions | On | On | 15 degrees, 10 s persistence, break after 45 min still |
| **Focus** | Long study or work sessions, stricter | On | On | 10 degrees, 3 s persistence, break after 30 min still |
| **Silent tracking** | Measure without buzzing, for baselines and tests | Off (logged only) | Off (logged only) | Same thresholds as Everyday, no vibration |
| **Maintenance** *(stretch goal)* | After good weeks, fewer alerts and a weekly summary | Reduced | On | Defined after user testing |

The thresholds are measured from the user's own calibrated upright position, not from vertical. Sources and student projects suggest values around 10 to 15 degrees and delays of a few seconds, so these are reasonable starting points, but they must be tuned.

### 5.2 Automatic states (no user action)

| State | How it is detected (proposed) | What the device does |
|---|---|---|
| **Sitting still (armed)** | Low movement and tilt within normal range | Alerts and break timer active |
| **Moving** | Sustained movement such as walking, standing up, reaching | Alerts paused until about 5 s of stillness |
| **Reclined or lying** | Torso far from calibrated upright (for example beyond roughly 40 degrees) for a sustained time | Alerts paused, so leaning back never counts as slouching |
| **Calibrating** | Triggered by a long button press | Records the new upright reference |
| **Charging / full** | USB power present | LED shows charging, then full |
| **Low battery** | Battery below a set level | LED warning, stops Wi-Fi syncing to save power |
| **Syncing** | Wi-Fi available and data waiting | Short burst upload, then back to low power |
| **Idle / sleep** | No movement for a long time | Low-power sleep, wakes on movement |
| **Error** | For example sensor not found | Long LED signal, safe shutdown of alerts |

### 5.3 Controls

| Action | Result |
|---|---|
| Short press | Cycle mode: Everyday, Focus, Silent tracking |
| Double press | Pause alerts for 30 minutes |
| Long press (about 2 s) | Calibrate: sit upright and hold still |
| Very long press (about 6 s) | Power off / standby |

### 5.4 Feedback vocabulary

| Signal | Meaning |
|---|---|
| Two short pulses | Slouch (forward lean) |
| One long pulse | Side lean |
| Three slow pulses | Time for a break |
| LED blinks 1 / 2 / 3 times after a mode change | Everyday / Focus / Silent tracking |
| LED fast blink, then solid | Calibrating, then done |
| LED slow pulse / solid | Charging / full |
| LED double blink every 20 s | Low battery |

A single LED limits this vocabulary. If testing shows it is confusing, upgrade to an RGB LED.

## 6. Making alerts useful instead of annoying

Alert annoyance is the main complaint in reviews of commercial trainers, so this is the core of the design.

- **Persistence:** alert only after the tilt lasts longer than the delay.
- **Movement gating:** no slouch alerts while the user is moving or reclined.
- **Hysteresis:** an alert clears when the user returns to within about 3 degrees inside the threshold, so it does not flicker at the boundary.
- **Escalation:** if ignored, repeat after about 30 s with a longer or stronger vibration.
- **Cooldown:** after three ignored alerts, stay quiet for about 5 minutes.
- **Sensor pause during vibration:** the motor shakes the sensor, so ignore readings for about 200 ms during the buzz plus about 50 ms to settle. A similar approach is used in the Core Posture project.
- **Break logic:** a break counts as taken when meaningful movement follows a long still period.

## 7. Data and dashboard

**On the device:** summaries per minute or per session (time upright, slouch events, breaks, long-still periods), not raw sensor data.

**On the dashboard (Mosquitto plus Node-RED, Grafana or ThingsBoard):**
- Posture score (share of sitting time within the threshold)
- Slouch events per hour and longest upright streak
- Breaks reminded versus breaks taken
- Hourly pattern across the day and session history
- CSV export

**Privacy design:** no account, no personal profile, no cloud by default. Data goes only to a broker the user controls. Upright's own privacy policy describes collecting profile and health details, so this is a real point of contrast.

## 8. Power

- LiPo battery (250 to 400 mAh, to be tested), charged over USB-C through the board's built-in charger.
- A rough estimate suggests 7 to 9 hours on a 250 mAh cell, so an 8-hour target is borderline. Real life is unknown until measured.
- Savings: batch Wi-Fi uploads, use sleep between samples, and consider a lower-power IMU later.

## 9. Failure behavior

- **Wi-Fi lost:** detection and logging continue, and data syncs later.
- **Low battery:** warn the user, stop syncing, keep core alerts as long as possible.
- **Bad calibration:** if the reference looks implausible, ask the user to calibrate again.
- **Stuck mode:** one reviewer reported a commercial device stuck in the wrong mode for a day, so the mode is always shown by the LED after any press, and the device returns to Everyday after a restart.

## 10. Validation plan

| Test | What it shows | Proposed target |
|---|---|---|
| Angle accuracy against a reference (phone inclinometer or protractor) | Sensor and filter quality | Error within 3 to 5 degrees |
| Naive threshold vs ErgoSense logic in scripted scenarios (sitting, slouching, walking, standing up, reaching, reclining) | Whether movement gating reduces false alerts | Report false alerts, misses and latency |
| Fixed 30-minute timer vs movement-aware break reminder | Whether the smarter reminder beats a simple timer | Report unnecessary and missed reminders |
| Small user test (5 to 8 people, alerts on vs silent tracking) | Posture time and annoyance | Report honestly with the small sample |
| Battery and offline test | Real battery life and offline operation | At least 8 hours is the goal |

## 11. Limits and honest claims

- **No neck sensing.** One upper-back sensor cannot measure head position. Forward head posture would need a second sensor on the neck (future work).
- **Not a medical device.** Studies of wearable posture feedback show small short-term posture changes, and no clear pain benefit. Describe it as awareness and reminders.
- **MPU6050** (the prototype sensor) is discontinued by its maker, so the driver should be easy to swap.
- **Estimates:** sizes, battery life and cost figures are estimates until measured.

## 12. Build stages

1. Sensor, filter and calibration working on the bench.
2. Slouch detection with delay, thresholds and the vibration motor.
3. Movement and reclined states, then the comparison against a naive version.
4. Break reminders and escalation logic.
5. Session log, MQTT sync and dashboard.
6. Enclosure, wearing methods, battery tests and the user test.
7. Stretch goals: maintenance mode, Telegram notifications, auto mode switching.

## 13. Files in this project folder

- `ErgoSense_Comparison.md`: comparison with Upright and DIY projects
- `ErgoSense_Component_Choices.md`: why this board, motor and sensor
- `ErgoSense_Parts_List.md` and `.pdf`: parts list
- `ErgoSense_Concept_Illustration.svg`: illustration of the final version
