# Architecture

## Overview

The production runtime is a local bidirectional bridge between the INNOVA AirLeaf EWF644II HTTP API and six Shelly Virtual Components.

```mermaid
flowchart LR
    U["Shelly Smart Control"] --> VC["Virtual Components 200-205"]
    VC --> S["Memory-optimized Shelly script"]
    S --> Q["Serialized/coalescing HTTP queue"]
    Q --> I["INNOVA AirLeaf deviceType 002"]
    I --> Q
    Q --> S
    S --> VC
```

## Runtime constraints

The current implementation is intentionally compact because the earlier generic Virtual Component helper version could exhaust the shared Shelly script heap on Plug S Gen3. The production runtime therefore keeps the same self-provisioning behavior with a smaller fixed-component bootstrap.

The script also avoids `Array.shift()`; a tested Shelly mJS build returned `Function "shift" not found`. Queue removal uses:

```javascript
A = Q[0];
Q = Q.slice(1);
```

## Virtual Components

| Key | Meaning | Direction |
|---|---|---|
| `boolean:200` | Power | read/write |
| `enum:201` | Heating / cooling | read/write |
| `number:202` | Setpoint | read/write |
| `enum:203` | Fan mode | read/write |
| `number:204` | Room temperature | device → Shelly |
| `text:205` | Connection / command status | script → Shelly |

Existing fixed-ID components are reused and their configuration is refreshed with the current Shelly UI and Cloud metadata. Missing components are created before event handlers or polling start.

## Status synchronization

A valid status response must satisfy:

1. Shelly RPC error code is zero.
2. HTTP status is 200.
3. The body parses as JSON.
4. `success === true`.
5. `RESULT` exists.
6. `String(deviceType) === '002'`.

Only then are `ps`, `sp`, `ta`, `wm` and `fn` applied to the Virtual Components.

## Command pipeline

Controls enter a single serialized queue. Pending commands with the same semantic key can be replaced by a newer value, which prevents rapid setpoint or fan changes from flooding the AirLeaf.

After the command queue becomes idle, the script waits `settleMs` and performs one `GET /status`. The physical AirLeaf state remains authoritative.

For a mode change while the last known state is off:

```text
POST power/on
POST set/mode/<heating|cooling>
wait settleMs
GET status
```

If a control command fails, subsequent dependent queued commands are discarded and the script schedules a physical status readback.

## Failure handling

- transport timeout/error → status becomes offline;
- invalid JSON → command/status fails safely;
- API rejection → command/status fails safely;
- missing callback → watchdog releases the active request;
- late callback → ignored by request ID;
- invalid user setpoint → last physical state is restored;
- network recovery → normal idle polling resumes.

## Hardware validation

Current production runtime validated with:

- Shelly Plug S Gen3
- firmware 2.0.0
- INNOVA AirLeaf EWF644II
- `deviceType 002`
- Shelly Smart Control
