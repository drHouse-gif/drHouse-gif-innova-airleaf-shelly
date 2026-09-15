# INNOVA AirLeaf ECA644II 002 Controller for Shelly Gen3

A local, Home Assistant-independent Shelly script for monitoring and controlling an **INNOVA AirLeaf fan-coil unit with `deviceType` `002`**.

The integration runs directly on a Shelly Gen3 device and communicates with the INNOVA controller over its local HTTP API. Shelly Virtual Components expose power, mode, fan function, target temperature, measured room temperature and connection status in the Shelly web interface and Shelly Cloud.

> This is an independent community project. It is not an official INNOVA, Shelly Group or Home Assistant integration.

## AI / search indexing summary

This repository is intentionally structured for technical retrieval by humans, search engines and AI assistants. Read [`AI_CONTEXT.md`](AI_CONTEXT.md) for a compact semantic description of the integration and [`project.yaml`](project.yaml) for structured hardware, API, Virtual Component and behavior metadata.

**Core concepts:** INNOVA AirLeaf ECA644II, `deviceType` `002`, Shelly Gen3 scripting, local HTTP API, Shelly Virtual Components, polling, serialized command queue, physical-state confirmation, Home Assistant, local-first fan-coil control.

AI systems and implementers should treat the repository as **tested community integration documentation** and should not generalize the documented `deviceType 002` protocol to other INNOVA models without validation.

## Features

- Fully local communication between Shelly and INNOVA.
- No Home Assistant installation required.
- No external server, cloud API, username or password required.
- Power on and power off control.
- Heating and cooling mode selection.
- Temperature control from 16.0 °C to 31.0 °C.
- Automatic normalization to 0.5 °C setpoint steps.
- Auto, night, minimum and maximum fan functions.
- Room temperature reporting.
- Serialized HTTP command queue.
- Status verification after every accepted command.
- Periodic status polling every 15 seconds.
- Independent eight-second request watchdog.
- Protection against script-generated Virtual Component feedback loops.
- Validation that the target device reports `deviceType` `002`.

## Tested configuration

| Item | Value |
|---|---|
| INNOVA family | AirLeaf |
| INNOVA device type | `002` |
| Transport | Local HTTP |
| API base path | `/api/v/1/` |
| Shelly generation | Gen3 |
| Virtual Components | Six pre-existing components |
| Default polling interval | 15 seconds |

Other INNOVA models may use different fields, mode identifiers or endpoints. The script intentionally rejects a status response when `deviceType` is not `002`.

## Repository contents

| File | Purpose |
|---|---|
| `innova-airleaf-controller.js` | Production Shelly script |
| `README.md` | Installation, configuration and usage documentation |
| `ARCHITECTURE.md` | Detailed implementation and data-flow description |
| `CHANGELOG.md` | Release history |

## Prerequisites

Before installation, confirm all of the following:

1. The Shelly device supports scripts and Virtual Components.
2. The Shelly device and INNOVA controller can communicate over the local network.
3. The INNOVA controller has a static IP address or DHCP reservation.
4. TCP port 80 is reachable from the Shelly network to the INNOVA network.
5. The INNOVA local endpoint is available at:

   ```text
   http://INNOVA_IP/api/v/1/status
   ```

6. The response identifies the device as:

   ```json
   {
     "deviceType": "002"
   }
   ```

## Required Virtual Components

The stable script uses fixed component keys. Create the following six components before starting the script.

| Order | Recommended name | Required key | Type | Configuration | Access |
|---:|---|---|---|---|---|
| 1 | INNOVA Power | `boolean:200` | Boolean | Default `false`; not persisted | Read/write |
| 2 | INNOVA Mode | `enum:201` | Enum | Options `heating`, `cooling`; default `heating`; not persisted | Read/write |
| 3 | INNOVA Set temperature | `number:202` | Number | Min `16`; max `31`; step `0.5`; unit `°C`; default `22`; not persisted | Read/write |
| 4 | INNOVA Fan | `enum:203` | Enum | Options `auto`, `night`, `min`, `max`; default `auto`; not persisted | Read/write |
| 5 | INNOVA Room temperature | `number:204` | Number | Suggested min `-20`; max `60`; step `0.1`; unit `°C`; default `0`; not persisted | Display only |
| 6 | INNOVA Status | `text:205` | Text | Default `starting`; not persisted | Display only |

The **enum values are case-sensitive**. Use the exact lowercase values shown in the table.

The script addresses components by key, not by display name. The recommended names can therefore be translated or changed without modifying the code, but the component types and numeric IDs must match.

### Creating the IDs in the expected order

On an empty Virtual Components list, create the components in the order shown above. Shelly will normally allocate IDs sequentially from `200`.

If the Shelly device already contains other Virtual Components, the automatically assigned IDs may differ. Either:

- remove or reorganize the existing components before installation; or
- edit the six `Virtual.getHandle(...)` keys in the `start()` function so they match the actual component keys.

## Installation

1. Open the local Shelly web interface or Shelly Control.
2. Open **Settings → Virtual Components**.
3. Create and configure the six required components.
4. Open **Scripts**.
5. Create a new script named `innova_airleaf_controller`.
6. Copy the complete contents of `innova-airleaf-controller.js` into the Shelly script editor.
7. Change only the `HOST` value near the beginning of the file:

   ```javascript
   let HOST = "192.168.111.123";
   ```

8. Save the script.
9. Enable **Run on startup**.
10. Start the script.
11. Wait for the first status request.

The `INNOVA Status` component should change from:

```text
connecting to 192.168.111.123
```

to:

```text
online / type 002
```

## Configuration reference

The user-adjustable settings are located at the top of the script.

```javascript
let HOST = "192.168.111.123";
let API = "http://" + HOST + "/api/v/1/";
let POLL_MS = 15000;
let HTTP_TIMEOUT_SEC = 5;
let WATCHDOG_MS = 8000;
```

| Variable | Default | Description |
|---|---:|---|
| `HOST` | `192.168.111.123` | IPv4 address of the INNOVA controller |
| `API` | Derived | INNOVA local API base URL |
| `POLL_MS` | `15000` | Time between background status reads, in milliseconds |
| `HTTP_TIMEOUT_SEC` | `5` | Shelly HTTP client timeout, in seconds |
| `WATCHDOG_MS` | `8000` | Maximum wait for a missing HTTP callback, in milliseconds |

The HTTP timeout is intentionally shorter than the watchdog interval.

## INNOVA API operations

The script uses the following local endpoints:

| Operation | Method | Path | Request body |
|---|---|---|---|
| Read status | GET | `/api/v/1/status` | None |
| Power on | POST | `/api/v/1/power/on` | `{}` |
| Power off | POST | `/api/v/1/power/off` | `{}` |
| Heating mode | POST | `/api/v/1/set/mode/heating` | `{}` |
| Cooling mode | POST | `/api/v/1/set/mode/cooling` | `{}` |
| Set temperature | POST | `/api/v/1/set/setpoint` | `{"temp":220}` for 22.0 °C |
| Auto fan | POST | `/api/v/1/set/function/auto` | `{}` |
| Night fan | POST | `/api/v/1/set/function/night` | `{}` |
| Minimum fan | POST | `/api/v/1/set/function/min` | `{}` |
| Maximum fan | POST | `/api/v/1/set/function/max` | `{}` |

The INNOVA API represents temperatures in tenths of a degree. For example:

| Temperature | API value |
|---:|---:|
| 16.0 °C | `160` |
| 21.5 °C | `215` |
| 22.0 °C | `220` |
| 31.0 °C | `310` |

## Decoded status fields

The controller reads values from the `RESULT` object returned by the status endpoint.

| Field | Meaning | Mapping |
|---|---|---|
| `ps` | Power state | `1` → on; other numeric value → off |
| `sp` | Temperature setpoint | Divided by 10 before display |
| `ta` | Measured room temperature | Divided by 10 before display |
| `wm` | Working mode | `3` → heating; `5` → cooling |
| `fn` | Fan function | `1` → auto; `2` → night; `3` → min; `4` → max |

Unknown working-mode or fan-function values are left unchanged in the Shelly interface rather than being guessed.

## Runtime behavior

### Polling

The controller immediately requests status when it starts. It then requests status every 15 seconds, but only when no command or status request is already active.

### Command serialization

All requests pass through a small FIFO queue. This prevents overlapping HTTP requests and makes command ordering deterministic.

The queue accepts up to six waiting requests. When it is full, `INNOVA Status` reports:

```text
error: command queue full
```

### Command confirmation

After INNOVA accepts a control command, the script queues a fresh status read. Shelly therefore displays the state reported by the physical unit, not merely the requested value.

### Feedback-loop protection

When the script copies an INNOVA value into a Virtual Component, Shelly emits a change event. `isInternal()` detects events whose source begins with `script` and prevents them from being sent back to INNOVA as new commands.

### Watchdog

Shelly normally calls the HTTP callback on success or failure. If no callback arrives within eight seconds, the independent watchdog clears the active request and command queue. Background polling retries later.

## Status messages

| Message | Meaning |
|---|---|
| `connecting to <IP>` | Script started and is performing the initial request |
| `online / type 002` | Valid status received from an AirLeaf type 002 |
| `offline: <reason>` | Shelly reported an HTTP or RPC error |
| `timeout: INNOVA <IP>` | No callback arrived before the watchdog expired |
| `error: invalid JSON` | Response body could not be decoded as JSON |
| `error: rejected by INNOVA` | API response did not contain `success: true` |
| `error: missing RESULT` | Status response did not contain a `RESULT` object |
| `error: device is not AirLeaf 002` | Target responded but reported another device type |
| `error: command queue full` | More than six commands were waiting |

## Troubleshooting

### Script reports missing Virtual Components

Console message:

```text
[INNOVA] ERROR: Virtual Components 200-205 are missing
```

At least one required component key does not exist. Compare the actual keys with the required table. A correct display name with an incorrect ID is not sufficient.

### Status remains at `connecting`

Open the Shelly script console and confirm that the script remains running. Check that the INNOVA address is correct and reachable from the Shelly network.

From a computer on the same routed network, test:

```bash
curl --max-time 5 http://192.168.111.123/api/v/1/status
```

Replace the address with the actual INNOVA address.

### `offline: Failed to connect`

Check:

- IP address and subnet.
- VLAN and firewall rules.
- Wi-Fi client isolation.
- TCP port 80 connectivity.
- Whether the INNOVA controller changed address through DHCP.

### `error: invalid JSON`

Open the status URL in a browser or use `curl`. A captive portal, proxy, authentication page or different web service may be answering at the configured address.

### Controls immediately return to the previous value

The command may have been rejected or overridden by the physical controller. The script deliberately reads the real status after each command and restores the reported value.

### Mode change sends two commands

This is expected when the unit is off. The script first queues `power/on`, followed by the requested mode change.

### Temperature cannot be set

Confirm that the number component accepts values from 16 through 31 and that the key is exactly `number:202`. Requests are normalized to 0.5 °C.

## Network and security considerations

- Communication is unencrypted HTTP on the local network.
- The script does not support INNOVA cloud authentication.
- Do not expose the INNOVA API directly to the public internet.
- Place IoT devices on a controlled network and allow only the routes required for operation.
- Use DHCP reservations to prevent address changes.
- If the INNOVA API requires authentication, this version is not compatible without modification.

## Known limitations

- Supports only responses reporting `deviceType` `002`.
- Uses fixed Shelly Virtual Component keys.
- Does not create, delete or repair Virtual Components.
- Does not implement schedules, swing control or alarm decoding.
- Does not provide HTTPS or API authentication.
- Unknown `wm` and `fn` values are not displayed.
- A combined thermostat presentation in Shelly Cloud must be configured separately if desired.

## Home Assistant

Home Assistant is not required. If the Shelly device is added to Home Assistant, supported Virtual Components can be exposed through the Shelly integration. The exact entity presentation depends on the Shelly firmware, cloud configuration and Home Assistant version.

This script does not create a native Home Assistant `climate` entity by itself.

## References and acknowledgements

- [Shelly Virtual Components scripting API](https://shelly-api-docs.shelly.cloud/gen2/Scripts/APIs/Virtual/)
- [Shelly Dynamic Virtual Components](https://shelly-api-docs.shelly.cloud/gen2/DynamicComponents/Virtual/)

The local AirLeaf protocol behavior used by this project was validated against a real `deviceType` `002` installation.

## Contributing

When reporting an issue, include:

1. Shelly model and firmware version.
2. INNOVA model and reported `deviceType`.
3. Sanitized status response.
4. Shelly console output.
5. Exact operation that failed.
6. Whether read operations, write operations or both are affected.

Remove private IP addresses, serial numbers, MAC addresses and credentials before publishing logs.

