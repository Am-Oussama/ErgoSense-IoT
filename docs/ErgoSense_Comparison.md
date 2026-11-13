# ErgoSense: Comparison with Existing Solutions

*Research date: 23 October 2026. Prices and specs change, so re-check before presenting.*

## Legend

- **X** = has the capability
- **~** = partial or manual
- **–** = not found / not available
- The **ErgoSense column shows design targets**. Nothing is built or measured yet.
- "DIY projects reviewed" means a small sample (IEEE-VIT *posture-correct*, the *Core Posture* project and one student report). It is indicative, not a market survey.

## Comparison table

| Capability | Upright GO S | Upright GO 2 | DIY projects reviewed | ErgoSense (target) |
|---|---|---|---|---|
| Vibration alert on slouch | X | X | ~ (LED/buzzer common) | X |
| Calibration to your own posture | X | X | ~ | X |
| Adjustable delay / sensitivity | X | X | – | X |
| Silent tracking mode | X | X | – | X |
| On-device storage, sync later | X (7 days) | X (30 days) | – | X |
| Automatic movement handling | ~ (manual presets) | ~ (manual presets) | – | X (to be validated) |
| Break / sedentary reminders | – | – | ~ (hydration timer in one project) | X |
| Account-free, local data | – | – | X | X |
| Open, user-editable thresholds | – | – | X | X |
| Published measured validation | ~ (self-reported stats, small studies) | ~ | – | X (planned tests) |
| Wearable-ready (battery, charging, wearing method) | X | X | – (breadboards) | ~ (prototype) |
| Neck / head sensing | – | – | ~ (flex sensor in one project) | – (stated limitation) |
| Phone app and statistics | X | X | – | ~ (local dashboard) |
| Price | $59 | $79 | Parts only | BOM about $12 to 20 (estimate, replace with local quotes) |

## Evidence notes

- **Upright tuning:** Upright's help material describes an adjustable delay (0 to 30 seconds), a slouch range setting, and trainers matched to activity level that the user selects manually.
- **Upright storage and sensors:** the store pages say the GO S has one sensor, 3 feedback plans and 7 days of on-device storage. The GO 2 has two sensors with a gyroscope, 9 plans and 30 days of storage. Both sync on reconnect.
- **Upright prices (23 Oct 2026):** $59 (GO S) and $79 (GO 2), with list prices of $79.95 and $99.95. Non-US buyers pay customs fees.
- **Upright claims:** the "54% pain reduction" figure on the store page comes from user-reported reviews, not a controlled trial. The Science page cites a small number of studies.
- **Upright data practices (privacy policy, revised July 2022, provided by the team):** the policy lists registration and health information, training-plan details (such as pain level and posture level) and device values (posture score, movement level, accelerometer values and smooth angle). It also mentions analytics providers and processing in the US. The wording is permissive ("may collect"), so this describes permissions, not proven behavior.
- **Upright neck limitation (Reddit thread, anecdotal):** one long-term user reports the device responds to upper-back slouch only and does not help with forward head posture.
- **DIY projects:** *posture-correct* (IEEE-VIT) uses an ESP32, an MPU6050 and a flex sensor, with threshold-based classification and LED/buzzer alerts. *Core Posture* uses an ESP32-C3, an MPU6050, a coin vibration motor and a fixed 15-degree threshold, with a desk display and an hourly hydration reminder.

## Limits of this comparison

- No Upright product was bought or tested by us. Everything about Upright comes from its pages, reviews and the team's pasted sources.
- Upright's own pages disagree in places (battery claims, charging port, water resistance), so treat their figures as marketing claims.
- Reddit evidence is a single thread and is anecdotal.
- We found no independent clinical evidence that posture-reminder devices prevent pain. Studies of wearable biofeedback show small short-term posture changes. ErgoSense should be described as a posture-awareness and break-reminder tool, not a treatment.

## Sources

- Upright store: https://store.uprightpose.com/ , https://store.uprightpose.com/products/upright-go2 , https://store.uprightpose.com/products/upright-go-s
- Upright help (trainer settings): https://intercom.help/upright-go/getting-started/trainer
- Upright Science page: https://www.uprightpose.com/science/
- TechRadar review: https://www.techradar.com/reviews/upright-go-2
- IEEE-VIT posture-correct: https://github.com/IEEE-VIT/posture-correct
- Core Posture (CircuitDigest): https://circuitdigest.com/microcontroller-projects/core-posture-smart-iot-posture-corrector-and-hydration-assistant
- Upright privacy policy and Reddit thread: pasted by the team (not independently re-fetched)
