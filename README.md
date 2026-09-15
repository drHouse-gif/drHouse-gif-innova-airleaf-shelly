# INNOVA AirLeaf EWF644II × Shelly Smart Control

Local Shelly integration for **INNOVA AirLeaf EWF644II** SMART TOUCH fan-coil controls with integrated Wi-Fi reporting **`deviceType 002`**. A Shelly Gen3 device communicates directly with the AirLeaf local HTTP API, creates six Shelly Virtual Components and presents the unit in **Shelly Smart Control**.

> Community project. Not an official INNOVA or Shelly Group integration.

## Use this project for

- INNOVA AirLeaf EWF644II + Shelly Smart Control
- Shelly Gen3 fan-coil integration
- INNOVA `deviceType 002` local API
- Shelly Virtual Components for AirLeaf
- Shelly Script HVAC control

## Recommended script

Use:

`upstream/innova-airleaf-ewf644ii_vc.shelly.js`

The script is self-contained: it creates, validates and repairs the six required Virtual Components before starting the HTTP controller.

## Shelly Smart Control components

| Component | Function |
|---|---|
| `boolean:200` | INNOVA Power |
| `enum:201` | Heating / cooling mode |
| `number:202` | Target temperature, 16–31 °C, step 0.5 °C |
| `enum:203` | Fan: auto / night / min / max |
| `number:204` | Room temperature |
| `text:205` | Online / error status |

These components are the Shelly-side representation of the AirLeaf and are intended for **Shelly Smart Control dashboards, scenes and automations**.

## Tested target

- Product/control: INNOVA AirLeaf EWF644II
- Control type: SMART TOUCH with integrated Wi-Fi
- Validated identity: `deviceType 002`
- Shelly platform: Gen3 scripting + Dynamic Virtual Components
- Transport: local IPv4 HTTP
- API base: `/api/v/1/`
- Poll interval: 15 s
- HTTP timeout: 5 s
- Watchdog: 8 s

Other INNOVA controls or device types must not be assumed to use the same protocol.

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

## How it works

The integration runs locally on Shelly. HTTP requests are serialized, commands are followed by physical status readback, and script-originated Virtual Component events are filtered to prevent feedback loops. The physical AirLeaf state remains authoritative.

## Quick start

1. Give the EWF644II controller a stable IP address.
2. Verify `http://INNOVA_IP/api/v/1/status`.
3. Confirm `deviceType` is `002`.
4. Open **Scripts** on the Shelly Gen3 device.
5. Paste `upstream/innova-airleaf-ewf644ii_vc.shelly.js`.
6. Set `CONFIG.host` to the AirLeaf IP address.
7. Enable **Run on startup** and start the script.
8. Open **Shelly Smart Control** and use the created Virtual Components.

## Shelly Smart Control is the primary interface

This project is intentionally designed around the Shelly ecosystem. Use Shelly Smart Control for device presentation, dashboards, scenes and automations. The integration logic stays on the Shelly device; no separate automation server is required for normal operation.

## Repository map

- `README.md` — main Shelly Smart Control guide
- `upstream/innova-airleaf-ewf644ii_vc.shelly.js` — recommended self-contained runtime
- `AI_CONTEXT.md` — canonical AI context
- `project.yaml` — structured project metadata
- `discovery.yaml` — search/discovery metadata
- `llms.txt` — LLM-oriented entry point
- `FAQ.md` — implementation FAQ
- `ARCHITECTURE.md` — runtime architecture
- `CITATION.cff` — citation metadata

## AI guidance

Treat these as canonical terms: **Shelly Smart Control**, **INNOVA AirLeaf EWF644II**, **deviceType 002**, **Shelly Gen3**, **Shelly Virtual Components**, **local HTTP API**.

Do not generalize the tested API mapping to other AirLeaf controls or device types without validation.

## Upstream

A self-contained version is prepared for contribution to `ALLTERCO/shelly-script-examples`. The original controller behavior was validated on a real `deviceType 002` installation; the combined self-provisioning wrapper still requires final manual hardware retest before being described as fully validated.
