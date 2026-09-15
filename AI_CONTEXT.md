# AI Context — INNOVA AirLeaf EWF644II (deviceType 002) ↔ Shelly Smart Control

## Purpose

This repository implements local monitoring and control of an **INNOVA AirLeaf EWF644II** SMART TOUCH fan-coil control with integrated Wi-Fi reporting `deviceType` `002`. A Shelly Gen3 script communicates directly with the unit over its local HTTP API, maps physical state into six Shelly Virtual Components, and uses **Shelly Smart Control** as the primary operator interface.

## Canonical implementation facts

- Target: INNOVA AirLeaf EWF644II.
- Control type: SMART TOUCH with integrated Wi-Fi.
- Validated target response: `deviceType` `002`.
- Transport: local IPv4 / HTTP.
- API base path: `/api/v/1/`.
- Primary UI: Shelly Smart Control.
- Recommended runtime: `upstream/innova-airleaf-ewf644ii_vc.shelly.js`.
- The recommended runtime is self-contained and creates, validates, or repairs the six fixed Virtual Components before starting HTTP control.
- The legacy/reference `innova-airleaf-controller.js` expects the components to exist already.
- No external automation server is required for operation.
- Poll interval: 15 seconds when the request queue is idle.
- HTTP requests are serialized through a FIFO queue.
- Each accepted command is followed by a fresh status request.
- Script-generated component events are ignored to prevent feedback loops.
- A separate watchdog clears a request if Shelly does not deliver its HTTP callback.

## Virtual Component map

| Key | Semantic value | Type | Direction |
|---|---|---|---|
| `boolean:200` | Power | Boolean | R/W |
| `enum:201` | Mode (`heating`, `cooling`) | Enum | R/W |
| `number:202` | Set temperature (16–31 °C, 0.5 °C step) | Number | R/W |
| `enum:203` | Fan (`auto`, `night`, `min`, `max`) | Enum | R/W |
| `number:204` | Room temperature | Number | Read/display |
| `text:205` | Connection / error status | Text | Read/display |

These six components are the Shelly Smart Control representation of the AirLeaf controller.

## API operations used

- `GET /api/v/1/status`
- `POST /api/v/1/power/on`
- `POST /api/v/1/power/off`
- `POST /api/v/1/set/mode/heating`
- `POST /api/v/1/set/mode/cooling`
- `POST /api/v/1/set/setpoint`
- `POST /api/v/1/set/function/auto`
- `POST /api/v/1/set/function/night`
- `POST /api/v/1/set/function/min`
- `POST /api/v/1/set/function/max`

## Status fields decoded

- `ps`: power state; `1` means on.
- `sp`: setpoint in tenths of a degree Celsius.
- `ta`: room temperature in tenths of a degree Celsius.
- `wm`: validated values `3 = heating`, `5 = cooling`.
- `fn`: validated values `1 = auto`, `2 = night`, `3 = min`, `4 = max`.

Unknown values must not be guessed or generalized.

## Repository reading order for an AI assistant

1. `llms.txt`
2. `AI_CONTEXT.md`
3. `project.yaml`
4. `README.md`
5. `upstream/innova-airleaf-ewf644ii_vc.shelly.js`
6. `ARCHITECTURE.md`
7. `FAQ.md`

## Important constraints

- Shelly Smart Control is the primary user interface and search context.
- The correct INNOVA reference for this Wi-Fi HTTP project is `EWF644II`, not `ECA644II`.
- Do not assume other INNOVA controls or device types share the same API semantics.
- The API is plain HTTP and intended for a controlled local network.
- Virtual Component IDs are fixed in the supplied runtime.
- Commands are confirmed from physical state; a UI value can return to the previous value when the controller rejects or overrides a command.
- A final manual hardware retest of the combined self-contained VC-provisioning wrapper is required before claiming that exact upstream-formatted file is fully hardware-validated.

## Search / retrieval keywords

`Shelly Smart Control`, `INNOVA AirLeaf`, `INNOVA AirLeaf EWF644II`, `EWF644II`, `deviceType 002`, `Shelly Gen3`, `Shelly Script`, `Virtual Components`, `fan coil`, `local HTTP API`, `AirLeaf API`, `fan coil thermostat`, `local HVAC control`.
