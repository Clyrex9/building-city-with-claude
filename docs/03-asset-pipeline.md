# Asset pipeline: Blender → glTF → Godot

## The rule

**Nothing enters Godot except a `.glb` from `blender/exports/`.** No manual mesh
editing in Godot, no re-exporting from a random location.

## Blender-side checklist (run before every export)

1. Scene units: Metric, **Unit Scale 1.0**, length = Meters.
2. Object at **world origin**, origin point at **floor center** of the asset.
3. `Ctrl+A` → **All Transforms** applied (scale must read 1,1,1).
4. Modifiers applied or marked for export (Blender exports evaluated meshes).
5. Normals: `Shift+N` recalculate outside. No flipped faces.
6. UVs unwrapped; no overlapping islands unless intentional (trim sheet).
7. Materials named `mat_<surface>`; **one material slot per surface type**.
8. Naming per `CITY_PROJECT.md` §5. Object name = final asset name.
9. Delete unused: loose verts, unused materials, extra collections.

## Export settings (glTF 2.0 / .glb)

| Setting | Value |
|---|---|
| Format | glTF Binary (`.glb`) |
| Include | Selected Objects |
| Transform → +Y Up | **on** |
| Data → Mesh: Apply Modifiers | on |
| Data → Mesh: UVs, Normals, Tangents | on |
| Data → Material: Export | Export |
| Images | Automatic (embedded) |
| Compression (Draco) | **off** (keeps Godot import simple) |
| Animation | off unless the asset is animated |

Output path: `blender/exports/<name>.glb`

## Godot-side import

1. Copy/sync `.glb` into `godot/assets/models/<category>/`.
2. In the Import dock: **Import As → Scene**, "Save to file" a `.tscn` under
   `godot/scenes/<category>/` on first import only.
3. Collision: use Blender name suffixes so Godot generates shapes automatically —
   | Suffix | Result |
   |---|---|
   | `-col` | static trimesh collision, mesh still visible |
   | `-colonly` | collision only, mesh hidden |
   | `-convcolonly` | convex collision only |
   Use `-convcolonly` proxies for props; `-col` for buildings.
4. Lightmap UVs: enable "Generate Lightmap UV2" only for interior meshes.
5. Never edit the imported `.tscn` in place — inherit a scene from it and edit that.

## Collision proxy convention

Build a simplified box/hull in Blender named `<asset>_collision-convcolonly`,
parented to the asset. Cheaper and more predictable than trimesh, and it survives
re-export.

## Sanity test (do this once, before modeling anything real)

Export a 1.8 m tall cube named `chr_scale_ref`. Import it. Stand the player next to
it. The player's eyes should sit at 1.7 m — just under the top. If that reads wrong,
**stop and fix the pipeline** before modeling anything else.

## Re-export loop

Change in Blender → re-export to the **same filename** → Godot reimports on focus →
instanced scenes update automatically. If they don't, the scene was edited in place
(see step 5).

## Texture handling

- Author textures in Blender's material as image nodes so glTF embeds them.
- Trim sheets live in `godot/assets/textures/trims/` and are shared; for those,
  export the mesh with the material name only and assign a Godot `.tres` material
  in the inherited scene. Keeps `.glb` files small.
