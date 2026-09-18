# INNOVA AirLeaf EWF644II × Shelly Smart Control

Local Shelly integration for **INNOVA AirLeaf EWF644II** SMART TOUCH fan-coil controls with integrated Wi-Fi reporting **`deviceType 002`**. A Shelly Gen3 device communicates directly with the AirLeaf local HTTP API and exposes the fan coil through Shelly Virtual Components for **Shelly Smart Control**.

> Community project. Not an official INNOVA or Shelly Group integration.

## Current status

The current runtime is hardware-tested on:

- **Shelly Plug S Gen3**
- firmware **2.0.0**
- INNOVA AirLeaf reporting **`deviceType 002`**
- local HTTP API **`/api/v/1/`**

The current implementation was reduced for the constrained Shelly Gen3 script heap after the earlier generic Virtual Component helper version caused `out_of_memory` on Plug S Gen3. The production runtime now uses a compact self-contained VC bootstrap and Shelly mJS-compatible queue handling.

## Recommended script

Use:

`upstream/innova-airleaf-ewf644ii_vc.shelly.js`

Change only:

```javascript
var CONFIG = {
  host: '192.0.2.10',
  ...
};
```

Replace the TEST-NET address with the local IPv4 address of the AirLeaf controller.

## Shelly Smart Control components

| Component | Function |
|---|---|
| `boolean:200` | Power |
| `enum:201` | Heating / cooling mode |
| `number:202` | Target temperature, 16–31 °C, step 0.5 °C |
| `enum:203` | Fan: auto / night / min / max |
| `number:204` | Room temperature |
| `text:205` | Online / command / error status |

The enum components include Shelly UI titles and Cloud log metadata. Temperature components use Cloud measurement metadata.

## Local API

```text
GET  /api/v/1/status
POST /api/v/1/power/on
POST /api/v/1/power/off
POST /api/v/1/set/mode/heating
POST /api/v/1/set/mode/cooling
POST /api/v/1/set/setpoint
POST /api/v/1/set/function/auto
POST /api/v/1/set/function/night
POST /api/v/1/set/function/min
POST /api/v/1/set/function/max
```

Temperature values use tenths of a degree. Example: **22.0 °C = `{"temp":220}`**.

Validated status fields:

- `ps` — power, `1 = on`
- `sp` — setpoint ×0.1 °C
- `ta` — room temperature ×0.1 °C
- `wm` — `3 = heating`, `5 = cooling`
- `fn` — `1 = auto`, `2 = night`, `3 = min`, `4 = max`

Unknown values are not guessed.

## Runtime behavior

The controller:

- creates or reuses the six fixed Virtual Components;
- updates existing VC configuration so Shelly Smart Control gets current labels and metadata;
- serializes all HTTP traffic;
- coalesces pending changes for the same control;
- powers the unit before changing mode when needed;
- aborts dependent queued commands when a control request fails;
- waits briefly and performs one physical status readback after a command burst;
- rejects status synchronization unless `deviceType` is `002`;
- ignores script-originated VC events to prevent feedback loops;
- uses a watchdog plus request IDs to ignore stale callbacks;
- avoids `Array.shift()` for compatibility with Shelly mJS builds where it is unavailable.

## Quick start

1. Give the AirLeaf controller a stable LAN address.
2. Verify `http://INNOVA_IP/api/v/1/status`.
3. Confirm the response reports `deviceType 002`.
4. Open **Scripts** on the Shelly Gen3 device.
5. Paste `upstream/innova-airleaf-ewf644ii_vc.shelly.js`.
6. Set `CONFIG.host` to the AirLeaf local IP.
7. Save and start the script.
8. Verify the six Virtual Components in Shelly Smart Control.
9. Enable **Run on startup** after the runtime is confirmed stable.

## Repository map

- `README.md` — deployment and behavior
- `upstream/innova-airleaf-ewf644ii_vc.shelly.js` — current production runtime
- `ARCHITECTURE.md` — request queue and synchronization design
- `AI_CONTEXT.md` — canonical implementation context
- `project.yaml` — structured project metadata
- `FAQ.md` — implementation FAQ
- `docs/UPSTREAM_SUBMISSION.md` — Shelly upstream contribution notes
- `llms.txt` — compact machine-readable project summary
- `innova-airleaf-controller.js` — legacy/reference controller

## Scope

The validated target is **INNOVA AirLeaf EWF644II / deviceType 002**. Do not assume other INNOVA controls or device types use identical endpoints or value mappings without validation.

Communication is unencrypted local HTTP, so the device API should remain on a trusted LAN.
