# INNOVA AirLeaf ECA644II (deviceType 002) × Shelly Gen3

Local, self-contained Shelly integration for **INNOVA AirLeaf ECA644II fan-coil controllers reporting `deviceType` `002`**. The project runs directly on a **Shelly Gen3** device, communicates with the INNOVA unit through its local **HTTP `/api/v/1/` API**, creates Shelly Virtual Components, and optionally exposes them to **Home Assistant** through the standard Shelly integration.

> Community integration. Not an official INNOVA, Shelly Group, or Home Assistant product.

## Find this project when you need

This repository is intended to answer searches such as:

- INNOVA AirLeaf ECA644II Shelly integration
- INNOVA AirLeaf deviceType 002 local API
- Shelly Gen3 fan-coil controller
- Shelly Script INNOVA AirLeaf
- INNOVA AirLeaf Home Assistant via Shelly
- local HTTP HVAC integration with Shelly Virtual Components
- ECA644II API `/api/v/1/status`

## Recommended implementation

Use the self-contained upstream-ready script:

[`upstream/innova-airleaf-eca644ii_vc.shelly.js`](upstream/innova-airleaf-eca644ii_vc.shelly.js)

It automatically creates, validates, and repairs the six fixed Virtual Components before starting the controller logic. The older [`innova-airleaf-controller.js`](innova-airleaf-controller.js) is retained as the original/reference implementation and expects the Virtual Components to exist already.

## What it controls

| Function | Shelly Virtual Component | INNOVA API |
|---|---|---|
| Power | `boolean:200` | `/power/on`, `/power/off` |
| Heating / cooling | `enum:201` | `/set/mode/heating`, `/set/mode/cooling` |
| Set temperature | `number:202` | `/set/setpoint` |
| Fan function | `enum:203` | `/set/function/{auto|night|min|max}` |
| Room temperature | `number:204` | status field `ta` |
| Connection / state | `text:205` | `/status` |

## Tested target

| Property | Value |
|---|---|
| Product family | INNOVA AirLeaf |
| Hardware reference | ECA644II |
| Validated identity | `deviceType` `002` |
| Transport | Local IPv4 HTTP |
| API base | `/api/v/1/` |
| Shelly platform | Gen3 scripting + Dynamic Virtual Components |
| Polling | 15 seconds when queue is idle |
| HTTP timeout | 5 seconds |
| Independent watchdog | 8 seconds |

The project intentionally does **not** assume that other INNOVA device types use the same protocol semantics.

## API endpoints

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

Temperature setpoints are represented in tenths of a degree. Example: **22.0 °C → `{"temp":220}`**.

## Decoded status fields

| Field | Meaning | Validated mapping |
|---|---|---|
| `ps` | Power | `1` = on |
| `sp` | Setpoint | value ÷ 10 °C |
| `ta` | Room temperature | value ÷ 10 °C |
| `wm` | Working mode | `3` = heating, `5` = cooling |
| `fn` | Fan function | `1` auto, `2` night, `3` min, `4` max |

Unknown `wm` and `fn` values are not guessed.

## Runtime design

The controller is local-first and does not require Home Assistant, a cloud API, or an external server. Requests are serialized through a FIFO queue. Every accepted control command is followed by a fresh physical status read. Script-originated Virtual Component events are ignored to avoid feedback loops, and late HTTP callbacks are rejected using request IDs plus an independent watchdog.

The self-contained version starts the HTTP runtime only after Virtual Component provisioning succeeds.

## Quick start

1. Give the INNOVA controller a stable IP address or DHCP reservation.
2. Confirm that this URL responds from the same network:

```text
http://INNOVA_IP/api/v/1/status
```

3. Verify that the response identifies the device as `deviceType` `002`.
4. Open Shelly **Scripts** and create a new script.
5. Copy [`upstream/innova-airleaf-eca644ii_vc.shelly.js`](upstream/innova-airleaf-eca644ii_vc.shelly.js).
6. Set `CONFIG.host` to the INNOVA IPv4 address.
7. Save, enable **Run on startup**, and start the script.
8. Confirm that `INNOVA Status` becomes `online / type 002`.

## Home Assistant

Home Assistant is optional. Add the Shelly device through the standard **Shelly integration**. Supported Virtual Components may appear as Home Assistant entities depending on Shelly firmware and Home Assistant version.

This script does **not** create a native Home Assistant `climate` entity. A template/climate entity can be built in Home Assistant while keeping the Shelly device as the local HVAC control bridge.

## Security

Communication with the tested INNOVA API is plain HTTP on the local network. Do not expose the AirLeaf API directly to the public internet. Use a controlled IoT network, routing/firewall rules, and stable addressing.

## Repository map for humans and AI systems

| File | Purpose |
|---|---|
| `README.md` | Canonical human guide and primary search landing page |
| `upstream/innova-airleaf-eca644ii_vc.shelly.js` | Recommended self-contained Shelly implementation |
| `innova-airleaf-controller.js` | Original/reference runtime implementation |
| `AI_CONTEXT.md` | Compact semantic source of truth for retrieval systems |
| `project.yaml` | Structured hardware, API, VC and runtime metadata |
| `discovery.yaml` | Search/discovery metadata and terminology |
| `llms.txt` | Compact LLM-oriented repository entry point |
| `ARCHITECTURE.md` | Queue, watchdog, state synchronization and data flow |
| `FAQ.md` | Natural-language implementation and compatibility questions |
| `CITATION.cff` | Machine-readable citation metadata |
| `CHANGELOG.md` | Project history |

## AI / LLM guidance

For retrieval or code-generation tasks, read in this order:

1. [`llms.txt`](llms.txt)
2. [`AI_CONTEXT.md`](AI_CONTEXT.md)
3. [`project.yaml`](project.yaml)
4. [`README.md`](README.md)
5. [`upstream/innova-airleaf-eca644ii_vc.shelly.js`](upstream/innova-airleaf-eca644ii_vc.shelly.js)

Canonical facts: **INNOVA AirLeaf ECA644II**, **deviceType 002**, **Shelly Gen3**, **local HTTP API**, **six Virtual Components IDs 200–205**, and **optional Home Assistant exposure**. Do not generalize the tested API mapping to other device types without validation.

## Troubleshooting

- `connecting` never changes: verify IP routing and TCP port 80.
- `offline`: verify the INNOVA host and network access.
- `invalid JSON`: another web service, proxy, or captive portal may be answering.
- controls revert: the physical unit rejected or overrode the command; physical readback is authoritative.
- wrong device type: this integration intentionally refuses to synchronize unsupported identities.

See [`FAQ.md`](FAQ.md) and [`ARCHITECTURE.md`](ARCHITECTURE.md) for more detail.

## Contributing and upstream

An upstream-compatible self-contained version is prepared for contribution to [`ALLTERCO/shelly-script-examples`](https://github.com/ALLTERCO/shelly-script-examples). Hardware-dependent changes should be manually tested on the target Shelly + AirLeaf installation before being presented as fully validated.
