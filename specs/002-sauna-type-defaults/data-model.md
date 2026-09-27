# Data Model: Sauna Type Defaults

Store canonical temperatures in Celsius. Convert only at display/input boundaries according to the user's preference.

## SaunaType

| Field | Description | Validation |
|---|---|---|
| `id` | Stable local identifier | Required and unique |
| `name` | User-visible type label | Required and unique among available types |
| `defaultTemperatureCelsius` | Default suggested temperature | Required |
| `typicalMinimumCelsius` / `typicalMaximumCelsius` | Informational expected range | Required; minimum must not exceed maximum |
| `isAvailable` | Whether selectable for new rounds | Required; built-ins default to `true` |

Initial seed data:

| Sauna type | Default | Typical range |
|---|---:|---:|
| Finnish sauna | 80 C | 60 C to 110 C |
| Infrared sauna | 55 C | 40 C to 65 C |
| Steam room | 45 C | 40 C to 50 C |

The range prompts the user to confirm an unusual entry. It never prevents saving a temperature.

## HeatRound

| Field | Description | Validation |
|---|---|---|
| `id` | Stable local identifier | Required and unique |
| `visitId` | Parent sauna visit | Required |
| `saunaTypeId` | Selected active type | Optional; references an available type when present |
| `saunaTypeLabelSnapshot` | Type label preserved at save time | Required when a type is selected |
| `temperatureCelsius` | Actual or suggested round temperature | Optional |
| `temperatureOrigin` | `TYPE_DEFAULT` or `USER_MEASURED` | Required when temperature is present; absent otherwise |

**Rules**:

- Select a type for a temperature-free round: set its default and `TYPE_DEFAULT`.
- Select a new type while origin is `TYPE_DEFAULT`: replace with the new default and retain `TYPE_DEFAULT`.
- Select a new type while origin is `USER_MEASURED`: require the user's `KEEP_MEASUREMENT` or `APPLY_TYPE_DEFAULT` choice.
- Clear temperature: clear origin.
- Save a round with no type or no temperature: permitted.
- Snapshot the selected type label and stored temperature when the round completes so later catalogue changes cannot rewrite history.

## TemperaturePreference

| Field | Description | Validation |
|---|---|---|
| `displayUnit` | `CELSIUS` or `FAHRENHEIT` | Required; default `CELSIUS` |

## HeartRateSourceSelection

This shared entity supports the parent sauna-session feature; it does not alter sauna-type defaults.

| Field | Description | Validation |
|---|---|---|
| `transport` | `BLUETOOTH_LE` or `ANT_PLUS` | Required when tracking is enabled |
| `deviceIdentifier` | Transport-scoped selected device reference | Required when connected |
| `deviceLabel` | User-visible monitor label | Optional |
| `connectionState` | `UNAVAILABLE`, `READY`, `SCANNING`, `CONNECTING`, `CONNECTED`, or `DISCONNECTED` | Required |

Exactly one source may be connected for an active visit. Both transports normalize incoming readings to timestamped beats-per-minute values associated with the active heat round or recovery interval.
