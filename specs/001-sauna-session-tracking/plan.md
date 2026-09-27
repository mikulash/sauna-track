# Implementation Plan: Sauna Session Tracking

**Branch**: `mikulash-sauna-session-tracking` | **Date**: 2026-09-28 | **Spec**: [spec.md](spec.md)

**Input**: Feature specification from `specs/001-sauna-session-tracking/spec.md`

## Summary

Build an offline-first native Android app in Java for recording multi-round sauna visits, recovery intervals, cold exposure, temperature, protocols, rituals, and calendar summaries. The app will persist visit data locally and optionally receive real-time heart-rate measurements from compatible Bluetooth Low Energy (BLE) heart-rate monitors. A foreground tracking service will keep an explicitly user-started heart-rate session active while the app is backgrounded, and the app will associate readings with the active heat round or recovery interval.

## Technical Context

**Language/Version**: Java 17

**Primary Dependencies**: Android SDK; AndroidX AppCompat, Lifecycle/ViewModel, Navigation, RecyclerView, Room, and WorkManager; Material Components; JUnit; AndroidX Test; Espresso. BLE heart-rate monitoring uses Android platform Bluetooth APIs and the standard GATT Heart Rate Profile rather than a vendor-specific SDK.

**Storage**: Room-backed local SQLite database for visits, phases, cold exposures, heart-rate readings, protocols, and preferences. No account, cloud synchronization, or remote service is in scope.

**Testing**: JUnit unit tests for state, duration, unit conversion, aggregation, and analytics calculations; Room integration tests; Android instrumented tests for persistence and BLE-service lifecycle seams; Espresso end-to-end UI tests using a fake heart-rate source.

**Target Platform**: Android phone, minimum SDK 26 (Android 8.0); compile and target the stable Android SDK required by the Android distribution channel at implementation time.

**Project Type**: Native mobile application

**Performance Goals**: Keep the active tracker responsive at 60 fps; render a saved visit and its summaries in under 1 second for a local history of 1,000 visits; reflect a received BLE heart-rate notification in the active view within 1 second while the app is foregrounded.

**Constraints**: Offline-first and no account required; heart-rate collection is opt-in; Bluetooth runtime permissions must be requested only when needed; tracking outside the app requires a user-visible foreground-service notification; the core timer must remain usable when Bluetooth is unsupported, denied, disconnected, or unavailable.

**Scale/Scope**: One user and one active visit at a time; up to 20 phases and 1,800 stored heart-rate readings per visit (a 3-hour visit sampled once every 6 seconds); local history and analytics for at least 1,000 completed visits.

## Constitution Check

The repository constitution is an uncustomized template with no ratified, project-specific principles or enforceable gates. No constitution violations are identified.

**Pre-design result**: PASS.

## Project Structure

### Documentation (this feature)

```text
specs/001-sauna-session-tracking/
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
│   │   │   │   ├── analytics/
│   │   │   │   ├── timer/
│   │   │   │   └── tracking/
│   │   │   ├── bluetooth/
│   │   │   ├── service/
│   │   │   └── ui/
│   │   │       ├── activevisit/
│   │   │       ├── history/
│   │   │       ├── analytics/
│   │   │       ├── protocols/
│   │   │       └── settings/
│   │   ├── res/
│   │   │   ├── layout/
│   │   │   ├── navigation/
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

**Structure Decision**: Use one native Android application module. Package boundaries separate persistence, deterministic domain calculations, BLE/device integration, foreground-service lifecycle, and screen-specific UI code. This keeps platform concerns out of analytics and timer logic, allowing deterministic unit tests and fakes for device input.

## Complexity Tracking

No constitution violations require justification.

## Post-Design Constitution Check

The completed design remains within the single-app, offline-first scope and introduces no constitution-defined violation.

**Post-design result**: PASS.
