# AI Context — INNOVA AirLeaf EWF644II (deviceType 002) ↔ Shelly Smart Control

## Purpose

This repository implements local monitoring and control of an **INNOVA AirLeaf EWF644II** SMART TOUCH fan-coil control with integrated Wi-Fi reporting `deviceType 002`. A Shelly Gen3 script communicates directly with the unit over its local HTTP API and maps physical state into six Shelly Virtual Components.

## Canonical implementation facts

- Target: INNOVA AirLeaf EWF644II.
- Validated device type: `002`.
- Transport: local IPv4 HTTP.
- API base path: `/api/v/1/`.
- Primary UI: Shelly Smart Control.
- Production runtime: `upstream/innova-airleaf-ewf644ii_vc.shelly.js`.
- Hardware-tested on Shelly Plug S Gen3 firmware 2.0.0.
- Runtime is memory-optimized for constrained Gen3 script RAM.
- The script creates or reuses six fixed Virtual Components.
- Existing components receive current UI titles and Cloud metadata.
- HTTP requests are serialized and pending same-control changes are coalesced.
- A command burst is followed by one delayed physical status readback.
- Script-generated component events are ignored to prevent feedback loops.
- Request IDs and a watchdog protect against missing or stale callbacks.
- Shelly mJS queue removal uses indexed access plus `slice(1)`, not `Array.shift()`.

## Virtual Component map

| Key | Semantic value | Direction |
|---|---|---|
| `boolean:200` | Power | R/W |
| `enum:201` | Mode: `heating`, `cooling` | R/W |
| `number:202` | Set temperature, 16–31 °C, 0.5 °C | R/W |
| `enum:203` | Fan: `auto`, `night`, `min`, `max` | R/W |
| `number:204` | Room temperature | read/display |
| `text:205` | Connection / command status | read/display |

## API operations

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

## Validated status mapping

- `ps`: power; 1 = on.
- `sp`: setpoint in tenths of °C.
- `ta`: room temperature in tenths of °C.
- `wm`: 3 = heating, 5 = cooling.
- `fn`: 1 = auto, 2 = night, 3 = min, 4 = max.

Do not infer unknown values.

## Runtime defaults

- poll: 15 s
- HTTP timeout: 5 s
- watchdog: 7.5 s
- command settle/readback delay: 0.5 s
- maximum waiting queue: 5

## Important constraints

- The canonical Wi-Fi controller reference for this project is `EWF644II`, not `ECA644II`.
- Public repository configuration uses TEST-NET addresses; real installation addresses must not be committed.
- Communication is plain HTTP and should remain on a trusted LAN.
- `innova-airleaf-controller.js` is legacy/reference code; use the self-contained upstream runtime for new deployments.
