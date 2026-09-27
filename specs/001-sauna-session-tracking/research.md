# Research: Sauna Session Tracking

## BLE heart-rate acquisition

**Decision**: Support Bluetooth Low Energy heart-rate monitors implementing the standard GATT Heart Rate Profile. Scan only after the user starts monitor setup, connect to the user-selected device, discover services, and subscribe to Heart Rate Measurement notifications from service `0x180D` and characteristic `0x2A37`.

**Rationale**: The standard profile supports common chest straps and other compatible monitors without a vendor SDK. Notification-based delivery supplies readings in real time and avoids polling. The BLE layer will parse the profile's flags so it correctly reads 8-bit or 16-bit beats-per-minute values.

**Alternatives considered**:

- Vendor-specific SDKs: rejected because they narrow device compatibility and add per-vendor dependencies.
- Manual heart-rate entry only: rejected because it does not satisfy real-time tracking.
- Health Connect as the live source: rejected because it is a health-data exchange mechanism, not a dependable real-time BLE stream.

## Background tracking and lifecycle

**Decision**: Run a foreground `connectedDevice` tracking service only while the user explicitly enables heart-rate tracking for an active visit. The service owns the BLE connection, writes timestamped readings through the repository, publishes connection and latest-reading state, and stops when tracking ends or the visit is ended/discarded.

**Rationale**: A foreground service with a persistent notification gives the user clear visibility and allows a connected monitor to keep delivering readings when the activity is no longer foregrounded. Persisting readings as they arrive prevents data loss if the UI is recreated.

**Alternatives considered**:

- Activity-bound connection: rejected because readings stop when the activity is backgrounded or recreated.
- Continuous background scanning: rejected for privacy, battery, and modern Android restrictions.
- Long-running background worker: rejected because it is not intended for an interactive, persistent BLE connection.

## Permissions and privacy

**Decision**: Request Bluetooth scan and connect permissions at the moment the user chooses to pair or reconnect a monitor. Declare version-appropriate legacy Bluetooth and location requirements only where required by the Android version. Clearly explain why access is requested and let the user continue with timer-only tracking after denial. Store health-related visit data locally; do not transmit it.

**Rationale**: Just-in-time permissions reduce surprise and honor the optional nature of monitor tracking. A timer-only path preserves core value when Bluetooth is unavailable or denied.

**Alternatives considered**:

- Asking for all permissions on launch: rejected because it is unnecessary before monitor use and reduces consent quality.
- Requiring a monitor to start a visit: rejected because it contradicts the optional heart-rate requirement.
- Cloud-only storage: rejected because the planned feature is offline-first and has no account scope.

## Timer and stale-session correctness

**Decision**: Derive displayed elapsed durations from persisted wall-clock phase start/end timestamps, not from an in-memory counter. Persist each start, transition, reading, and explicit user action as the visit's last-update time. On resume, show the last-update time and require an explicit user choice to resume, finish, or discard an unfinished visit.

**Rationale**: Timestamp-based duration calculation survives process death, device sleep, activity recreation, and foreground-service restarts. Explicit stale-session resolution prevents silently producing misleading records.

**Alternatives considered**:

- In-memory ticking counter: rejected because it loses accuracy and state across lifecycle events.
- Automatically ending old visits: rejected because the intended end time cannot be inferred safely.

## Architecture and persistence

**Decision**: Use a layered, single-module Android architecture: Room entities and DAO-backed repositories for persistence, pure Java domain services for phase state, duration, temperature conversion, totals, and analytics, and lifecycle-aware UI controllers/view models for screen state.

**Rationale**: The design makes the highest-risk logic independently testable, provides transactional phase changes, and isolates Android and BLE APIs from calculations.

**Alternatives considered**:

- Direct database access from screens: rejected because it makes state transitions and testing unreliable.
- A remote backend: rejected because it adds account, synchronization, privacy, and operational scope not required by the feature.
