# Godot technical spec

## Scene architecture

```
main.tscn                       (Node3D — the game root, set as main scene)
├─ World                        (Node3D)
│  ├─ Environment               (WorldEnvironment)
│  ├─ Sun                       (DirectionalLight3D)
│  ├─ Terrain                   (StaticBody3D — ground plane + roads)
│  ├─ Buildings                 (Node3D — bld_*.tscn instances)
│  ├─ Props                     (Node3D — prp_*.tscn instances)
│  └─ Navigation                (NavigationRegion3D — reserved, EP+ )
├─ Player                       (CharacterBody3D — player.tscn)
│  ├─ Collision                 (CollisionShape3D — capsule 0.4r × 1.8h)
│  ├─ Head                      (Node3D @ y=1.7)
│  │  ├─ Camera                 (Camera3D)
│  │  └─ InteractRay            (RayCast3D — 2.5 m, mask = interactable)
│  └─ Footsteps                 (AudioStreamPlayer3D)
└─ UI                           (CanvasLayer — ui_hud.tscn)
   ├─ Crosshair
   ├─ InteractPrompt            (Label)
   └─ DebugStats                (Label — FPS/tris, toggled with F3)
```

Rule: `main.tscn` never contains geometry directly. Everything is an instanced scene.

## Autoloads

| Name | Script | Responsibility |
|---|---|---|
| `GameState` | `scripts/game_state.gd` | time of day, paused, player ref |
| `Interaction` | `scripts/interaction.gd` | registry of interactables, prompt text |
| `AudioDirector` | `scripts/audio_director.gd` | ambience layers, surface→footstep map |

Keep autoloads to three. Anything else is a node.

## Physics layers

| Bit | Name | Used by |
|---|---|---|
| 1 | `world` | terrain, roads, static geometry |
| 2 | `building` | building collision |
| 3 | `prop` | street props |
| 4 | `player` | player body |
| 5 | `interactable` | doors, machines, signs |
| 6 | `trigger` | area volumes (interior fade, zones) |
| 7 | `vehicle` | reserved |
| 8 | `npc` | reserved |

Player collides with 1,2,3,7. InteractRay masks **5** only.

## Input map

| Action | Keys |
|---|---|
| `move_forward` / `move_back` / `move_left` / `move_right` | W / S / A / D |
| `jump` | Space |
| `sprint` | Shift |
| `interact` | E |
| `toggle_debug` | F3 |
| `toggle_daynight` | T |
| `pause` | Esc |

Mouse look: relative motion, sensitivity 0.0022, pitch clamped ±89°.

## Player movement values

| Param | Value |
|---|---|
| Walk speed | 4.0 m/s |
| Sprint speed | 7.0 m/s |
| Acceleration / friction | 12.0 / 14.0 |
| Jump velocity | 4.8 m/s |
| Gravity | project default (9.8) |
| Capsule | radius 0.4, height 1.8 |
| Step height | 0.3 (floor_max_angle 46°) |

## Rendering settings

- Renderer: **Forward+**
- Shadows: directional 4096, 4 splits, soft shadows on
- GI: SDFGI on for day; check cost in EP06 before committing
- MSAA 3D: 2× · SSAO: on, low · Glow: on for night emissives
- `physics/common/physics_ticks_per_second`: 60

## Performance guardrails

- Draw calls < 1500 · tris on screen < 800k · 60 FPS at 1080p.
- Use `MultiMeshInstance3D` for repeated props (>20 instances) — streetlights, bins.
- Enable occlusion culling once real buildings replace the grey-box volumes.
- Check with `get_performance_monitors` at the end of every work session and record
  the numbers, so regressions are attributable to a specific change.

## Coding conventions (GDScript)

- `class_name` on anything instanced more than once.
- Typed everywhere: `var speed: float = 4.0`.
- Signals over polling; `@onready` for node refs, never `get_node()` in `_process`.
- One responsibility per script; if a script passes ~150 lines, split it.
