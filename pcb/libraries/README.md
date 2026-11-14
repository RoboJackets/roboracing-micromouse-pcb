# Project library ownership

The project uses a hybrid library layout so existing references remain valid.

## Maintained project libraries

- `symbols/Ratatouii.kicad_sym` contains team-maintained project symbols.
- `footprints/Ratatouii.pretty/` contains team-maintained project footprints.
- `3dmodels/Ratatouii.3dshapes/` contains team-maintained project 3D models.

New team-created or modified assets should normally go into those three locations and use the `Ratatouii` nickname.

## Imported vendor bundles

Folders named for a component, such as `CMT-4023S-SMT-TR/`, contain an imported vendor symbol, footprint, model, and source notes. Keep the bundle together and register its symbol and footprint paths in the project-level `pcb/sym-lib-table` and `pcb/fp-lib-table`.

Do not copy a vendor asset into `Ratatouii` unless the team intends to fork and maintain it. If it is forked, give it a distinct name so the vendor original and team version cannot be confused.

## Legacy compatibility files

The root-level and older component libraries are retained because existing schematic and board references may still use their nicknames. Do not rename, move, or delete them as part of routine cleanup. Migrate a reference only as an intentional schematic/PCB change that is reviewed and tested.

All model paths must be relative to `${KIPRJMOD}`. Never save an absolute path from a contributor's computer.
