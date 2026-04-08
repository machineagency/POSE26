# Science Jubilee — Claude Code Knowledge Base

You are assisting a scientist who controls a Jubilee multi-tool motion platform for lab automation. The `science_jubilee` Python library provides the API — **always read its source code** for method signatures, parameters, and tool capabilities rather than relying solely on this document.

This document covers things that are NOT obvious from the source: safety rules, physical conventions, unit traps, calibration prerequisites, and common gotchas.

**Before generating any lab code:**
1. Check if an existing notebook in `examples/` already covers the task — if so, direct the user there instead of writing new code
2. Read `lab_config/deck_config.yaml` and `lab_config/tool_config.yaml` to understand the specific machine's tool indices, IP address, and calibration

---

## Where to Find Things

| What | Where |
|------|-------|
| Machine API | `science_jubilee/Machine.py` |
| Tool implementations | `science_jubilee/tools/` (Syringe.py, Pipette.py, Camera.py, AS7341.py, etc.) |
| Tool configs (defaults) | `science_jubilee/tools/configs/examples/*.json` |
| Tool configs (per-machine) | `science_jubilee/tools/configs/user/*.json` — **checked first**, overrides examples |
| Deck definitions | `science_jubilee/decks/deck_definition/*.json` |
| Labware definitions | `science_jubilee/labware/labware_definition/*.json` |
| This machine's setup | `lab_config/deck_config.yaml` and `lab_config/tool_config.yaml` |
| Example notebooks | `examples/` directory, organized by tool/topic (see index below) |

---

## Example Notebook Index

When a user asks how to do something, **check this list first** and point them to the relevant notebook before writing new code.

| Task | Notebook |
|------|----------|
| Connect and move the machine | `examples/start-here/MachineJogging.ipynb` |
| Understand the deck and slots | `examples/labware/0_AutomationDeckIntro.ipynb` |
| Load and access labware/wells | `examples/labware/1_LabwareIntro.ipynb` |
| Calibrate Z offset (syringe) | `examples/syringe/0_SetZOffset.ipynb` |
| Syringe basics | `examples/syringe/1_SyringeIntro.ipynb` |
| Serial dilution (syringe) | `examples/syringe/2_SerialDilution.ipynb` |
| Syringe extruder / gel dispensing | `examples/syringe/3_SyringeExtruderIntro.ipynb` |
| Gel printing | `examples/syringe/4_GelPrinting.ipynb` |
| Pipette basics | `examples/pipette/0_PipetteIntro.ipynb` |
| Serial dilution (pipette) | `examples/pipette/1_SerialDilution.ipynb` |
| Take pictures with camera | `examples/camera/TakingPictures.ipynb` |
| Spectral sensor setup | `examples/spectral-sensor/SensorSetup.ipynb` |
| Calibrate deck slots | `examples/calibration/LabAutomationDeckCalibration.ipynb` |
| Calibrate tool XY alignment | `examples/calibration/ToolAlignmentXY.ipynb` |
| Set tool parking positions | `examples/calibration/SetToolParkingPositions.ipynb` |
| Full multi-tool demo | `DemoOfAllTools.ipynb` |

---

## Critical Safety Rules

### Collision prevention
1. **ALWAYS** call `m.home_all()` before any movement commands
2. **ALWAYS** call `m.park_tool()` before picking up a different tool
3. **ALWAYS** raise Z for clearance before lateral moves — `m.move_to(z=150)` or let `safe_z_movement()` handle it
4. **Never** move to Z < `deck.safe_z` without extreme caution
5. When manually moving with `move_to` near labware, use small Z increments (0.5 mm) — don't overshoot
6. The lab automation deck sits ABOVE the aluminum bed plate. Machine Z=0 is at the bed plate, not the deck surface. Account for this height difference.

### Z-axis convention
**Z positive = DOWN.** This is the most dangerous thing to get wrong.
- Increasing Z → bed moves down → MORE clearance (safer)
- Decreasing Z → bed moves up → tool gets CLOSER to labware (collision risk)
- `m.move_to(z=0)` = bed at top — **dangerous** if labware is present

### Tool-specific safety
- **Syringe plunger must be at 0 mL before powering on** (no limit switches — position is assumed)
- **Z-offset calibration resets on machine restart** — must redo before liquid handling each session
- **`pipette.drop_tip()` before parking** to avoid contamination
- **`cam.release()` when done with Camera** to free hardware
- **`spec.disconnect()` when done with AS7341** to free serial port
- Verify tool indices match your physical setup: `m.configured_tools`

### Physical setup
- **Digital setup must match physical installation** — tool will crash into labware if slots don't match
- **Well A1 must face the tool parking rail** when installing labware
- **Remove lids** from labware before running
- **Gel printing with glass plate**: remove plate during homing, reinstall after homing completes

---

## Unit Trap (the #1 source of bugs)

| Tool | Volume unit | Example |
|------|-------------|---------|
| **Syringe** | **milliliters (mL)** | `syringe.transfer(vol=1.5, ...)` |
| **Pipette** | **microliters (uL)** | `pipette.aspirate(200, ...)` |

These are different by a factor of 1000. Double-check every time.

---

## Non-Obvious Conventions

### Well positioning
- `well.bottom(3)` = 3mm ABOVE the bottom (positive = up from bottom). Use for aspirate.
- `well.top(-1)` = 1mm BELOW the top (negative = down into well). Use for dispense.
- Column indexing starts at **1** (not 0): `plate.column_data[1]`, `plate.wells['A1']`

### Tool lifecycle (strict order)
```
instantiate tool → m.load_tool(tool) → m.pickup_tool(tool) → use → m.park_tool()
```
- Pipette additionally requires: `add_tiprack()` and setting `.trash` BEFORE pickup
- Pipette workflow: `pickup_tip()` → aspirate/dispense → `drop_tip()` (or `return_tip()`)

### Speed
- `move_to` and `move` take speed as `s=` in **mm/min** (not mm/sec)
- Helper: `mm_per_sec * 60 = mm_per_min`

### Serial dilution math
- Dilution ratio = (transfer_volume + destination_volume) / transfer_volume
- **2x dilution**: transfer equal volume into equal volume of diluent
- **10x dilution**: transfer 1 part into 9 parts diluent
- Pre-fill destination wells with diluent BEFORE transferring sample
- Total volume per well = pre-fill + transfer — must not exceed well capacity

### Calibration prerequisites
- **Before any liquid handling**: Z-offset calibration (see `examples/syringe/0_SetZOffset.ipynb`). Resets each restart.
- **Before pipette use**: deck calibration (`examples/calibration/LabAutomationDeckCalibration.ipynb`)
- **Before gel printing**: Z-offset is extremely sensitive — calibrate to glass plate height, not aluminum bed

---

## GCode Quick Reference (Duet3D)

| Command | Description |
|---------|-------------|
| `G28` | Home all axes (= `m.home_all()`) |
| `G0`/`G1 X_ Y_ Z_ F_` | Linear move (F = feedrate mm/min) |
| `T<n>` / `T-1` | Select tool n / deselect all (= `pickup_tool` / `park_tool`) |
| `M114` | Report position (= `m.get_position()`) |
| `M400` | Wait for moves to complete |
| `M409 K"key"` | Query object model |
| `G10 P<tool> Z<offset>` | Set tool Z offset |
| `G4 P<ms>` / `G4 S<sec>` | Dwell/pause |
