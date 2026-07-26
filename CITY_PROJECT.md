# Project specification — a playable city

> Source of truth for the project. Everything in `docs/` expands on a section here.
> All prompts and code comments are written in English.

---

## 1. Goal

Build a small, walkable, playable city **from zero** — modeled in Blender through
**Blender MCP**, assembled and made playable in Godot 4 through **Godot MCP** — with
Claude driving both tools. No purchased or downloaded assets: everything in the city
is modeled as part of the project.

## 2. Definition of done

The project is complete when a player can open one build and:

1. Spawn as a controllable character in a city block.
2. Walk the streets with working collision, sidewalks and street props.
3. Enter at least **2 interior spaces** (a shop and an apartment lobby).
4. See lighting change between day and night.
5. Hear ambient city audio and surface-dependent footsteps.
6. Interact with at least 3 objects (door, elevator or sign, vending machine).
7. Run at **60 FPS on a mid-range GPU** at 1080p.

## 3. Hard constraints

| Constraint | Value | Why |
|---|---|---|
| Engine | Godot **4.4+**, Forward+ renderer | MCP tooling targets 4.x |
| Units | 1 Godot unit = **1 meter**, Blender scene scale 1.0 | avoids import scale problems |
| Up axis | Blender Z-up → export as **Y-up glTF** | Godot is Y-up |
| Interchange format | **.glb** (binary glTF), one file per asset | single file, textures embedded |
| Origin | Every asset origin at its **floor center** | grid snapping works |
| Grid | City grid module = **4 m**; footprints are multiples of 4 | modular kit reuse |
| Building floor height | **3.2 m** | consistent stacking |
| Street width | **12 m** road + 3 m sidewalk each side | traffic + camera room |
| Tris budget | prop < 2k, building < 25k, whole block < 400k | 60 FPS target |
| Textures | 1024² props, 2048² buildings, trim sheets preferred | VRAM |
| Naming | `snake_case`, prefixed by class (§5) | scriptable, greppable |

## 4. Repository layout

```
.
├─ CITY_PROJECT.md          ← this file
├─ CLAUDE.md                ← conventions Claude loads each session
├─ README.md                ← overview and quickstart
├─ docs/
│  ├─ 01-art-bible.md       ← style, palette, silhouette and scale rules
│  ├─ 02-tech-spec.md       ← Godot scene architecture, layers, autoloads
│  ├─ 03-asset-pipeline.md  ← Blender → glTF → Godot, step by step
│  └─ 04-mcp-setup.md       ← Blender MCP + Godot MCP setup and troubleshooting
├─ blender/
│  ├─ src/                  ← .blend working files (one per asset family)
│  ├─ scripts/              ← reusable Python for Blender MCP
│  └─ exports/              ← .glb output, synced into godot/assets/models
└─ godot/
   ├─ project.godot
   ├─ scenes/               ← world, player, buildings, props, ui
   ├─ scripts/              ← GDScript
   ├─ assets/               ← models, textures, audio
   └─ resources/            ← .tres materials, MeshLibrary
```

## 5. Naming convention

| Kind | Pattern | Example |
|---|---|---|
| Blender source | `<family>.blend` | `buildings_residential.blend` |
| Exported mesh | `<class>_<name>_<variant>.glb` | `bld_apartment_a.glb` |
| Godot scene | `<class>_<name>.tscn` | `bld_apartment_a.tscn` |
| Script | `<node_name>.gd` | `player_controller.gd` |
| Material | `mat_<surface>.tres` | `mat_asphalt_wet.tres` |

Class prefixes: `bld_` building · `prp_` prop · `veh_` vehicle · `chr_` character
· `env_` environment/terrain · `mod_` modular kit piece · `ui_` interface.

## 6. Build order, and why

1. **Grey-box first.** Playable movement before a single finished mesh. There is
   always something walkable to test against.
2. **Modular kit before hero assets.** Walls, floors, windows, doors first —
   buildings are then assembled from them, in Blender or with a Godot GridMap.
3. **Streets and props** once the buildings define the space.
4. **Interiors and interaction** — only two interiors are required (§2.3).
5. **Lighting near the end.** It makes everything look finished, and it's cheap to
   iterate once the geometry is settled.
6. **Audio, performance pass, export build.**

## 7. Risk register

| Risk | Mitigation |
|---|---|
| An MCP server disconnects mid-session | reconnect steps in `docs/04-mcp-setup.md` |
| Blender hangs on a heavy operation | commit before heavy ops; save often |
| Import scale wrong | verified once with a 1.8 m reference cube, then never changed |
| Scope creep (traffic AI, NPCs, weather) | anything outside §2 goes to the icebox, §8 |
| No history to roll back to | commit at the end of every work session |

## 8. Icebox — explicitly out of scope

Simple traffic on splines · NPC pedestrians on a navmesh · weather and rain shaders ·
interior light probes · minimap · save/load of player position.

These are not part of the definition of done. They get added here rather than built.
