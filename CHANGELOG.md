# Changelog

All notable changes to this project are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [6.0.0] - 2026-09-18

### Changed

- Replace the generic Virtual Component helper runtime with a compact self-contained bootstrap for lower Shelly Gen3 memory use.
- Fix Shelly mJS compatibility by replacing `Array.shift()` queue removal with indexed access plus `slice(1)`.
- Add enum UI title mappings for heating/cooling and all fan modes so Shelly Smart Control renders valid labels.
- Add Shelly Cloud `measurement` / `log` metadata to the Virtual Components.
- Add pending-command coalescing and a short settle window so rapid UI changes produce one final physical status readback.
- Abort dependent queued control commands after a command failure.
- Keep request-ID protection, HTTP watchdog handling and physical state reconciliation.

### Validated

- Shelly Plug S Gen3, firmware 2.0.0.
- INNOVA AirLeaf EWF644II reporting `deviceType 002`.
- Power, heating/cooling mode, 0.5 °C setpoint control, fan mode, room temperature and status display in Shelly Smart Control.

### Notes

- The repository runtime uses TEST-NET `192.0.2.10`; installers must set `CONFIG.host` to the local AirLeaf address.
- The older `innova-airleaf-controller.js` remains for reference only.

## [5.0.1] - 2026-08-17

### Added

- Local status polling for INNOVA AirLeaf `deviceType` `002`.
- Power on and power off control.
- Heating and cooling mode selection.
- Temperature setpoint control from 16 °C to 31 °C.
- Auto, night, minimum and maximum fan-function control.
- Room-temperature reporting.
- Six-component Shelly Virtual Component interface.
- Serialized HTTP request queue.
- Queue capacity protection.
- Independent HTTP callback watchdog.
- Request identifier protection against late callbacks.
- Status verification after accepted control commands.
- Validation of HTTP status, JSON response, API success and device type.
- Prevention of script-generated Virtual Component feedback loops.
- English source documentation and GitHub installation guide.

### Compatibility

- Designed for Shelly Gen3 scripting environments with Virtual Components.
- Tested against an INNOVA AirLeaf controller reporting `deviceType 002`.
