# Research: Sauna Type Defaults and HR Transport

## Built-in sauna type defaults

**Decision**: Seed three built-in types with Celsius canonical defaults: Finnish sauna at 80 C, infrared sauna at 55 C, and steam room at 45 C. Defaults are provided as editable-at-the-round suggestions only; the app does not position them as safety guidance.

**Rationale**: These values give users a useful starting value appropriate to each category while preserving the requirement to capture actual conditions. Storing canonical Celsius values makes historical conversion consistent.

**Alternatives considered**:

- Requiring every temperature to be entered manually: rejected because it adds repeated friction and does not meet the default-temperature requirement.
- Treating defaults as validation limits: rejected because sauna conditions vary and could incorrectly block accurate recording.
- Storing separate Celsius and Fahrenheit values: rejected because it risks divergent values; one canonical value converts accurately for display.

## Manual-temperature protection

**Decision**: Track the origin of a heat round's current temperature as `TYPE_DEFAULT` or `USER_MEASURED`. Selecting a type replaces only a `TYPE_DEFAULT` value. If a user-measured value exists, show an explicit keep/apply-default choice before changing it.

**Rationale**: The source flag makes replacement deterministic and protects actual measured data from accidental loss.

**Alternatives considered**:

- Always replacing the temperature upon type change: rejected because it destroys a measurement.
- Never replacing the temperature: rejected because a type change before data entry would leave an irrelevant default.

## Bluetooth LE heart-rate transport

**Decision**: Support monitors implementing the standard Bluetooth LE GATT Heart Rate Profile: service `0x180D`, Heart Rate Measurement characteristic `0x2A37`, delivered as notifications.

**Rationale**: BLE is available on modern Android phones and supported by common consumer and fitness heart-rate monitors. Standard-profile support avoids a vendor-specific dependency.

**Alternatives considered**:

- Vendor-specific Bluetooth SDKs: rejected because they narrow compatibility.
- Polling for measurements: rejected because the profile publishes real-time notifications.

## ANT+ heart-rate transport

**Decision**: Add an ANT+ implementation through the official ANT+ Android SDK PluginLib and Heart Rate device profile. Detect ANT+ availability through the SDK and present ANT+ pairing only when the required ANT Radio Service and ANT+ Plugins Service, or compatible ANT hardware/dongle, are available.

**Rationale**: ANT+ is common in performance-sport sensors and allows compatible users to use their existing monitors. It requires Android-device support or compatible hardware, so it is an optional capability rather than a baseline dependency.

**Alternatives considered**:

- Bluetooth LE only: rejected because it excludes the explicitly requested ANT+ monitors.
- Native low-level ANT radio integration: rejected because the official SDK already provides device discovery and heart-rate profile support.

## Unified HR source

**Decision**: Define a transport-neutral `HeartRateSource` contract that exposes availability, discovery, connection state, device label, incoming timestamped BPM readings, disconnect, and cleanup. Implement Bluetooth LE and ANT+ adapters behind it; allow the user to choose the available transport/device for an active visit.

**Rationale**: Analytics and heat-round tracking receive the same reading shape regardless of radio protocol. Fakes can test state and analytics without Bluetooth or ANT+ hardware.

**Alternatives considered**:

- Separate UI/analytics flows for each transport: rejected because it duplicates behavior and risks inconsistent saved readings.
- Connecting to two sources simultaneously: rejected for initial scope because it creates ambiguous readings; exactly one source is selected per active visit.
