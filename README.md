# Stardust

Up to **10 million WebGL particles** assemble into the Sun, Earth, the Moon,
Saturn, a cube, or a torus — and you can stir them with your cursor.

**Live: https://gaploid.github.io/stardust/**

## What it does

- **Shapes** — Earth (real coastlines, city lights, ETOPO1 sea floor), the Moon
  (real LROC albedo and mineral color), the Sun (procedural, modeled on SDO
  imagery: filaments, fire seas, prominence loops), Saturn (real body and ring
  textures, incl. the Cassini division), plus cube and torus. Switching scatters
  the particles into a cloud and reassembles them.
- **Render modes** — *water* (liquid surface with swell and specular glints),
  *particles* (crisp film-grain points), *fog* (volumetric puffs).
- **Cursor** — a touch bubble parts the near surface, rings ripple outward, the
  trail sheds vortices and drags the medium along.
- **Advanced panel** — particle size, hand size, particle count (20k–10M), a
  depth-of-field focus slider, and toggles for sea floor, city lights,
  atmosphere, trails, and starfield.

Drag rotates · Space scatters · wheel zooms.

## Data sources

Every planetary surface is real data, downsampled and embedded directly in the
HTML as base64 — no network requests, no textures to load. Maps are
equirectangular and sampled per particle at generation time.

| Map | Source | Encoding | Size |
| --- | --- | --- | --- |
| Earth land mask | NASA Blue Marble (Nov 2004) | 512×256, 1 bit/cell | 16 KB |
| Earth city lights | NASA Earth at Night composite | 512×256, 4 bit/cell | 64 KB |
| Sea-floor height | NOAA NCEI ETOPO1 bathymetry | 512×256, 4 bit/cell | 64 KB |
| Lunar albedo | NASA/GSFC/ASU LROC mosaic | 512×256, 4 bit/cell | 64 KB |
| Lunar color warmth | NASA/GSFC/ASU LROC mosaic | 512×256, 4 bit/cell | 64 KB |
| Saturn body | NASA Cassini-derived texture | 256 latitudes × RGB | 768 B |
| Saturn rings | NASA Cassini-derived texture | 128 radial samples × RGBA | 512 B |

The Sun uses no map — it is generated procedurally.

Imagery courtesy of NASA Earth Observatory, NASA/GSFC/Arizona State University
(LROC), NASA/JPL-Caltech, and NOAA NCEI. Public domain / free to use with credit.

## Tech

A single self-contained `index.html` (~450 KB) — no dependencies, no build step.
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
