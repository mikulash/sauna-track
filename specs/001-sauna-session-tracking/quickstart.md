# Quickstart: Validate Sauna Session Tracking

## Prerequisites

- Android Studio with a current stable Android SDK and Java 17 toolchain.
- An Android device or emulator meeting the app's minimum Android version.
- For live HR validation, a physical Android device with Bluetooth enabled and a compatible BLE Heart Rate Profile monitor.

## Build and automated validation

From the repository root, run:

```powershell
.\gradlew test
.\gradlew connectedAndroidTest
```

Expected outcome: timer state, duration, temperature conversion, summary, analytics, and persistence tests pass; instrumented tests validate active-visit transitions and the UI's graceful no-monitor behavior.

## End-to-end validation scenarios

1. Start a visit, complete two heat rounds separated by a recovery interval, add a cold plunge, and end the visit. Confirm the detail view lists the ordered rounds and interval, cold plunge, total heat time, and total recovery time.
2. Set a heat-round temperature in Celsius, change the preference to Fahrenheit, and review the saved round. Confirm the displayed value converts while the original measurement is unchanged when switching back.
3. Start a visit, leave it open, return after closing/reopening the app, and confirm the active view displays its last-update time and lets the user resume, end, or discard it.
4. Complete visits on both sides of a week and month boundary. Confirm the weekly and monthly counts use each visit's local completion date.
5. Enable heart-rate tracking, select a compatible monitor, complete a heat round and recovery interval, and end the visit. Confirm live BPM is shown during tracking and stored readings appear in phase analytics.
6. Repeat the preceding scenario after denying Bluetooth access or turning off the monitor. Confirm the visit remains recordable, earlier readings remain visible when applicable, and the UI clearly states that later readings are unavailable.
7. Select a protocol, complete a visit, mark it as a ritual, and confirm both values appear in visit history and details.

## Contract and data references

- User-visible behavior: [UI contract](contracts/ui-contract.md)
- Persistence, validation, and state transitions: [data model](data-model.md)
- Technology decisions and runtime constraints: [research](research.md)
