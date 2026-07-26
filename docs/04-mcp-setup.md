# MCP setup

This project drives two MCP servers from Claude Code. Run this checklist at the start
of every work session.

## Requirements

| Tool | Version | Extra |
|---|---|---|
| Blender | 4.x | BlenderMCP addon |
| Godot | 4.4+ | Godot MCP plugin, enabled in the project |
| Claude Code | latest | both servers registered in your MCP config |

## Blender MCP

1. Launch Blender with the **BlenderMCP** addon enabled
   (`Edit → Preferences → Add-ons`).
2. Open the N-panel in the 3D viewport → **BlenderMCP** tab → **Connect to Claude**.
3. Verify: ask Claude to report the scene contents. You should get object names back.
4. Optional integrations (Polyhaven / Sketchfab / Hyper3D) — **leave them off**.
   Everything in this project is modeled from scratch.

**If it disconnects:** click Disconnect → Connect in the N-panel. If that fails,
restart Blender. The `.blend` on disk is the truth, so save often.

## Godot MCP

1. Open the Godot 4.4+ editor with `godot/project.godot`.
2. Enable the MCP plugin in `Project → Project Settings → Plugins`.
3. Verify: ask Claude for project info and the scene tree. You should get both.
4. Keep the editor on the **3D viewport** tab — editor screenshots capture whatever
   is currently visible.

**If it disconnects:** disable and re-enable the plugin, or `reload_project`. Save
scenes before doing either.

## Session verification

One prompt that checks both:

```
Report the Blender scene contents and the Godot project info and scene tree.
```

Both must answer before you start working. A dead MCP connection discovered halfway
through a modeling session is the most expensive failure mode in this setup.

## Known failure modes

| Symptom | Cause | Fix |
|---|---|---|
| Blender tool call hangs | a modal operator is open in Blender | press Esc in Blender, retry |
| Godot node added but not visible | scene not saved, or wrong parent | `save_scene`, re-check the tree |
| Screenshot shows the wrong view | editor is on the 2D or Script tab | switch to the 3D viewport |
| Import didn't refresh | Godot missed the focus event | click into the editor window once |
| Long Python block fails quietly | exception swallowed by the addon | re-run in smaller chunks with prints |

## Save discipline

- Blender: `Ctrl+S` after every completed asset.
- Godot: `save_scene` after every structural change.
- Git: commit at the end of every work session.
