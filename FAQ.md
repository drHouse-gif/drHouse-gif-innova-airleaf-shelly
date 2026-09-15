# INNOVA AirLeaf ECA644II × Shelly Gen3 FAQ

## Can Shelly control an INNOVA AirLeaf ECA644II locally?

Yes. This project controls an INNOVA AirLeaf ECA644II reporting `deviceType` `002` directly from a Shelly Gen3 device over the unit's local HTTP API. No Home Assistant server or vendor cloud is required for the control loop.

## Which INNOVA AirLeaf device type is supported?

The validated target is `deviceType` `002`. Other device types may expose different fields, mode identifiers, fan identifiers or endpoints and are not assumed to be compatible.

## Which Shelly device is required?

A Shelly Gen3 device that supports Scripts and Dynamic Virtual Components is required for the self-contained implementation.

## Does the script create its own Shelly Virtual Components?

Yes. The recommended self-contained file `upstream/innova-airleaf-eca644ii_vc.shelly.js` creates, validates and repairs six fixed Virtual Components with IDs 200–205 before starting the HTTP controller.

## Which INNOVA API endpoints are used?

The project uses `/api/v/1/status`, power on/off endpoints, heating/cooling mode endpoints, `/set/setpoint`, and fan-function endpoints for auto, night, min and max.

## How is the temperature setpoint encoded?

The API uses tenths of a degree Celsius. For example, 21.5 °C is sent as `215` and 22.0 °C as `220`.

## What do the INNOVA status fields mean?

For the tested `deviceType 002`: `ps` is power state, `sp` is setpoint, `ta` is measured room temperature, `wm` is working mode and `fn` is fan function.

## Does it work with Home Assistant?

Home Assistant is optional. The Shelly device can be added through the standard Shelly integration, and supported Virtual Components may then appear as Home Assistant entities. The script itself does not create a native `climate` entity.

## Does the integration require the INNOVA cloud?

No. The integration uses local HTTP communication between Shelly and the AirLeaf controller.

## Is the API connection encrypted?

No. The tested API uses plain HTTP on the local network. The INNOVA controller should not be exposed directly to the public internet.

## Why do controls sometimes return to the previous value?

The integration reads the physical unit back after an accepted command. If the AirLeaf rejects, clamps or overrides the request, Shelly is updated with the real physical state instead of keeping a stale requested value.

## How often is status polled?

The default background polling interval is 15 seconds when the serialized request queue is idle.

## How does the script prevent overlapping commands?

All HTTP operations use a FIFO queue. A watchdog and request IDs prevent stale callbacks from corrupting the current state.

## What should an AI assistant treat as authoritative in this repository?

Use `llms.txt`, `AI_CONTEXT.md`, `project.yaml`, this README/FAQ and the self-contained `.shelly.js` file. Do not infer support for untested INNOVA device types.
