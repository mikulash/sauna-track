# Feature Specification: Sauna Session Tracking

**Feature Branch**: `mikulash-sauna-session-tracking`

**Created**: 2026-09-28

**Status**: Draft

**Input**: User description: "show rounds of sauna, time in, temperature, type of sauna; measure heart rate inside and outside; track sauna sessions, recovery time, cold plunges or showers; count sessions per week and month; select protocols such as Brian Johnson's; show heart-rate analytics; mark a session as a sauna ritual; show the last update when a timer is left running; support Celsius and Fahrenheit; support multiple heat rounds and recovery intervals per visit; use a workout-tracking style interface."

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Record a Sauna Visit (Priority: P1)

As a sauna-goer, I can start a visit, record one or more heat rounds and recovery intervals, and end the visit so that I have an accurate record of the time spent in and out of the sauna.

**Why this priority**: Capturing the core visit is the basis for all history, totals, and later analysis.

**Independent Test**: A user can create a visit with two heat rounds and their intervening recovery interval, end the visit, and review the saved phase durations and total heat and recovery time.

**Acceptance Scenarios**:

1. **Given** no active visit, **When** the user starts a visit and begins a heat round, **Then** the system displays its elapsed time and records its start time.
2. **Given** an active heat round, **When** the user ends it and starts a recovery interval, **Then** the completed heat round is retained and the recovery interval timer begins.
3. **Given** a visit with completed heat rounds and recovery intervals, **When** the user ends the visit, **Then** the system saves the visit and separately totals heat-round and recovery-interval time.
4. **Given** a completed heat round, **When** the user records sauna type and temperature, **Then** those details are shown with that round in the visit record.

---

### User Story 2 - Track Recovery and Cold Exposure (Priority: P2)

As a sauna-goer, I can record whether a recovery interval included a cold plunge or shower and capture its duration, so that my visit history reflects my complete contrast routine.

**Why this priority**: Cold exposure is a meaningful part of many sauna routines, but the visit remains useful without it.

**Independent Test**: A user can add a cold plunge or cold shower to a recovery interval and subsequently see its type and duration in the saved visit.

**Acceptance Scenarios**:

1. **Given** an active or completed recovery interval, **When** the user records a cold plunge, **Then** the system saves the cold-exposure type and duration with that interval.
2. **Given** a recovery interval with a recorded cold shower, **When** the user views the visit, **Then** the cold shower is visibly distinguished from the overall recovery interval.

---

### User Story 3 - Monitor Heart Rate Across a Visit (Priority: P2)

As a sauna-goer using a heart-rate monitor, I can enable heart-rate tracking for a visit and view readings during heat rounds and recovery intervals, so that I can understand how my body responds to each phase.

**Why this priority**: Heart-rate tracking adds personal insight while remaining optional for users who only want time and temperature tracking.

**Independent Test**: With heart-rate tracking enabled, a user can complete a visit with a heat round and a recovery interval and view readings associated with each phase.

**Acceptance Scenarios**:

1. **Given** heart-rate tracking is enabled for a visit, **When** readings are received during a heat round and recovery interval, **Then** the system associates each reading with the phase active at the time.
2. **Given** a completed visit containing heart-rate readings, **When** the user views its analytics, **Then** the system shows heart-rate change over the course of each heat round and recovery interval.
3. **Given** heart-rate tracking is unavailable or disabled, **When** the user records a visit, **Then** the visit can be completed without heart-rate data and analytics clearly state that no readings are available.

---

### User Story 4 - Review Progress and Follow a Protocol (Priority: P3)

As a sauna-goer, I can choose a sauna protocol, mark a visit as a ritual, and review weekly and monthly counts and heart-rate trends so that I can follow a repeatable routine and observe progress.

**Why this priority**: Protocols and summaries help users build consistency after reliable individual visit tracking is available.

**Independent Test**: A user can select a protocol, complete and mark a visit as a ritual, then view weekly and monthly visit counts and the visit's heart-rate comparison.

**Acceptance Scenarios**:

1. **Given** available protocols, **When** the user selects one before a visit, **Then** the visit records the selected protocol and presents its stated guidance.
2. **Given** a completed visit, **When** the user marks it as a sauna ritual, **Then** the ritual designation is retained in visit history and summaries.
3. **Given** completed visits in the current week and month, **When** the user opens progress summaries, **Then** the system shows the number of completed visits in each period.
4. **Given** visits with heart-rate data and heat-round durations, **When** the user opens heart-rate analytics, **Then** the system presents comparisons of heart-rate response by minutes in heat, recovery after the visit, and whether cold exposure was recorded.

---

### User Story 5 - Recover an Unfinished Visit (Priority: P3)

As a sauna-goer who leaves a timer running, I can see when the active visit was last updated and either resume or end it, so that I can correct an unfinished record without guessing.

**Why this priority**: Clear stale-timer handling protects record accuracy and avoids misleading totals.

**Independent Test**: A user starts a phase, leaves it inactive, returns later, sees the last-update time, and can end the phase or visit.

**Acceptance Scenarios**:

1. **Given** an active visit with no recent interaction or reading, **When** the user returns to the tracker, **Then** the system displays the time of the most recent visit update.
2. **Given** an active visit, **When** the user ends the active phase or the visit, **Then** the system records the selected end time and recalculates totals.

### Edge Cases

- A user ends a visit while a heat round, recovery interval, or cold exposure is still active; the active item is ended with the visit.
- A user changes the temperature-unit preference after recording visits; previously stored temperature values display correctly in the newly selected unit without changing their underlying measurements.
- A visit has no temperature, sauna type, cold exposure, or heart-rate data; time tracking and completion remain available.
- A heart-rate monitor disconnects during a visit; retained readings remain available and the user is informed that later readings are missing.
- A heat round crosses a week or month boundary; visit counts are based on the visit's completion date.
- A user records multiple cold-exposure activities in one recovery interval; each activity remains separately represented.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: The system MUST allow users to start, pause between, resume, and end a sauna visit containing one or more heat rounds and recovery intervals.
- **FR-002**: The system MUST record the start time, end time, and elapsed duration of every heat round and recovery interval.
- **FR-003**: The system MUST separately calculate and display total time spent in heat rounds and total time spent in recovery intervals for each completed visit.
- **FR-004**: The system MUST allow users to record a sauna type and a temperature for each heat round.
- **FR-005**: The system MUST let users select Celsius or Fahrenheit as their display preference and display all temperatures consistently in that preference.
- **FR-006**: The system MUST allow users to record a cold plunge or cold shower within a recovery interval, including its duration and type.
- **FR-007**: The system MUST allow users to enable or disable heart-rate tracking for each visit without preventing the visit from being recorded.
- **FR-008**: When heart-rate tracking is enabled, the system MUST associate available readings with the active heat round or recovery interval and preserve their recorded times.
- **FR-009**: The system MUST show a completed visit's heart-rate response during heat rounds and recovery intervals when heart-rate readings are available.
- **FR-010**: The system MUST provide heart-rate analytics that compare heart-rate change against minutes in heat, post-visit recovery time, and recorded cold exposure.
- **FR-011**: The system MUST offer selectable sauna protocols, including a Brian Johnson-inspired option, and retain the selected protocol with the visit.
- **FR-012**: The system MUST clearly present protocol guidance as user-selected routine information rather than individualized medical advice.
- **FR-013**: The system MUST allow users to mark a completed visit as a sauna ritual and show that designation in its history and summaries.
- **FR-014**: The system MUST show the count of completed sauna visits for the current week and current month.
- **FR-015**: The system MUST persist a last-update time for every active visit and display it when the user returns to an unfinished visit.
- **FR-016**: The system MUST let users end or discard an unfinished visit after reviewing its last-update time.
- **FR-017**: The system MUST retain completed visit history, its component phases, selected protocol, ritual designation, and recorded metrics for later review.
- **FR-018**: The system MUST provide a workout-tracker-style visit flow that keeps the active phase, elapsed time, and next relevant action prominent.

### Key Entities *(include if feature involves data)*

- **Sauna Visit**: A complete attendance record containing its timing, selected protocol, ritual designation, last-update time, totals, and ordered phases.
- **Heat Round**: A period spent in a sauna, with its start and end times, duration, sauna type, and temperature.
- **Recovery Interval**: A period between or after heat rounds, with its timing, duration, heart-rate readings, and optional cold-exposure activities.
- **Cold Exposure**: A cold plunge or cold shower recorded within a recovery interval, with type, timing, and duration.
- **Heart-Rate Reading**: A timestamped measurement captured while heart-rate tracking is enabled and associated with the contemporaneous visit phase.
- **Sauna Protocol**: A named, user-selectable routine containing its stated guidance and intended sequence or targets.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: At least 90% of users can record and complete a two-round sauna visit with a recovery interval in under 2 minutes, excluding the elapsed activity time.
- **SC-002**: Users can identify total heat time, total recovery time, and each heat round's sauna type and temperature from a completed visit in under 30 seconds.
- **SC-003**: For a visit with available heart-rate readings, users can view phase-level heart-rate changes and the visit's heat, recovery, and cold-exposure comparisons in under 30 seconds.
- **SC-004**: Weekly and monthly completed-visit counts match a test dataset's expected totals in 100% of tested date-boundary cases.
- **SC-005**: In usability testing, at least 90% of users returning to an unfinished visit can identify its last-update time and correctly resume, end, or discard it without assistance.
- **SC-006**: At least 85% of users can select a protocol and mark a completed visit as a ritual on their first attempt.

## Assumptions

- The feature is intended for individual users tracking their own sauna activity; multi-user coaching, social sharing, and clinical monitoring are outside the initial scope.
- Heart-rate tracking is optional and uses readings supplied by a compatible heart-rate monitor when available; the user can complete every visit without a monitor.
- The initial protocol library contains general routine guidance, including a Brian Johnson-inspired option, and does not provide individualized medical guidance.
- A heat round is the user-facing name for time inside a sauna, and a recovery interval is the user-facing name for time outside it.
- A completed visit is counted in weekly and monthly summaries by its completion date using the user's local calendar.
- The interface prioritizes the active routine in a concise, workout-tracker-inspired layout; visual imitation of a specific product is out of scope.
