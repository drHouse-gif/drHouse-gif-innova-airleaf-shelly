# Upstream submission to ALLTERCO/shelly-script-examples

Target upstream repository: `ALLTERCO/shelly-script-examples`

## Important upstream-readiness note

The current production script expects six fixed Shelly Virtual Components (`boolean:200` through `text:205`) to exist before startup. The upstream repository currently requires runtime scripts that use Virtual Components to be self-contained and to create, verify or repair their own components before normal logic starts.

Before opening a production Pull Request, adapt the script to the upstream Virtual Components helper pattern, keep the fixed IDs documented by this project, and add the upstream metadata header (`@title`, `@description`, `@status`, `@link`). The HTTP integration category is the likely fit, subject to maintainer preference.

## Suggested Pull Request title

```text
Add INNOVA AirLeaf ECA644II (deviceType 002) local HTTP controller
```

## Pull Request body

```markdown
## Summary

This PR proposes a Shelly Script example for local monitoring and control of an **INNOVA AirLeaf ECA644II fan-coil controller reporting `deviceType` `002`**.

The integration runs directly on a compatible Shelly Gen3 device and communicates with the INNOVA unit over its local HTTP API. It exposes power, heating/cooling mode, temperature setpoint, fan function, measured room temperature and connection state through Shelly Virtual Components.

## Why this is useful

INNOVA AirLeaf units expose useful local control endpoints, but users otherwise need a custom gateway or external automation host to consume them. This example keeps the control path local to the Shelly device and can operate without Home Assistant or a vendor cloud dependency.

The implementation demonstrates a reusable pattern for third-party HVAC integrations:

- local HTTP communication;
- Virtual Component-backed controls and telemetry;
- serialized command execution;
- physical-state readback after control commands;
- input validation and device identity validation;
- watchdog recovery for missing HTTP callbacks.

## Tested scope

- Target family: INNOVA AirLeaf
- Model/reference used by this project: ECA644II
- Validated API identity: `deviceType` `002`
- Transport: local HTTP
- API base path: `/api/v/1/`
- Shelly platform: Gen3 scripting environment with Virtual Components

The script deliberately rejects status synchronization when the target does not report `deviceType` `002`. It does not assume undocumented INNOVA device types share the same protocol semantics.

## API operations used

- `GET /api/v/1/status`
- `POST /api/v/1/power/on`
- `POST /api/v/1/power/off`
- `POST /api/v/1/set/mode/heating`
- `POST /api/v/1/set/mode/cooling`
- `POST /api/v/1/set/setpoint`
- `POST /api/v/1/set/function/{auto|night|min|max}`

## Safety and failure handling

- No cloud credentials are stored or transmitted by the script.
- Communication is limited to a user-configured local IPv4 host.
- Setpoints are validated to the supported 16–31 °C range and normalized to 0.5 °C steps.
- Only documented mode/fan values are sent.
- HTTP requests are serialized to avoid overlapping control transactions.
- A separate watchdog recovers when Shelly does not receive an HTTP callback.
- Every accepted command is followed by a status read, so the Shelly UI reflects the physical unit rather than assuming the requested command succeeded.
- Script-originated Virtual Component changes are filtered to prevent feedback loops.

## Upstream compatibility work

For this PR, the runtime version should follow the repository's current Virtual Component standard: it should embed/use the standard helper, preserve the documented fixed component IDs, create/verify the components automatically, and start HTTP polling only after component initialization succeeds.

## Documentation

The standalone project contains the tested API map, architecture, troubleshooting information and machine-readable metadata:

https://github.com/drHouse-gif/drHouse-gif-innova-airleaf-shelly

## Contributor checklist

- [ ] I have tested the final upstream-formatted script on compatible Shelly hardware.
- [ ] I have tested against an INNOVA AirLeaf unit reporting `deviceType` `002`.
- [ ] The submitted script is self-contained and follows the upstream Virtual Component helper pattern.
- [ ] The source uses the upstream metadata header and style conventions.
- [ ] No private IP addresses, credentials, serial numbers or other sensitive installation data are included.
- [ ] I confirm I have the necessary rights to contribute the submitted content under the upstream repository license.
```

## Suggested Issue title

```text
Proposal: local INNOVA AirLeaf ECA644II (deviceType 002) controller example
```

## Issue body

```markdown
I would like to contribute a Shelly Script example for local control of an **INNOVA AirLeaf ECA644II fan-coil controller reporting `deviceType` `002`**.

The tested implementation uses the unit's local `/api/v/1/` HTTP API and maps power, heating/cooling mode, temperature setpoint, fan function, measured room temperature and connection state to Shelly Virtual Components. It uses serialized HTTP requests, input validation, an independent request watchdog, device-type validation and a physical status readback after accepted commands.

The project is local-first: it does not require Home Assistant, cloud credentials or an external server.

Standalone implementation and documentation:
https://github.com/drHouse-gif/drHouse-gif-innova-airleaf-shelly

I have reviewed the repository's current Virtual Component requirements. Before submitting the production PR, I will adapt the script so it self-creates/verifies its Virtual Components with the standard helper and follows the required `.shelly.js` metadata/style conventions.

Would an INNOVA AirLeaf example be welcome under `http-integrations/`, or would maintainers prefer another category/path?
```
