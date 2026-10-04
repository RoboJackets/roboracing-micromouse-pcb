# KiCad project setup and library guide

This project keeps every custom KiCad asset inside the repository. The original per-component folders and library nicknames are retained for compatibility. Do not install these libraries globally and do not use absolute paths. KiCad resolves `${KIPRJMOD}` to the `pcb/` directory on every computer.

## Library layout

```text
pcb/
├── micromouse-pcb.kicad_pro
├── micromouse-pcb.kicad_sch
├── micromouse-pcb.kicad_pcb
├── sym-lib-table                 # project symbol-library registrations
├── fp-lib-table                  # project footprint-library registrations
└── libraries/
    ├── micromouse-symbols.kicad_sym
    ├── micromouse.pretty/        # original team footprint library
    ├── TEENSY_4.0/               # original Teensy symbol/footprints/model
    ├── SFH_4545/                 # original SFH symbol/footprint/model
    ├── LSM6DSV16XTR/             # original IMU assets
    ├── TEFT4300/                 # original receiver assets
    ├── DEV-16771/                # original Teensy 4.1 assets
    ├── symbols/                  # retained organized copies
    ├── footprints/               # retained organized copies
    └── 3dmodels/                 # retained organized model copies
```

KiCad's standard libraries are still used for standard parts. Only custom or modified assets belong in this repository.

## First-time setup after cloning

1. Install KiCad 10.0 or newer, including the official symbol, footprint, and 3D-model packages.
2. Clone the repository normally. Do not move only the `pcb/` directory out of the checkout.
3. Open `pcb/micromouse-pcb.kicad_pro` from KiCad's Project Manager. Do not open the schematic or board as a standalone file on the first launch.
4. If KiCad asks how to configure the global symbol or footprint libraries, choose the recommended/default official library table. This supplies standard libraries such as `Device`, `Resistor_SMD`, and `Connector_PinSocket_2.54mm`.
5. Confirm the project libraries:
   - In Schematic Editor, open **Preferences → Manage Symbol Libraries → Project Specific Libraries**. `micromouse-symbols`, `TEENSY_4.0`, `SFH_4545`, and the other part-number libraries should be listed.
   - In PCB Editor, open **Preferences → Manage Footprint Libraries → Project Specific Libraries**. The original `micromouse`, `TEENSY_4.0`, `SFH_4545`, and other part-number libraries should be listed. `Ratatouii` remains as a compatibility alias for already-saved organized-copy references.
6. Open the schematic and PCB. Run the Electrical Rules Checker and Design Rules Checker before making changes so any local installation problem is obvious.
7. Open PCB Editor's 3D Viewer. Official-library models come from `${KICAD10_3DMODEL_DIR}`; repository models come from `${KIPRJMOD}/libraries/3dmodels/Ratatouii.3dshapes`.

There is no manual import step for repository libraries. The checked-in `sym-lib-table` and `fp-lib-table` perform the registration.

## Add a new symbol

1. Open **Symbol Editor** from the project.
2. Select the `micromouse-symbols` library for a team-created symbol. Keep imported vendor symbols in their existing part-number library.
3. Use a stable part number or descriptive name, for example `DRV8833PW` or `Encoder_Header_1x06`.
4. Set its default footprint to `micromouse:<footprint-name>` for a team footprint, or to the matching part-number library such as `TEENSY_4.0:TEENSY_SHORT`. Use the official library nickname for an unchanged official footprint.
5. Fill in at least **Reference**, **Value**, **Datasheet**, and **Description**. Verify pin numbers against the datasheet.
6. Save the library. For a team-created symbol, the file that should change is `pcb/libraries/micromouse-symbols.kicad_sym`.
7. Place the symbol in the schematic, annotate it, run ERC, and commit the symbol file with the schematic change.

Do not rename or move an existing part-number library. New team-owned work belongs in `micromouse-symbols.kicad_sym`; a new vendor folder is acceptable when its files need to remain grouped together, provided it is added to the project library tables.

## Add a new footprint

1. Open **Footprint Editor** from the project.
2. Select the `micromouse` library for a team-created footprint, or the existing part-number library for a vendor-specific footprint.
3. Name it clearly and without a library prefix; a team footprint will display as `micromouse:<name>`.
4. Check pad numbers, hole sizes, courtyard, fabrication outline, pin-1 marking, solder-mask clearance, and component orientation against the datasheet.
5. Save a team footprint into `pcb/libraries/micromouse.pretty/`. One `.kicad_mod` file is created per footprint.
6. Assign `micromouse:<name>` to the symbol in **Tools → Assign Footprints**.
7. Update the PCB from the schematic, run DRC, and commit the `.kicad_mod`, schematic, and PCB changes together.

Never place `.kicad_mod` files directly in `libraries/` and never add a standalone footprint directory to a user's global table.

## Add or replace a 3D model

1. Prefer STEP (`.step`) for mechanical accuracy; use WRL only when color is needed and no STEP model is available.
2. Copy the model into `pcb/libraries/3dmodels/Ratatouii.3dshapes/`. Use the component or footprint name for the filename.
3. In **Footprint Editor → Footprint Properties → 3D Models**, attach it using this portable path:

   ```text
   ${KIPRJMOD}/libraries/3dmodels/Ratatouii.3dshapes/<model>.step
   ```

4. Adjust offset, rotation, and scale in the footprint—not by editing the model for one board instance.
5. Check the result in 3D Viewer from the top, bottom, and side.
6. Save the footprint, then use **Tools → Update Footprints from Library** in PCB Editor for existing board instances.
7. Commit the model and footprint together. The project model directory is explicitly exempted from the repository's generated-STEP ignore rule.

Do not use paths such as `C:\Users\...`, `/Users/...`, or a downloaded model's original location. They will fail on another contributor's computer.

## Review checklist

Before opening a pull request that changes library assets, verify:

- The project opens without a missing-library warning on a clean checkout.
- Every custom footprint reference uses a registered project nickname such as `micromouse:`, `TEENSY_4.0:`, `SFH_4545:`, `LSM6DSV16XTR:`, or `TEFT4300:`.
- Every repository 3D-model reference begins with `${KIPRJMOD}/libraries/3dmodels/Ratatouii.3dshapes/`.
- No absolute path appears in a KiCad file.
- Symbol pin numbers match footprint pad numbers and the datasheet.
- ERC and DRC have been run and any intentional violations are documented.
- The 3D model is aligned correctly.
- Symbol, footprint, model, schematic, and PCB changes that depend on one another are committed together.
