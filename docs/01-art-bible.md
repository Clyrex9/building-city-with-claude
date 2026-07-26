# Art bible

## Style target

**Stylized-realistic, low-mid poly, trim-sheet textured.** Think "clean modern city
block at golden hour" — readable silhouettes, restrained detail, colour doing the
heavy lifting. Not photoreal (too expensive for the performance budget), not
flat-shaded cartoon (harder to read as a real city).

Reference vibe: mid-2010s stylized city dioramas — strong shapes, soft ambient
occlusion, saturated accent props against desaturated architecture.

## Silhouette rules

- Every building gets **one distinguishing feature** at street level (awning,
  stairs, recessed entry, corner shop) — that's what the player sees.
- Rooflines vary: never two neighbouring buildings the same height.
- Detail density: **high 0–3 m** (eye level), medium 3–8 m, low above.

## Palette

| Role | Use | Hex |
|---|---|---|
| Architecture base 1 | concrete / plaster | `#C9C3B8` |
| Architecture base 2 | warm brick | `#9E6B4F` |
| Architecture base 3 | cool panel | `#7C858C` |
| Ground | asphalt | `#3A3B3D` |
| Ground | sidewalk | `#A8A49C` |
| Accent A | shop signage, awnings | `#D9552F` |
| Accent B | doors, trim, transit | `#2E7D9A` |
| Accent C | vegetation | `#5E7F4B` |
| Night emissive | windows / streetlight | `#FFC978` |

Rule: **max 2 accent colours visible per building.** Accents are ≤10 % of any frame.

## Materials

- Trim sheets first: one 2048² trim sheet covers walls, window frames, door frames,
  roof edges for the whole modular kit.
- Tiling textures for ground surfaces only (asphalt, sidewalk, brick).
- Unique UVs only for hero props (shop sign, vending machine).
- Roughness variation matters more than albedo detail — wet asphalt, dull plaster,
  glossy glass.

## Scale reference (memorize these)

| Thing | Size |
|---|---|
| Player eye height | 1.7 m |
| Door | 0.9 × 2.1 m |
| Floor-to-floor | 3.2 m |
| Ground-floor shopfront | 4.0 m tall |
| Sidewalk width | 3.0 m |
| Road lane | 3.5 m |
| Streetlight | 6.0 m |
| Bench | 1.6 × 0.6 × 0.85 m |
| Car | 4.4 × 1.8 × 1.5 m |

## Lighting intent

- **Day:** sun at ~35° elevation, warm key, cool sky bounce. Long shadows down the
  street axis — align the street grid so the sun rakes along it.
- **Night:** streetlights are the key, emissive windows are the fill, one cool
  moonlight rim.
- Fog: light exponential fog, ~0.008 density — sells depth between blocks cheaply.
