---
name: ntu-machines
description: Discover PUDA machine capabilities in ntu lab and choose the right machines for protocol generation. Use when you need to know more about a machine, how to use a machine, or how to generate commands and protocols for any PUDA-connected machine
---

# PUDA Machines

## Goal

Provide machine-selection and capability guidance for PUDA workflows, then load the correct machine reference before generating commands.

## Critical Rule

If you are unsure which machine should be used for a command, **ask the user** before proceeding.  
Do **not** assume.

## Environment-Scoped Vision Gate

Before a physical NTU workflow whose correctness or safety depends on visible setup, load `puda-machine-vision-validation`. Confirm `puda env current` is `ntu`, use the current NTU machine reference and camera/pose, and capture fresh evidence without movement when possible. Never reuse BEARS or IMRE camera calibration, workspace polygons, coordinates, or prior confirmations.

## Machine Capabilities and When to Use

### First Machine (`machine_id: "first"`)

Use for **liquid handling and deck operations**.

Capabilities:
- Pipetting workflows: aspirate, dispense, attach tip, drop tip
- Deck and labware workflows: load deck, position-dependent operations
- Sequenced robotic handling steps in wet-lab protocols

Use this machine when:
- The task is about moving liquids between wells/labware
- The user mentions tip usage, aspiration/dispensing, or deck slots/labware setup

Before command generation:
- Refer to: [first-machine](references/first-machine.md)
- If visible deck/tray occupancy, labware, tips, tools, or requested positions affect execution, apply `puda-machine-vision-validation` using an NTU-scoped First Machine camera/workspace profile.
- Run `puda machine commands first` to understand available commands
- Follow constraints and sequencing in `references/first-machine.md`

### PipQuBotV3 Machine (`machine_id: "pipqubotv3"`)

Use for **liquid handling and deck operations**.

Capabilities:
- Pipetting workflows: aspirate, dispense, attach tip, drop tip
- Deck and labware workflows: load deck, position-dependent operations
- Sequenced robotic handling steps in wet-lab protocols

Use this machine when:
- The task is about moving liquids between wells/labware
- The user mentions tip usage, aspiration/dispensing, or deck slots/labware setup

Before command generation:
- Refer to: [pipqubotv3-machine](references/pipqubotv3-machine.md)
- If visible deck/tray occupancy, labware, tips, tools, or requested positions affect execution, apply `puda-machine-vision-validation` using an NTU-scoped PipQuBotV3 camera/workspace profile.
- Run `puda machine commands pipqubotv3` to understand available commands
- Follow constraints and sequencing in `references/pipqubotv3-machine.md`

### Biologic Machine (`machine_id: "biologic"`)

Use for **electrochemical testing and characterization**.

Capabilities:
- OCV (Open Circuit Voltage)
- CA (Chronoamperometry)
- PEIS / GEIS (Impedance spectroscopy)
- CV (Cyclic Voltammetry)
- MPP variants (MPP, MPP_Cycles, MPP_Tracking)

Use this machine when:
- The task is an electrochemical measurement or battery/cell characterization
- The user asks for OCV, CA, EIS, CV, or MPP tests

Before command generation:
- Refer to: [biologic-machine](references/biologic-machine.md)
- If the workflow has a suitable camera and explicit NTU cell/fixture/cable map, apply `puda-machine-vision-validation`; imagery does not prove continuity, channel readiness, or electrical limits.
- Run `puda machine commands biologic` to understand available commands
- Follow constraints in `references/biologic-machine.md`

### Centrifuge Machine (`machine_id: "centrifuge"`)

Use for **centrifugation, spin-downs, and phase or pellet separation**.

Capabilities:
- Sample spin-downs before or after handling steps
- Separation workflows based on centrifugal force
- Pelleting, clarification, and phase-separation preparation steps

Use this machine when:
- The user asks to centrifuge, spin, spin down, pellet, or clarify samples

Before command generation:
- Refer to: [centrifuge-machine](references/centrifuge-machine.md)
- If a suitable passive camera exists, apply `puda-machine-vision-validation` to visible rotor/tube occupancy, balance pattern, lid state, and clearance; telemetry/interlocks must still prove stopped/locked state.
- Run `puda machine commands centrifuge` to understand available commands
- Follow constraints in `references/centrifuge-machine.md`

### Dobot M1Pro Machine (`machine_id: "dobot-m1pro"`)

Use for **gripper-based tube transfers between fixed positions**.

Capabilities:
- Pick-and-place tube handling with a gripper
- Moving tubes between predefined source and destination positions
- Position-based transfer steps between pipqubotv3, bioshake, centrifuge machines

Use this machine when:
- The user asks to transfer tubes with a gripper
- The workflow involves moving tubes between known hardcoded positions
- A step references Dobot M1Pro positions such as centrifuge tube or MTP coordinates

Before command generation:
- Refer to: [dobot-m1pro-machine](references/dobot-m1pro-machine.md)
- Before camera-guided transfer, apply `puda-machine-vision-validation` with the current NTU camera pose/calibration, source and destination positions, target tube, gripper state, and keep-out zones; validation-only capture must not move the arm.
- Run `puda machine commands dobot-m1pro` to understand available commands
- Follow constraints in `references/dobot-m1pro-machine.md`

### BioShake Machine (`machine_id: "bioshake"`)

Use for **shaking, heating, and plate clamping operations**.

Capabilities:
- Shaking at a specified RPM for a given duration
- Heating (or cooling) to a target temperature and holding for a given duration
- Clamping and unclamping plates on the shaker
- Homing the shaker to its locked home position

Use this machine when:
- The user asks to shake, vortex, or mix a plate on a shaker
- The user asks to heat or incubate a plate at a specific temperature
- The user asks to clamp or unclamp a plate on the BioShake
- The workflow requires holding a plate at temperature while shaking

Before command generation:
- If visible plate placement, clamp state, or surrounding clearance matters and a suitable passive camera exists, apply `puda-machine-vision-validation`; driver telemetry must still prove stopped/temperature state.
- Run `puda machine commands bioshake` to understand available commands

### Balance Machine

Use for **gravimetric mass measurement via an Arduino-based USB load-cell balance on Linux**.

Capabilities:
- Continuous calibrated mass readings from a load-cell over USB serial (`/dev/ttyUSB*` or `/dev/ttyACM*`)
- Background reader thread streaming readings at ~4 Hz; no polling required
- Tare command to zero the balance before a dispense step
- Freshness check (`fresh` flag) to detect stale/disconnected readings
- NATS telemetry publishing via the edge service
- Custom calibration CSV support

Use this machine when:
- The workflow requires weighing a container before or after a liquid transfer
- The user asks for gravimetric calibration, transfer error calculation, or balance feedback
- The task involves viscosity or transfer accuracy experiments needing mass data

Before use:
- Refer to: [balance-machine](references/balance-machine.md)
- If a suitable passive camera exists and visible vessel/pan placement matters, apply `puda-machine-vision-validation`; still verify tare, calibration, connectivity, and freshness from balance telemetry.
- Ask the user for the **Linux serial port** (`/dev/ttyUSB1`, etc.) — do not assume
- Ensure the edge service is running (`uv run --package balance-edge python edge/balance.py`)

### Opentrons Machine (`machine_id: "opentrons"`)

Use for **automated liquid handling and full protocol generation on the Opentrons OT-2 robot**.

Capabilities:
- Full protocol code generation via `Protocol.to_python_code()` — produces valid runnable OT-2 Python
- Pipetting workflows: `aspirate`, `dispense`, `mix`, `transfer` (with auto-chunking for large volumes)
- Tip management: `pick_up_tip`, `drop_tip`
- Deck and labware setup: `load_labware`, `load_instrument`
- Flow control: `flow_rate`, `air_gap`, `blow_out`, `touch_tip`, `move_to`
- Protocol utilities: `delay`, `comment`, `home`
- CSV-driven loops: `read_csv_file` + `loop` for data-driven protocols
- Custom labware support: AMDM mass balance vials (30 mL, 50 mL) loaded inline
- All gen2 pipette types: p10, p20, p300, p1000 (single and multi-channel)
- **External camera image capture**: `camera_capture` — triggers the external camera mounted above the deck to capture and save a still image of the wellplate

Use this machine when:
- The user references an Opentrons OT-2 robot
- The task involves generating a complete OT-2 protocol or individual liquid handling commands
- The user mentions Opentrons labware (tip racks, well plates, reservoirs, NEST, Corning, mass balance vials)
- The workflow requires data-driven dispensing from a CSV file
- The workflow requires capturing a camera image of the wellplate after dispensing steps

Before command generation:
- Refer to: [opentrons-machine](references/opentrons-machine.md)
- Before physical execution, apply `puda-machine-vision-validation` with an **NTU-scoped** Opentrons camera/deck profile. If `puda-opentrons-vision-validation` is installed, use it only for OT-2 semantics—never reuse BEARS camera coordinates, slot polygons, or current-scene confirmations.
- Run `puda machine commands opentrons` to understand available commands
- Follow all command types, params, sequencing rules, and labware constraints in `references/opentrons-machine.md`


## Selection Workflow

1. Parse user intent and identify the tasks.
2. Match intent to the machine capabilities above.
3. If machine selection is unclear or ambiguous, **ask the user** and wait for confirmation.
4. Load the corresponding reference file and CLI help.
5. Generate commands only after machine choice is confirmed.

## Output Guidance

When answering machine-selection questions:
- State the recommended machine and a one-line reason tied to capability.
- If uncertain, ask a direct clarification question instead of guessing.

## Critical sequencing rules
0. When execution depends on visible physical setup, run `puda-machine-vision-validation` with an **NTU-scoped** machine/camera profile; do not reuse calibration, geometry, credentials, or confirmations from BEARS/IMRE.
1. `bioshake` must not be shaking while any machine is operating on a Bioshake position.
2. `centrifuge` must not be spinning while any machine is operating on a Centrifuge position.
3. `opentrons` protocols must always end with no tip attached to any pipette.
4. `opentrons` deck slot (`location`) for every `load_labware` command must be explicitly confirmed by the user — **never assume a slot**.
5. `opentrons` `capture_image` must be its own standalone protocol — never combined with pipetting commands in the same protocol.
6. `balance` — always call `startup()` before reading and `shutdown()` after. Always tare before a dispense step. Always verify `fresh == True` before using a reading.

