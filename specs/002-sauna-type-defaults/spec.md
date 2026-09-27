# Feature Specification: Sauna Type Defaults

**Feature Branch**: `mikulash-sauna-session-tracking`

**Created**: 2026-09-28

**Status**: Draft

**Input**: User description: "allow to track type of sauna like infra, finish, steam; track temperature inside; for each type save default one."

## User Scenarios & Testing *(mandatory)*

### User Story 1 - Choose a Sauna Type (Priority: P1)

As a sauna-goer, I can select the kind of sauna used for a heat round, so that each round clearly records its conditions.

**Why this priority**: Accurate sauna-type identification gives recorded temperature its practical context and is required before a relevant default can be applied.

**Independent Test**: A user can begin a heat round, select Finnish sauna, infrared sauna, or steam room, and see the selected type in the saved round.

**Acceptance Scenarios**:

1. **Given** a new or active heat round, **When** the user opens sauna-type selection, **Then** the app offers Finnish sauna, infrared sauna, and steam room.
2. **Given** the user selects a sauna type, **When** the heat round is saved, **Then** the saved record shows the selected type.
3. **Given** a user does not select a sauna type, **When** the heat round is saved, **Then** the round remains valid and is labelled as an unspecified sauna type.

---

### User Story 2 - Apply a Default Temperature (Priority: P1)

As a sauna-goer, I can have the expected temperature for a selected sauna type filled in automatically, so that I can record a heat round quickly while still correcting the reading when needed.

**Why this priority**: Default temperatures remove repeated data entry while preserving accurate, per-visit records.

**Independent Test**: A user selects each available sauna type for separate heat rounds and verifies that the appropriate default temperature is filled in, then changes one value and saves it.

**Acceptance Scenarios**:

1. **Given** a new heat round with no recorded temperature, **When** the user selects a sauna type, **Then** the type's default temperature is filled in.
2. **Given** a heat round with an automatically filled temperature, **When** the user changes the temperature, **Then** the entered temperature is saved for that round without changing the sauna type's default.
3. **Given** the user changes from one sauna type to another before manually editing the temperature, **When** the new type is selected, **Then** its default temperature replaces the prior automatic value.
4. **Given** the user has manually entered a temperature, **When** the user changes sauna type, **Then** the app asks whether to keep the entered temperature or apply the new type's default.

---

### User Story 3 - Review Accurate Sauna Conditions (Priority: P2)

As a sauna-goer, I can review the selected sauna type and the actual saved temperature for every heat round, so that my history distinguishes standard settings from real conditions.

**Why this priority**: Reviewable historical conditions make the saved sauna records useful for comparing visits and later analytics.

**Independent Test**: A user saves heat rounds using different types and an overridden temperature, then confirms that visit history preserves each type and saved temperature.

**Acceptance Scenarios**:

1. **Given** a completed visit with heat rounds from multiple sauna types, **When** the user opens its details, **Then** each heat round shows its own type and saved temperature.
2. **Given** the user changes the global temperature display unit, **When** the user reviews historical heat rounds, **Then** their temperatures display in the selected unit while preserving their measured values.

### Edge Cases

- A user changes the sauna type after manually entering a temperature; the app requires a deliberate choice between retaining the measurement and applying the new type's default.
- A temperature is unavailable or was not measured; the user can save the selected sauna type without inventing a reading.
- A user changes the display unit after a default was applied or a temperature was manually entered; the displayed default and saved measurement convert consistently.
- A historical sauna type is later removed from the available selection list; existing heat rounds retain their original type label and temperature.
- A sauna's actual temperature is outside the usual range for its selected type; the app permits the value with a clear confirmation rather than silently replacing it.

## Requirements *(mandatory)*

### Functional Requirements

- **FR-001**: The system MUST offer Finnish sauna, infrared sauna, and steam room as selectable sauna types for every heat round.
- **FR-002**: The system MUST associate one stored default temperature with each selectable sauna type.
- **FR-003**: The system MUST automatically fill a heat round's temperature with the selected sauna type's default when the round has no manually entered temperature.
- **FR-004**: The system MUST allow the user to replace an automatically filled temperature with the actual measured temperature for that heat round.
- **FR-005**: The system MUST preserve a user-entered heat-round temperature independently of its sauna type's stored default.
- **FR-006**: When the user changes sauna type after entering a temperature, the system MUST require the user to choose whether to retain that temperature or apply the newly selected type's default.
- **FR-007**: When the user changes sauna type before manually entering a temperature, the system MUST replace the prior automatic value with the newly selected type's default.
- **FR-008**: The system MUST allow a heat round to be saved with a selected sauna type and no recorded temperature.
- **FR-009**: The system MUST retain the selected sauna type and the actual saved temperature for each completed heat round.
- **FR-010**: The system MUST display type defaults and saved heat-round temperatures in the user's selected Celsius or Fahrenheit preference.
- **FR-011**: The system MUST keep historical heat-round type labels and saved temperatures unchanged if the available sauna-type catalogue changes.
- **FR-012**: The system MUST identify a temperature as a default or as a user-entered measurement while the heat round is being recorded.

### Key Entities *(include if feature involves data)*

- **Sauna Type**: A selectable sauna category with a stable name, a default temperature, availability status, and normal operating range used to warn about unusual entries.
- **Heat Round**: A time-bounded session inside one sauna, storing the selected sauna type, actual temperature if known, and whether the current displayed value originated from a type default or user measurement.
- **Temperature Preference**: The user's chosen Celsius or Fahrenheit display unit, applied consistently to both defaults and stored heat-round temperatures.

## Success Criteria *(mandatory)*

### Measurable Outcomes

- **SC-001**: At least 90% of users can select a sauna type and start a heat round with its default temperature in under 15 seconds.
- **SC-002**: In usability testing, at least 90% of users can replace a default with an actual measurement and later identify that saved measurement in under 30 seconds.
- **SC-003**: In 100% of tested records, changing the display unit changes only the displayed temperature and does not alter the saved temperature or sauna type.
- **SC-004**: In 100% of tested sauna-type changes after a manual temperature entry, the user is asked to retain the measurement or apply the new type's default.
- **SC-005**: At least 95% of users can distinguish a default temperature from a recorded measurement while adding a heat round.

## Assumptions

- “Infra” means infrared sauna and “finish” means Finnish sauna.
- The initial type catalogue contains Finnish sauna, infrared sauna, and steam room; the catalogue can be expanded later without changing historical records.
- Default temperatures are sensible starting values associated with each type, not safety limits or individualized health guidance.
- Users may save a heat round without a temperature when a reading is unavailable.
- Default temperatures are maintained by the app for the initial release; user-defined sauna types and editable global defaults are outside this feature's scope.
