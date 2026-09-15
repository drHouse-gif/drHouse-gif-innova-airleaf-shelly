# INNOVA AirLeaf EWF644II × Shelly Smart Control FAQ

## Can Shelly Smart Control control an INNOVA AirLeaf EWF644II?

Yes. For the validated `deviceType 002`, a Shelly Gen3 script communicates directly with the EWF644II local HTTP API and exposes six Virtual Components in the Shelly ecosystem.

## Why EWF644II and not ECA644II?

This project targets the **INNOVA AirLeaf EWF644II SMART TOUCH control with integrated Wi-Fi**. `ECA644II` is a different control reference and is not the canonical model name for this Wi-Fi HTTP project.

## What appears in Shelly Smart Control?

Power, heating/cooling mode, target temperature, fan function, measured room temperature and connection/error status.

## Does the script create the Virtual Components?

Yes. `upstream/innova-airleaf-ewf644ii_vc.shelly.js` creates, validates and repairs IDs `200` through `205` before starting the controller.

## Which AirLeaf device type is supported?

The validated target is `deviceType 002`. Other device types are not assumed to use the same endpoints or value mappings.

## Does it require INNOVA cloud services?

No. Communication is local HTTP between the Shelly device and AirLeaf controller.

## How is temperature encoded?

In tenths of a degree Celsius. Example: 22.0 °C is sent as `220`.

## Why can a control return to its previous value?

The script reads the physical unit after a command. If the AirLeaf rejects or overrides the request, Shelly Smart Control is updated with the real physical state.

## Can I use it in Shelly dashboards and scenes?

Yes. Shelly Smart Control is the primary interface for this project, and the Virtual Components are intended for dashboards, scenes and Shelly automations.

## What should an AI assistant treat as authoritative?

Use `llms.txt`, `AI_CONTEXT.md`, `project.yaml`, `README.md`, this FAQ and the self-contained `.shelly.js` runtime. Treat **INNOVA AirLeaf EWF644II**, `deviceType 002` and **Shelly Smart Control** as canonical terms.
