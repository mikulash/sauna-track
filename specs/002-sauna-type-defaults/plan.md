# Implementation Plan: Sauna Type Defaults

**Branch**: `mikulash-sauna-session-tracking` | **Date**: 2026-09-28 | **Spec**: [spec.md](spec.md)

**Input**: Feature specification from `specs/002-sauna-type-defaults/spec.md`; heart-rate monitors must be supported through Bluetooth or ANT+.

## Summary

Build the sauna-type catalogue and heat-round temperature behavior in the native Java Android app. The feature presents Finnish sauna, infrared sauna, and steam room; supplies a stored default temperature when the user selects a type; and preserves actual manual measurements and historical labels. The surrounding app's heart-rate integration will use a transport-neutral source that supports both Bluetooth LE and ANT+ so heat-round records can later be correlated with real-time heart-rate data without coupling sauna-type logic to a radio protocol.

## Technical Context

**Language/Version**: Java 17

**Primary Dependencies**: Android SDK; AndroidX AppCompat, Lifecycle/ViewModel, Navigation, RecyclerView, Room, and Material Components; JUnit; AndroidX Test; Espresso. Heart-rate integration boundary: Android Bluetooth LE APIs for standard Heart Rate Profile devices and the official ANT+ Android SDK PluginLib for ANT+ devices.

**Storage**: Room-backed local SQLite database for sauna types, heat rounds, temperature preference, and historical snapshots of type label/default context. Heart-rate source metadata remains owned by the session-tracking feature.

**Testing**: JUnit tests for default application, manual override, type switching, historical snapshot, and Celsius/Fahrenheit conversion; Room integration tests for persisted catalogue and heat rounds; Espresso UI tests for type selection and manual-value confirmation; contract tests for fake Bluetooth LE and ANT+ heart-rate sources in the shared tracking boundary.

**Target Platform**: Android phone, minimum SDK 26 (Android 8.0); compile and target the stable Android SDK required by the Android distribution channel at implementation time.

**Project Type**: Native mobile application

**Performance Goals**: Show a selected sauna type and its default temperature within 250 ms of user selection; render a saved heat round's type and temperature within 1 second from local history; receive and surface a connected monitor's heart-rate reading within 1 second.

**Constraints**: Offline-first and no account required; temperature defaults are suggestions rather than safety limits; manual temperature input is never silently overwritten; heart-rate collection is optional and must continue to degrade gracefully when either Bluetooth or ANT+ support is unavailable.

**Scale/Scope**: One local user; initial catalogue of three built-in sauna types; one active visit and heat round at a time. User-defined sauna types and editable global defaults are out of scope for this feature. The shared HR boundary supports one selected monitor source per active visit.

## Constitution Check

The repository constitution remains an uncustomized template with no ratified, project-specific principles or enforceable gates. No constitution violations are identified.

**Pre-design result**: PASS.

## Project Structure

### Documentation (this feature)

```text
specs/002-sauna-type-defaults/
├── plan.md
├── research.md
├── data-model.md
├── quickstart.md
├── contracts/
│   └── ui-contract.md
└── tasks.md                 # Created later by /speckit-tasks
```

### Source Code (repository root)

```text
app/
├── src/
│   ├── main/
│   │   ├── java/com/mikulash/saunatrack/
│   │   │   ├── data/
│   │   │   │   ├── local/
│   │   │   │   ├── repository/
│   │   │   │   └── model/
│   │   │   ├── domain/
│   │   │   │   ├── temperature/
│   │   │   │   └── tracking/
│   │   │   ├── heartrate/
│   │   │   │   ├── bluetooth/
│   │   │   │   ├── antplus/
│   │   │   │   └── HeartRateSource.java
│   │   │   └── ui/
│   │   │       └── activevisit/
│   │   ├── res/
│   │   │   ├── layout/
│   │   │   └── values/
│   │   └── AndroidManifest.xml
│   ├── test/java/com/mikulash/saunatrack/
│   └── androidTest/java/com/mikulash/saunatrack/
├── build.gradle
└── proguard-rules.pro

build.gradle
settings.gradle
gradle.properties
```

**Structure Decision**: Use a single native Android application module. Sauna types, temperatures, and heat-round state live in the data and domain layers; the active-visit UI renders their state. Heart-rate acquisition is behind a shared `HeartRateSource` boundary with separate Bluetooth LE and ANT+ implementations so radio protocol code cannot affect type/default behavior.

## Complexity Tracking

No constitution violations require justification.

## Post-Design Constitution Check

The design remains a single offline-first app. Dual heart-rate transport support is isolated behind one interface because the user explicitly requires Bluetooth or ANT+ compatibility; it does not introduce an additional deployable service or feature module.

**Post-design result**: PASS.
