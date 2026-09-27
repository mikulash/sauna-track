# UI Contract: Sauna Session Tracking

This contract defines user-visible behavior and state, not implementation classes or screen layout details.

## Active Visit

| User action | Preconditions | Result |
|---|---|---|
| Start visit | No active visit | Creates an active visit and starts its first heat round. |
| End heat round | Active heat round | Closes the round and offers to start a recovery interval. |
| Start next heat round | Active recovery interval | Closes the recovery interval and starts the next heat round. |
| Record cold exposure | Active or completed recovery interval | Creates a cold plunge or cold shower associated with that interval. |
| End visit | Active visit | Presents finalization; on confirmation, closes active items and saves the visit. |
| Discard visit | Active visit | Requires confirmation and removes it from progress and history counts. |

The active view always shows the active phase name, elapsed time, current totals, the next valid phase action, and the last-update time. A returned active visit prominently offers **Resume**, **End visit**, and **Discard**.

## Monitor Connection

| State | Required presentation | Available action |
|---|---|---|
| Disabled | HR tracking is off; timer remains available | Enable tracking |
| Permission required | Explain that Bluetooth access is needed to find/connect a monitor | Grant or continue without monitor |
| Scanning | Searching for compatible monitors | Cancel scan |
| Device selected | Display selected device and connect progress | Cancel |
| Connected | Display latest BPM, reading time, and current phase | Disconnect |
| Disconnected / unavailable | Explain that future readings are unavailable; retain earlier data | Reconnect or continue without monitor |

The app must never imply that a reading is current after the monitor disconnects. Monitor setup and reconnection must be initiated by the user.

## Visit Details and Analytics

A completed visit displays:

- Heat rounds in chronological order with sauna type, temperature, and duration.
- Recovery intervals with duration and their cold exposures.
- Total heat and recovery time.
- Selected protocol and ritual designation.
- HR trends by phase when readings exist; otherwise an explicit no-data state.

Analytics displays weekly/monthly counts, trend comparisons by heat-round duration, recovery after heat, and visits with/without cold exposure. Comparisons with insufficient readings or visits must state that there is not enough data; they must not present health conclusions.

## Settings and Units

Changing the temperature unit updates displayed values throughout active and historical records. The underlying measurement and saved visit totals remain unchanged.
