# Stardust

https://github.com/user-attachments/assets/f5e3a007-c20a-4873-9af9-357c8698b9d6

Up to **10 million WebGL particles** assemble into Earth, the Moon, Jupiter,
Saturn, the Sun, or a cube — and you can stir them with your cursor.

**Live: https://gaploid.github.io/stardust/**

## What it does

- **Shapes** — Earth (real coastlines, city lights, ETOPO1 sea floor), the Moon
  (real LROC albedo and mineral color), Jupiter (Cassini's cylindrical map, read
  back in enhanced colour), Saturn (real body and ring textures, incl. the
  Cassini division), the Sun (procedural, modeled on SDO imagery: filaments,
  fire seas, prominence loops), plus a cube. Switching scatters the particles
  into a cloud and reassembles them.
- **Render modes** — *water* (liquid surface with swell and specular glints),
  *particles* (crisp film-grain points), *fog* (volumetric puffs).
- **Cursor** — a touch bubble parts the near surface, rings ripple outward, the
  trail sheds vortices and drags the medium along.
- **Advanced panel** — hand size, particle count (20k–10M), opacity, blur, and
  toggles for sea floor, city lights, atmosphere, trails, and starfield. *Blur*
  is the point size: at a million points, growing them reads as softening, so
  the control is named for what it does.

Drag rotates · Space scatters · wheel zooms.

## Data sources

Every planetary surface is real data, downsampled and embedded directly in the
HTML as base64 — no network requests, no textures to load. Maps are
equirectangular and sampled per particle at generation time.

The same list is in the page itself, behind **credits** in the top-right corner.

| Map | Source | Encoding | Size |
| --- | --- | --- | --- |
| Earth land mask | NASA Blue Marble (Nov 2004) | 512×256, 1 bit/cell | 16 KB |
| Earth city lights | NASA Earth at Night composite | 512×256, 4 bit/cell | 64 KB |
| Sea-floor height | NOAA NCEI ETOPO1 bathymetry | 512×256, 4 bit/cell | 64 KB |
| Lunar albedo | NASA/GSFC/ASU LROC mosaic | 512×256, 4 bit/cell | 64 KB |
| Lunar color warmth | NASA/GSFC/ASU LROC mosaic | 512×256, 4 bit/cell | 64 KB |
| Jupiter clouds | NASA/JPL/SSI Cassini cylindrical map (PIA07782) | 512×256, 4 bit/cell | 64 KB |
| Jupiter chroma | same map, red minus blue | 256×128, 4 bit/cell | 16 KB |
| Jupiter bands | same map, averaged along each latitude | 256 latitudes × RGB | 768 B |
| Saturn body | Cassini bands, recoloured to PIA06193 (natural colour) | 256 latitudes × RGB | 768 B |
| Saturn rings | NASA Cassini-derived texture | 128 radial samples × RGBA | 512 B |

The Sun uses no map — it is generated procedurally.

Saturn's strip keeps its Cassini band structure but wears a palette measured off
PIA06193, the natural-colour mosaic: sunlit disc rgb(222, 206, 158), half-lit
mid latitudes rgb(146, 138, 119), winter north rgb(82, 84, 92). Saturn is
butterscotch and grey, not the lemon the strip used to make of it.

Jupiter is split into three layers rather than one colour map: brightness
carries the belts, the ovals and the Great Red Spot and needs the resolution,
while chroma varies far more slowly and rides at a quarter of it. Full colour at
512×256 would have cost 128 KB of base64 instead of 81 KB.

Imagery courtesy of NASA Earth Observatory, NASA/GSFC/Arizona State University
(LROC), NASA/JPL-Caltech, NASA/JPL/Space Science Institute, and NOAA NCEI.
Public domain / free to use with credit.

## Tech

A single self-contained `index.html` (~570 KB) — no dependencies, no build step.
One WebGL 1 point-sprite pass; all motion (assembly, waves, vortices, depth of
field) is computed in the vertex shader.

Exposure is normalized against both particle count and particle size. Additive
blending makes on-screen brightness a product of coverage and density, so either
slider would otherwise double as an exposure control and burn out the limb —
where the sphere is edge-on and points pile up. They change grain instead.

Vertex buffers are sized to the particle count the slider asks for and grow only
when it is raised — 10M particles is 333 MB of VRAM, so it is opt-in. The CPU
side stages one 25k chunk at a time, which keeps generation off the main thread
in slices and costs ~2 MB of RAM whatever the count.

## License

MIT — see [LICENSE](LICENSE). The embedded NASA and NOAA maps are public domain;
credit is given above.

## Analytics

This site uses Google Analytics to understand basic usage.
