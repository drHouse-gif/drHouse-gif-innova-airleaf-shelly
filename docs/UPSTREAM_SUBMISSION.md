# Upstream submission to ALLTERCO/shelly-script-examples

Target upstream repository: `ALLTERCO/shelly-script-examples`

## Current status

The upstream-formatted self-contained runtime is ready at:

`upstream/innova-airleaf-eca644ii_vc.shelly.js`

Planned upstream path:

`http-integrations/innova-airleaf/innova-airleaf-eca644ii_vc.shelly.js`

The file follows the current Shelly Script Examples pattern:

- `.shelly.js` machine-readable header with `@title`, `@description`, `@status` and `@link`;
- embedded standard `ensureVirtualComponents(...)` helper pattern;
- six fixed Virtual Components are created/reused/repaired before HTTP runtime starts;
- no manual VC provisioning is required;
- local HTTP control remains serialized;
- device identity is checked for `deviceType 002`;
- accepted commands are confirmed with a fresh physical status read;
- feedback-loop and watchdog protections remain enabled.

The integrated self-contained file has passed JavaScript syntax validation. The original HTTP controller logic was validated against a real `deviceType 002` installation. A final on-device test of the newly combined automatic VC provisioning path is still recommended before requesting final upstream merge.

## Pull Request title

```text
Add INNOVA AirLeaf ECA644II (deviceType 002) local HTTP controller
```

## Pull Request body

```markdown
## Summary

This PR adds a self-contained Shelly Script example for local monitoring and control of an **INNOVA AirLeaf ECA644II fan-coil controller reporting `deviceType` `002`**.

The integration runs directly on a compatible Shelly Gen3 device and communicates with the INNOVA unit over its local `/api/v/1/` HTTP API. It exposes power, heating/cooling mode, temperature setpoint, fan function, measured room temperature and connection state through Shelly Virtual Components.

## Why this is useful

The example provides a local-first HVAC integration without requiring Home Assistant, an external gateway, cloud credentials or a continuously running automation server. It also demonstrates a reusable pattern for third-party HTTP HVAC devices.

## Upstream compatibility

- Self-contained single `.shelly.js` file.
- Embeds the standard Virtual Component helper pattern.
- Creates/reuses/repairs fixed IDs `200-205` before runtime startup.
- Uses the required metadata header.
- Runtime polling starts only after VC initialization succeeds.

## Reliability / safety behavior

- Validates `deviceType 002` before applying physical state.
- Serializes HTTP requests to prevent overlapping control transactions.
- Normalizes temperature commands to 0.5 C and validates 16-31 C.
- Confirms every accepted command with a fresh status request.
- Ignores script-generated VC events to prevent feedback loops.
- Uses an independent watchdog for missing HTTP callbacks.
- Does not expose or require cloud credentials.

## Tested scope

- INNOVA family: AirLeaf
- Model/reference: ECA644II
- Validated API identity: `deviceType 002`
- Transport: local HTTP
- API base: `/api/v/1/`
- Shelly: Gen3 scripting with Dynamic Virtual Components

Standalone project, protocol map and architecture:
https://github.com/drHouse-gif/drHouse-gif-innova-airleaf-shelly

## Contributor checklist

- [x] Self-contained upstream-format script prepared.
- [x] Standard Virtual Component helper pattern integrated.
- [x] Required metadata header included.
- [x] JavaScript syntax validation passed.
- [x] Original controller logic validated against a real `deviceType 002` installation.
- [ ] Final combined self-provisioning path re-tested on Shelly hardware before final merge.
- [x] No private IP addresses, credentials, serial numbers or installation secrets included.
```

## Submission status

The connected GitHub App can read `ALLTERCO/shelly-script-examples` but currently has no permission to create issues or Pull Requests there. GitHub also requires the PR head repository to be a fork of the upstream repository. Once a fork exists under `drHouse-gif`, copy this file to the planned upstream path and open the PR with the text above.
