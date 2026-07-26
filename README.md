# Building a City with Claude

Building a small, **playable city from scratch** — every mesh modeled in Blender,
every scene assembled in Godot 4 — with [Claude Code](https://claude.com/claude-code)
driving both applications through MCP servers.

No asset store, no downloaded models, no AI-generated meshes. A modular kit is built
by hand, buildings are assembled from that kit, and the result is a walkable city
block you can download and play.

> **Status:** scaffolding. Specification and conventions are in place; the city
> itself is not built yet. Follow along in `docs/` and the commit history.

---

## How it works

```
        Claude Code
        ┌────┴────┐
   Blender MCP   Godot MCP
        │             │
    Blender 4.x    Godot 4.4+
        │             │
     .glb export ──►  scenes, scripts, play-testing
```

Claude reads the specification, models geometry in Blender through the
**Blender MCP** server, exports it as glTF, then wires it into scenes, physics and
gameplay in Godot through the **Godot MCP** server — checking its own work with
viewport screenshots and performance monitors.

## What's being built

A single city block that satisfies a fixed definition of done:

- A controllable character walking streets with real collision
- At least two enterable interiors
- A day/night lighting cycle
- Ambient audio and surface-dependent footsteps
- At least three interactable objects
- **60 FPS at 1080p on a mid-range GPU**

Full criteria: [CITY_PROJECT.md §2](CITY_PROJECT.md).

## Repository layout

| Path | Contents |
|---|---|
| [CITY_PROJECT.md](CITY_PROJECT.md) | The specification — goals, constraints, build order |
| [CLAUDE.md](CLAUDE.md) | Conventions Claude loads automatically each session |
| [docs/01-art-bible.md](docs/01-art-bible.md) | Style, palette, silhouette and real-world scale rules |
| [docs/02-tech-spec.md](docs/02-tech-spec.md) | Godot scene architecture, physics layers, input map, budgets |
| [docs/03-asset-pipeline.md](docs/03-asset-pipeline.md) | Blender → glTF → Godot, with export settings |
| [docs/04-mcp-setup.md](docs/04-mcp-setup.md) | Setting up both MCP servers, and how they fail |
| [blender/](blender/) | `.blend` sources, Python helpers, `.glb` exports |
| [godot/](godot/) | The Godot 4 project |

## Design decisions worth stealing

Most of the value here isn't the city — it's the constraints that keep an AI agent
producing consistent geometry across many sessions:

- **One unit = one meter, everywhere.** Blender scene scale 1.0, glTF exported Y-up.
  Verified once with a 1.8 m reference cube, then never questioned again.
- **A 4 m grid module** and a fixed 3.2 m floor height, so anything modeled in any
  session snaps against anything else.
- **Origin at floor center** on every asset — placement becomes arithmetic.
- **Naming with class prefixes** (`bld_`, `prp_`, `mod_`, …) so the agent can find
  and reason about assets without reading every file.
- **Explicit tri budgets per asset class**, checked at export, instead of a
  performance panic at the end.
- **A written icebox.** Out-of-scope ideas get recorded, not built. This is the
  single most effective guard against agent scope creep.

## Running it yourself

**Requirements:** Blender 4.x with the BlenderMCP addon · Godot 4.4+ with the Godot
MCP plugin · Claude Code with both servers configured.

```bash
git clone <this repo>
cd building-city-with-claude
```

Open `godot/project.godot` in Godot, open Blender, then follow
[docs/04-mcp-setup.md](docs/04-mcp-setup.md) to connect both MCP servers. From there,
start a Claude Code session in the repository root — `CLAUDE.md` supplies the
conventions automatically.

## License

MIT for code and documentation. See [LICENSE](LICENSE).
