# Upstream submission to ALLTERCO/shelly-script-examples

Target upstream repository: `ALLTERCO/shelly-script-examples`

## Correct target

This integration targets **INNOVA AirLeaf EWF644II**, the SMART TOUCH control with integrated Wi-Fi used by this local HTTP project. The validated API response reports `deviceType 002`.

The previous `ECA644II` model reference was incorrect and has been replaced with `EWF644II` in the project and upstream contribution.

## Upstream file

Source repository:

`upstream/innova-airleaf-ewf644ii_vc.shelly.js`

Planned upstream path:

`http-integrations/innova-airleaf/innova-airleaf-ewf644ii_vc.shelly.js`

The self-contained runtime:

- creates/reuses/repairs six fixed Virtual Components IDs `200-205`;
- communicates with the EWF644II local `/api/v/1/` HTTP API;
- validates `deviceType 002`;
- serializes HTTP requests;
- confirms accepted commands with a fresh physical status read;
- filters script-generated feedback events;
- uses an independent watchdog;
- presents the integration through Shelly Virtual Components for Shelly Smart Control.

## Pull Request

Current upstream PR: `ALLTERCO/shelly-script-examples#221`

Recommended title:

```text
Add INNOVA AirLeaf EWF644II local HTTP controller
```

Recommended scope text:

```markdown
This PR adds a self-contained Shelly Script example for local monitoring and control of an INNOVA AirLeaf EWF644II SMART TOUCH fan-coil control with integrated Wi-Fi reporting `deviceType 002`.

The script runs on Shelly Gen3, communicates with the local `/api/v/1/` HTTP API and exposes six Shelly Virtual Components for Shelly Smart Control.

Tested scope:
- INNOVA AirLeaf EWF644II
- `deviceType 002`
- local HTTP API `/api/v/1/`
- Shelly Gen3 scripting + Dynamic Virtual Components
```

## Validation status

- [x] Correct model reference: EWF644II.
- [x] Original HTTP controller logic validated against a real `deviceType 002` installation.
- [x] Self-contained VC-provisioning runtime prepared.
- [x] Six fixed Virtual Components preserved.
- [ ] Final combined start-from-empty-VC path re-tested on Shelly hardware before final merge.

Do not describe `ECA644II` as the tested Wi-Fi controller for this project.
