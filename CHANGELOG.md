# Changelog

All notable changes to this project are documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

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
- Tested against an INNOVA AirLeaf controller reporting `deviceType` `002`.

### Known limitations

- Virtual Components must exist before the script starts.
- Component keys are fixed in the `start()` function.
- The local API connection uses unencrypted HTTP.
- Only AirLeaf `deviceType` `002` is accepted.

