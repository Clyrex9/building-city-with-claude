# CLAUDE.md — conventions for this project

Read `CITY_PROJECT.md` first; it is the source of truth. This file is the short
version of the rules to follow without being reminded.

## Non-negotiable technical rules

- Godot **4.4+**, Forward+. 1 unit = 1 meter.
- Blender scene scale **1.0**, apply all transforms before export.
- Export **.glb** only, **+Y up**, `-Z forward`, into `blender/exports/`.
- Asset origin at **floor center**. Building footprints are multiples of **4 m**.
  Floor height **3.2 m**.
- Naming: `snake_case` with class prefix (`bld_ prp_ veh_ chr_ env_ mod_ ui_`).
- Tris budget: prop < 2k, building < 25k, block < 400k.

## Working rules

- **Grey-box before art.** Never model a finished asset before the space is playable.
- **One asset family per `.blend` file.** Do not accumulate a mega-scene.
- Before creating anything in Godot, check the scene tree; do not duplicate nodes.
- After any structural change, `save_scene` — the editor state is not the truth,
  the `.tscn` on disk is.
- Physics layer names live in `docs/02-tech-spec.md`. Never invent a new layer
  without adding it there.
- Prefer small, verifiable steps: build the smallest version that works, run it,
  screenshot it, then extend.

## MCP usage

- **Blender MCP** — modeling, materials, UVs, export. Prefer `execute_blender_code`
  with a readable, commented Python block over many small tool calls; it is easier
  to review and to re-run.
- **Godot MCP** — scenes, nodes, scripts, play-testing. Prefer the typed tools
  (`add_node`, `setup_physics_body`, …) over raw editor scripts, so failures are
  legible.
- If a server is not connected, **stop and say so** — do not fabricate a fallback.

## Things to never do here

- Do not add assets from Polyhaven / Sketchfab / AI generators. The premise of the
  project is building everything ourselves.
- Do not refactor the folder layout.
- Do not build anything from the icebox (`CITY_PROJECT.md` §8) unprompted.

## Local-only notes

`production/` is untracked and holds working notes. Read it if it exists, but never
reference its contents in tracked files.
