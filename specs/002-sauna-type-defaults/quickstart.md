# Quickstart: Validate Sauna Type Defaults

## Prerequisites

- Android Studio with a current stable Android SDK and Java 17 toolchain.
- An Android device or emulator meeting the app's minimum Android version.
- For Bluetooth LE validation: a physical Android device with Bluetooth enabled and a compatible BLE heart-rate monitor.
- For ANT+ validation: a physical Android device with ANT Radio Service and ANT+ Plugins Service (or compatible ANT hardware), plus an ANT+ heart-rate monitor.

## Build and automated validation

From the repository root, run:

```powershell
.\gradlew test
.\gradlew connectedAndroidTest
```

Expected outcome: all sauna-type, temperature-origin, conversion, persistence, and monitor-source contract tests pass.

## End-to-end validation scenarios

1. Start a heat round, select Finnish sauna, infrared sauna, and steam room in turn before editing temperature. Confirm each selection applies its configured default in the selected unit.
2. Select a sauna type, replace the default with a measured temperature, select another type, and choose both confirmation paths in separate attempts. Confirm **Keep measurement** preserves the entered value and **Use default** replaces it.
3. Complete a heat round, change the global temperature unit, and review history. Confirm type label and saved temperature are preserved while the display unit converts.
4. Enter a temperature outside the type's typical range. Confirm the app requires confirmation but preserves the entered temperature after confirmation.
5. On a BLE-capable device, connect a compatible monitor and confirm a received BPM is tagged as Bluetooth LE and associated with the active phase.
6. On ANT+-capable hardware, connect a compatible monitor and confirm the same active view and stored reading behavior with an ANT+ transport tag.
7. Attempt ANT+ setup on a device without supported ANT+ services/hardware. Confirm the app explains the unavailable capability and leaves Bluetooth LE and timer-only tracking available.

## References

- Sauna-type state, defaults, and source-selection shape: [data model](data-model.md)
- User-visible state and choices: [UI contract](contracts/ui-contract.md)
- Decisions behind defaults and dual transport: [research](research.md)
