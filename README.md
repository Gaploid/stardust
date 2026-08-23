# Stardust

Up to **2 million WebGL particles** assemble into Earth, the Moon, a cube, a torus,
or a sphere — and you can stir them with your cursor.

**Live: https://gaploid.github.io/stardust/**

## What it does

- **Shapes** — Earth (real continents and, optionally, the ETOPO1 sea floor with
  mid-ocean ridges and trenches), the Moon (real LROC albedo: maria, Tycho rays,
  subtle mineral color), plus cube, torus, and sphere. Switching shapes scatters
  the particles into a cloud and reassembles them.
- **Render modes** — *water* (soft liquid surface with swell, specular glints, and
  ripples), *particles* (crisp film-grain points), *fog* (volumetric puffs that
  swirl around your hand).
- **Cursor interaction** — a touch bubble parts the near surface, expanding rings
  ripple outward, and the trail sheds little vortices; the medium is dragged along
  with your motion.
- **Advanced panel** — particle size, hand size, particle count (20k–2M), a focus
  slider that slides the depth-of-field plane through the object, and a sea-floor
  toggle for Earth.

Click (or Space) scatters the shape; drag rotates; wheel zooms.

## Tech

A single self-contained `index.html` — no dependencies, no build step. Everything
runs in one WebGL 1 point-sprite pass with all motion (assembly, waves, vortices,
depth of field) computed in the vertex shader. The planetary data is embedded as
compact base64 bitmasks sampled at particle-generation time:

| Map | Source | Encoding |
| --- | --- | --- |
| Earth land mask | NASA Blue Marble (Nov 2004) | 512×256, 1 bit/cell |
| Lunar albedo | NASA LROC mosaic | 512×256, 4 bit/cell |
| Lunar color warmth | NASA LROC mosaic | 512×256, 4 bit/cell |
| Sea-floor height | NOAA ETOPO1 | 512×256, 4 bit/cell |

Imagery courtesy of NASA Earth Observatory, NASA/GSFC/Arizona State University
(LROC), and NOAA NCEI (ETOPO1).
