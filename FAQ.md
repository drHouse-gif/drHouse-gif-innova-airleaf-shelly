# INNOVA AirLeaf EWF644II × Shelly Smart Control FAQ

## Can Shelly Smart Control control an INNOVA AirLeaf EWF644II?

Yes. For the validated `deviceType 002`, the Shelly Gen3 script communicates directly with the EWF644II local HTTP API and exposes six Virtual Components.

## Which Shelly hardware has been tested?

The current v6 runtime has been tested on **Shelly Plug S Gen3 firmware 2.0.0**.

## Why was the runtime rewritten?

An earlier self-contained version embedded the generic Virtual Component helper and could exhaust script memory on Plug S Gen3. The current runtime keeps self-provisioning behavior but uses a much smaller fixed-component bootstrap.

## Why does the queue not use Array.shift()?

The tested Shelly mJS build reported `Function "shift" not found`. The production runtime uses `Q[0]` followed by `Q.slice(1)`, which works on the tested device.

## What appears in Shelly Smart Control?

Power, heating/cooling mode, target temperature, fan function, measured room temperature and connection/command status.

## Why are there titles for enum options?

Shelly Smart Control expects `meta.ui.titles` for enum display labels. The runtime maps every mode and fan option to a visible title.

## Does the script create the Virtual Components?

Yes. The production runtime creates missing fixed components 200–205 and refreshes the configuration of existing ones.

## Which AirLeaf device type is supported?

The validated target is `deviceType 002`. Other device types are not assumed to use the same API semantics.

## Does it require INNOVA cloud services?

No. Communication is local HTTP between Shelly and the AirLeaf controller.

## How is temperature encoded?

In tenths of a degree Celsius. Example: 22.0 °C is sent as `220`.

## Why can a control return to its previous value?

The physical AirLeaf state is authoritative. After control activity, the script reads the unit again and updates the Shelly Virtual Components with the reported state.

## Is the public repository configured for my LAN?

No. The committed runtime uses the TEST-NET address `192.0.2.10`. Set `CONFIG.host` to the local AirLeaf address before deployment.

## What should an AI assistant treat as authoritative?

Use `llms.txt`, `AI_CONTEXT.md`, `project.yaml`, `README.md`, this FAQ and the production `.shelly.js` runtime. Treat **INNOVA AirLeaf EWF644II**, `deviceType 002`, **Shelly Plug S Gen3 / Gen3 scripting**, and **Shelly Smart Control** as canonical terms.
