# Upstream submission to ALLTERCO/shelly-script-examples

Target repository: `ALLTERCO/shelly-script-examples`

## Current submission state

The contribution branch has been refreshed with the current hardware-tested runtime:

`drHouse-gif/shelly-script-examples:feature/innova-airleaf-eca644ii`

The branch name is legacy, but its contents now target **INNOVA AirLeaf EWF644II** and contain the current v6 runtime.

Existing upstream pull request:

`ALLTERCO/shelly-script-examples#221`

The PR is currently closed and not merged. Its head branch now contains the updated files, so reopening the PR will present the refreshed implementation.

Recommended PR title:

```text
Add INNOVA AirLeaf EWF644II local HTTP controller
```

## Validated scope

- INNOVA AirLeaf EWF644II
- SMART TOUCH with integrated Wi-Fi
- `deviceType 002`
- Shelly Plug S Gen3
- firmware 2.0.0
- Shelly Smart Control
- local HTTP API `/api/v/1/`

## Upstream files

- `http-integrations/innova-airleaf/README.md`
- `http-integrations/innova-airleaf/innova-airleaf-ewf644ii_vc.shelly.js`
- `http-integrations/README.md`
- `CHANGELOG.md`
- `examples-manifest.json`

## Current runtime improvements

The refreshed contribution:

- replaces the higher-memory generic-helper implementation with a compact fixed-component bootstrap;
- creates or reuses six Virtual Components;
- adds Shelly Smart Control enum titles;
- adds Shelly Cloud measurement/log metadata;
- serializes HTTP requests;
- coalesces pending same-control changes;
- performs delayed physical status readback after commands;
- validates `deviceType 002`;
- aborts dependent queued control commands after a command failure;
- protects against stale callbacks with request IDs and a watchdog;
- avoids `Array.shift()` for compatibility with the tested Shelly mJS runtime;
- uses a TEST-NET address in public source instead of installation-specific IPs.

## Pull-request summary

Suggested body:

```markdown
Adds a production Shelly Script example for local monitoring and control of an INNOVA AirLeaf EWF644II SMART TOUCH fan-coil controller reporting `deviceType 002`.

Validated on Shelly Plug S Gen3 firmware 2.0.0 with Shelly Smart Control.

The script exposes Power, heating/cooling mode, target temperature, fan mode, room temperature and connection status as Shelly Virtual Components. It uses a compact self-contained VC bootstrap, serialized/coalesced HTTP commands, physical-state readback, enum UI titles, Shelly Cloud metadata, a watchdog and stale-callback protection.
```

## Validation status

- [x] Correct model reference: EWF644II.
- [x] Real `deviceType 002` protocol validated.
- [x] Current self-contained runtime tested on Shelly Plug S Gen3.
- [x] Shelly Smart Control enum labels validated.
- [x] Memory-reduced runtime replaces the previous OOM-prone helper build.
- [x] Public source uses a TEST-NET address.
- [x] Manifest, changelog and HTTP integration index prepared.
- [ ] Upstream PR #221 must be reopened or replaced from the GitHub UI because the connected GitHub integration does not have permission to modify pull requests in the ALLTERCO repository.
