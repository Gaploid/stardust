# Stardust

https://github.com/user-attachments/assets/f5e3a007-c20a-4873-9af9-357c8698b9d6

Up to **10 million WebGL particles** assemble into Earth, the Moon, Mars, Jupiter,
Saturn, the Sun, or a cube — and you can stir them with your cursor.

**Live: https://gaploid.github.io/stardust/**

## What it does

- **Shapes** — Earth (real coastlines, city lights, ETOPO1 sea floor), the Moon
  (real LROC albedo and mineral color), Mars (the Viking colour mosaic over MOLA
  relief, with Phobos and Deimos in orbit), Jupiter (Cassini's cylindrical map,
  read back in enhanced colour), Saturn (real body and ring textures, incl. the
  Cassini division), the Sun (procedural, modeled on SDO imagery: a boiling
  surface, filament channels, active regions, and prominences that erupt off
  the limb every minute or so), plus a cube. Switching scatters the particles
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

## Real physics

The particles here are an animation: they look like a planet, they do not pull
on each other. The simulation that does — two planets of real mass colliding
under their own gravity — is its own project now:
**[Cosmic Collisions](https://github.com/Gaploid/cosmic-collisions)**, live at
https://gaploid.github.io/cosmic-collisions/

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
| Mars surface | NASA/JPL/USGS Viking MDIM 2.1 colorized mosaic | 512×256, 4 bit/cell into a 16-colour palette | 64 KB |
| Mars relief | NASA/GSFC MGS MOLA MEGDR, 4 px/degree | 256×128, 8 bit/cell | 32 KB |
| Jupiter clouds | NASA/JPL/SSI Cassini cylindrical map (PIA07782) | 512×256, 4 bit/cell | 64 KB |
| Jupiter chroma | same map, red minus blue | 256×128, 4 bit/cell | 16 KB |
| Jupiter bands | same map, averaged along each latitude | 256 latitudes × RGB | 768 B |
| Saturn body | Cassini bands, recoloured to PIA06193 (natural colour) | 256 latitudes × RGB | 768 B |
| Saturn rings | NASA Cassini-derived texture | 128 radial samples × RGBA | 512 B |
| The sky | Yale Bright Star Catalogue, 5th ed. | 5080 stars × 6 B | 30 KB |

The Sun uses no map — it is generated procedurally. Only the slow structure
(filament channels, sunspots, active regions) is baked into the particles;
everything that moves — the convective churn, the boiling granulation, the
spicules on the limb, the eruptions — is computed in the vertex shader every
frame, so nothing on it stands still.

The starfield is the real one: every star down to magnitude 6, each carrying its
right ascension, declination, visual magnitude and B-V colour index in six bytes.
They sit on the celestial sphere and turn with the world, so dragging a shape
moves the sky behind it.

Saturn's strip keeps its Cassini band structure but wears a palette measured off
PIA06193, the natural-colour mosaic: sunlit disc rgb(222, 206, 158), half-lit
mid latitudes rgb(146, 138, 119), winter north rgb(82, 84, 92). Saturn is
butterscotch and grey, not the lemon the strip used to make of it.

Jupiter is split into three layers rather than one colour map: brightness
carries the belts, the ovals and the Great Red Spot and needs the resolution,
while chroma varies far more slowly and rides at a quarter of it. Full colour at
512×256 would have cost 128 KB of base64 instead of 81 KB.

Imagery courtesy of NASA Earth Observatory, NASA/GSFC/Arizona State University
(LROC), NASA/JPL-Caltech, NASA/JPL/USGS (Viking MDIM 2.1), NASA/GSFC (MOLA),
NASA/JPL/Space Science Institute, and NOAA NCEI. Star
positions from the Bright Star Catalogue, 5th Revised Ed. (Hoffleit & Warren),
via the Harvard/SAO catalogue archive. Public domain / free to use with credit.

## Tech

One self-contained page, no dependencies and no build step. `index.html` is
WebGL 1: one point-sprite pass, all motion in the vertex shader, exposure
normalized against particle count and size so those sliders change grain, not
brightness (10M points is 333 MB of VRAM, so it is opt-in).

## Changelog

Broad strokes, newest first; the commit history tells each one in full.

- **2026-08-26 — Impact moves out.** The second page left for
  [Cosmic Collisions](https://github.com/Gaploid/cosmic-collisions); what stays
  here is the animation.
- **2026-08-25 — A brighter sky.** The catalogue stars get a steeper magnitude
  curve and glows on the bright few, on by default.
- **2026-08-25 — Mars, with Phobos and Deimos.** The Viking colour mosaic over
  MOLA relief, read back in enhanced red. Two procedural moons in tilted
  orbits, tidally locked and rigid in the swell.
- **2026-08-24 — The Sun comes alive.** Nothing on it is baked any more: it
  boils, churns and erupts about once a minute, all in the shader.
- **2026-08-24 — A real sky.** The Yale Bright Star Catalogue, every star to
  magnitude 6, turning with the world.
- **2026-08-24 — Jupiter, and a butterscotch Saturn.** Jupiter from Cassini's
  cylindrical map replaces the torus; Saturn is recoloured off the
  natural-colour mosaic.
- **2026-08-24 — Ten million particles.** Capacity goes 2M → 10M with buffers
  sized to the slider, exposure normalized against count and size, and a
  loading screen that boots the world in slices. Menu icons, mobile controls,
  an advanced panel and a credits panel.
- **2026-08-24 — Saturn, and more of Earth.** Saturn with real body and ring
  textures; ETOPO1 sea floor, city lights, particle trails and a toggleable
  atmosphere on Earth.
- **2026-08-24 — The Sun.** Procedural, after SDO imagery: a boiling
  photosphere, sunspots, prominences.
- **2026-08-24 — The animation lands.** Earth, the Moon, a cube, a torus and a
  sphere; water, particles and fog; the cursor stirs the surface.
- **2026-08-23 — The repository.**

## License

MIT — see [LICENSE](LICENSE). The embedded NASA and NOAA maps are public domain;
credit is given above.

## Analytics

This site uses Google Analytics to understand basic usage.
