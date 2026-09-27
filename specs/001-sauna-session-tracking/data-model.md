# Data Model: Sauna Session Tracking

All times are stored as UTC instants and rendered in the user's local time zone. Durations are derived from start and end times. Temperatures are stored in Celsius and converted only for display.

## SaunaVisit

| Field | Description | Validation |
|---|---|---|
| `id` | Stable local identifier | Required and unique |
| `status` | `ACTIVE`, `COMPLETED`, or `DISCARDED` | Only one `ACTIVE` visit is permitted |
| `startedAt` | Visit start instant | Required |
| `endedAt` | Visit end instant | Required for `COMPLETED` and `DISCARDED`; must not precede `startedAt` |
| `lastUpdatedAt` | Most recent persisted action or reading | Required; must be at or after `startedAt` |
| `protocolId` | Optional selected protocol | Must reference an available protocol when present |
| `isRitual` | Whether the visit is a sauna ritual | Required, default `false` |
| `heartRateTrackingEnabled` | Whether live HR tracking was enabled | Required |
| `heartRateDeviceLabel` | User-visible monitor name | Optional |

**Relationships**: One visit has ordered `Phase` records and zero or more `HeartRateReading` records. It optionally references one `SaunaProtocol`.

**State transitions**:

```text
ACTIVE -> COMPLETED
ACTIVE -> DISCARDED
```

Ending or discarding an active visit must close any active phase and cold exposure at the chosen end time in one transaction.

## Phase

| Field | Description | Validation |
|---|---|---|
| `id` | Stable local identifier | Required and unique |
| `visitId` | Parent visit | Required; parent must not be discarded |
| `sequence` | Ordering within a visit | Starts at 1; unique within a visit |
| `type` | `HEAT_ROUND` or `RECOVERY_INTERVAL` | Required |
| `startedAt` / `endedAt` | Phase bounds | Start required; end must not precede start |
| `saunaType` | Sauna style or free-text label | Optional; available only for heat rounds |
| `temperatureCelsius` | Measured heat temperature | Optional; must be plausible for a sauna when set |

**Rules**: A visit may have only one active phase. Heat rounds and recovery intervals alternate after the initial heat round. A completed visit must contain at least one completed heat round.

## ColdExposure

| Field | Description | Validation |
|---|---|---|
| `id` | Stable local identifier | Required and unique |
| `recoveryPhaseId` | Parent recovery interval | Required |
| `type` | `COLD_PLUNGE` or `COLD_SHOWER` | Required |
| `startedAt` / `endedAt` | Cold-exposure bounds | Start required; end must not precede start; must occur within its recovery interval when that interval is closed |

**Relationships**: A recovery interval may contain zero or more cold exposures. Multiple exposures are allowed and remain separate.

## HeartRateReading

| Field | Description | Validation |
|---|---|---|
| `id` | Stable local identifier | Required and unique |
| `visitId` | Parent visit | Required |
| `phaseId` | Active heat round or recovery interval at receipt time | Required |
| `recordedAt` | Receipt instant | Required; must fall within the phase's interval when closed |
| `beatsPerMinute` | Parsed heart rate | Integer from 1 through 255 |

**Rules**: Store readings only while the parent visit is active and heart-rate tracking is enabled. A disconnection preserves prior readings and records a connection status for the UI; it must not fabricate later values.

## SaunaProtocol

| Field | Description | Validation |
|---|---|---|
| `id` | Stable local identifier | Required and unique |
| `name` | User-visible protocol name | Required and unique |
| `description` | General routine guidance | Required; includes non-medical disclaimer |
| `targetRounds` | Suggested heat/recovery sequence | Optional positive values |
| `isBuiltIn` | Whether supplied with the app | Required |

The initial built-in set includes a Brian Johnson-inspired protocol. Protocols are guidance only and never enforce a visit's timing.

## UserPreference

| Field | Description | Validation |
|---|---|---|
| `temperatureUnit` | `CELSIUS` or `FAHRENHEIT` display preference | Required; default `CELSIUS` |

## Derived values

- **Heat total**: sum of completed heat-round durations, plus elapsed time for an active heat round in the active tracker only.
- **Recovery total**: sum of completed recovery-interval durations, plus elapsed time for an active recovery interval in the active tracker only.
- **Weekly/monthly count**: completed visits grouped by local completion date.
- **Phase HR change**: last valid BPM minus first valid BPM within a phase.
- **Post-visit recovery**: reading changes in recovery intervals following a heat round; unavailable when the necessary readings do not exist.
- **Cold-exposure comparison**: analytics groups completed visits with at least one cold exposure separately from those without one and labels insufficient data rather than implying a conclusion.
