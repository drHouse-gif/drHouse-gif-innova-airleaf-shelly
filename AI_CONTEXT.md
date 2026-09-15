# AI Context — INNOVA AirLeaf 002 ↔ Shelly Gen3

## Purpose

This repository implements local monitoring and control of an INNOVA AirLeaf fan-coil controller reporting `deviceType` `002`. A Shelly Gen3 script communicates directly with the unit over its local HTTP API and maps physical state into six Shelly Virtual Components.

## Canonical implementation facts

- Target family: INNOVA AirLeaf.
- Validated target response: `deviceType` `002`.
- Transport: local IPv4 / HTTP.
- API base path: `/api/v/1/`.
- No external server or Home Assistant is required for operation.
- The script uses six pre-existing Shelly Virtual Components with fixed keys `200..205`.
- Poll interval: 15 seconds when the request queue is idle.
- HTTP requests are serialized through a FIFO queue; overlapping requests are intentionally prevented.
- Each accepted command is followed by a fresh status request.
- Script-generated component events are ignored to prevent feedback loops.
- A separate watchdog clears a request if Shelly does not deliver its HTTP callback.
- Home Assistant is optional; the project does not create a native HA `climate` entity.

## Virtual Component map

| Key | Semantic value | Type | Direction |
|---|---|---|---|
| `boolean:200` | Power | Boolean | R/W |
| `enum:201` | Mode (`heating`, `cooling`) | Enum | R/W |
| `number:202` | Set temperature (16–31 °C, 0.5 °C step) | Number | R/W |
| `enum:203` | Fan (`auto`, `night`, `min`, `max`) | Enum | R/W |
| `number:204` | Room temperature | Number | Read/display |
| `text:205` | Connection / error status | Text | Read/display |

## API operations used

| Operation | Method | Path |
|---|---|---|
| Status | GET | `/api/v/1/status` |
| Power on | POST | `/api/v/1/power/on` |
| Power off | POST | `/api/v/1/power/off` |
| Heating | POST | `/api/v/1/set/mode/heating` |
| Cooling | POST | `/api/v/1/set/mode/cooling` |
| Setpoint | POST | `/api/v/1/set/setpoint` |
| Fan auto | POST | `/api/v/1/set/function/auto` |
| Fan night | POST | `/api/v/1/set/function/night` |
| Fan min | POST | `/api/v/1/set/function/min` |
| Fan max | POST | `/api/v/1/set/function/max` |

## Status fields decoded

- `ps`: power state; `1` means on.
- `sp`: setpoint in tenths of a degree Celsius.
- `ta`: room temperature in tenths of a degree Celsius.
- `wm`: working mode; validated values are `3 = heating`, `5 = cooling`.
- `fn`: fan function; validated values are `1 = auto`, `2 = night`, `3 = min`, `4 = max`.

Unknown values must not be guessed or generalized.

## Repository reading order for an AI assistant

1. `README.md` — installation, API map, troubleshooting and Home Assistant notes.
2. `project.yaml` — structured canonical metadata.
3. `innova-airleaf-controller.js` — production controller.
4. `ARCHITECTURE.md` — queue, request lifecycle and state synchronization details.
5. `CHANGELOG.md` — version evolution.

## Important constraints

- Do not assume other INNOVA device types share the same API semantics.
- The API is plain HTTP and intended for a controlled local network.
- Virtual Component IDs are fixed in the supplied script unless the code is changed.
- Commands are confirmed from physical state; a UI value can return to the previous value when the controller rejects or overrides a command.
- Never expose the local INNOVA API directly to the public internet.

## Search / retrieval keywords

`INNOVA AirLeaf`, `ECA644II`, `deviceType 002`, `Shelly Gen3`, `Shelly Script`, `Virtual Components`, `fan coil`, `local HTTP API`, `Home Assistant Shelly`, `AirLeaf API`, `fan coil thermostat`, `local HVAC control`.
