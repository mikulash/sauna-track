# UI Contract: Sauna Type Defaults

## Sauna Type Selection

The active heat-round editor presents Finnish sauna, infrared sauna, and steam room. Each option shows its type name and default temperature in the user's selected unit.

| User action | Current value | Required result |
|---|---|---|
| Select sauna type | No temperature | Fill that type's default; label it “Default”. |
| Select another type | Default temperature | Replace with the new type's default; keep the “Default” label. |
| Edit temperature | Default temperature | Save entered value; label it “Measured”. |
| Select another type | Measured temperature | Ask the user to **Keep measurement** or **Use [type] default**. |
| Clear temperature | Any | Remove the value and its origin label. |

An unusual temperature prompts for confirmation and does not block saving. Saving without a type or temperature remains valid.

## Visit Details

Each heat round displays its saved sauna-type label, its temperature if recorded, and a default/measured indicator only while editing. Completed-record details identify the stored type and temperature without allowing a later type-catalogue change to alter history.

## Heart-Rate Source Selection

The shared monitor setup presents only available sources:

| Source state | Required presentation | User action |
|---|---|---|
| Bluetooth LE available | Scan compatible BLE heart-rate monitors | Select a device |
| ANT+ services/hardware available | Search ANT+ heart-rate monitors | Select a device |
| ANT+ unavailable | Explain that ANT+ support is unavailable on this device | Use Bluetooth LE or continue without a monitor |
| Both unavailable or denied | Explain that heart-rate tracking cannot start | Continue timer-only |

After a device is selected, the active visit displays the transport label, device label, connection state, latest reading, and reading time. The app must preserve already-recorded readings after a disconnect and never invent later readings.
