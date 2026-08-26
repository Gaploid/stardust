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

## Impact

**Live: https://gaploid.github.io/stardust/collision.html**

A second page, and a different kind of thing: not a shape to assemble but a
simulation. Two planets of real mass — a proto-Earth and an impactor from 0.02
to 1 M⊕, differentiated iron core to crust — fall together under their own
gravity and collide: 16k to 262k particles, each pulling on all the others,
touching ones pushing back like a stiff spring and bleeding the energy into
heat that stays in the rock and lights it, deep red past 900 K, white past
40 000. Five presets, from the canonical Moon-forming impact to a hit-and-run;
the readout follows the largest body and its disk, says what Moon that disk
would make, and keeps the books. Not SPH — no shocks, no vaporization — but the
deformation, the tidal arm, the disk and the re-accretion are all there.

Drag rotates · wheel zooms · Space pauses · R restarts · F follows the largest body.

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

Two self-contained pages, no dependencies and no build step. `index.html` is
WebGL 1: one point-sprite pass, all motion in the vertex shader, exposure
normalized against particle count and size so those sliders change grain, not
brightness (10M points is 333 MB of VRAM, so it is opt-in).

`collision.html` is WebGL 2 with no compute shaders to lean on, so the hard
parts are tricks: contacts from a hashed grid filled by depth-peeling, gravity
from a 64³ particle mesh with the loose material corrected pairwise against it,
P³M-style, so moonlets bind instead of smearing. Its bodies are onions of
Fibonacci-spiral shells — a lattice cut to a sphere is terraced, and the skin
drew every step as a ring — relaxed and then crept into equilibrium so that
nothing pops out of the surface. Symplectic Euler, Morton order, and books on momentum and energy that caught most of what was wrong. The
picture is screen-space fluid rendering — impostors, a bilateral blur that melts
them into a skin, a coverage cut that takes the beads off the limb — with hot
rock glowing by its temperature and lighting everything else. ACES and FXAA.

## Changelog

Broad strokes, newest first; the commit history tells each one in full.

- **2026-08-26 — A shorter panel.** The advanced panel lost its two spin
  sliders — a spin is a property of the rock, like its density, and both bodies
  keep the eight-hour day they start with — and says the rest in fewer words:
  the units moved into the labels, and a rule marks where the settings that
  restart the run end.
- **2026-08-26 — Skin.** The bodies are built as onions of Fibonacci shells
  instead of a lattice cut to a sphere, so the concentric rings the crust wore
  in every frame are gone; the pile is relaxed before it settles and settles
  at a creep, so nothing pops out of the surface; the grains are 4 % smaller,
  since a random pile jams looser than a lattice, which keeps the radius and
  the books; and the skin's silhouette is cut and feathered by a coverage
  field, which takes the staircase of lit discs off the limb.
- **2026-08-26 — The film.** An ACES-style tone curve — deeper shadows, a
  clean roll-off at the hottest — the sun as a disk with a glare that the
  bodies occlude, a light vignette, and FXAA. The knobs live in
  `__impact.look`.
- **2026-08-26 — Lit by the magma.** The planet and the second body light
  what is near them with the glow of their own surface: the arm and the
  disk get their planet-facing sides in orange, a moonlet is lit from
  below, the planet's cold far side stays dark, and the bodies shadow each
  other as balls — the first cut lit the crust spalled off the impactor's
  far side through the impactor, a glint along its rim.
- **2026-08-26 — Heat.** The heat the contacts make stays in the material
  and spreads by contact; every particle has a temperature and the rock glows
  by it — the planet a magma ocean at 3000 K a day after the canonical
  impact, the far side cold, the sparks off the contact white — and the
  readout says the mean. Bloom on what is brighter than white. On the way:
  the books were booking a barely-loaded contact's spring work as heat with
  either sign; the balance goes from −2.8 % to under a percent.
- **2026-08-26 — Faster.** A step at 131k from 2.5 ms to 1.4, and 4× runs at
  30 fps instead of 19: empty mesh blocks skipped, the mesh every eighth
  step, the particles kept in spatial order, the analysis read back without
  waiting, the spring let go after the splash. And the mesh deposit was
  losing 2 % of the mass to half-float rounding; it blends in full floats.
- **2026-08-26 — Dust, gone.** The points that rode on the particles never
  read as anything but noise; the skin is the body.
- **2026-08-26 — The disk binds.** Moonlets came apart into puffs: a cell
  of the gravity mesh is five particles wide and a moonlet is a cell or two.
  The loose material now gets its gravity corrected pairwise, P³M-style, and
  the disk collects into moonlets that stay; a moon line says what Moon the
  disk would make.
- **2026-08-26 — The books.** The readout keeps momentum, angular momentum
  and energy, and they caught the gravity mesh braking a spinning planet by
  lagging four steps behind it; the mesh now pulls as one impulse where it
  is measured, and cell on cell at centres of mass, and L holds to 0.01 %.
- **2026-08-26 — Spin.** Both bodies arrive turning about their axes, with
  eight-hour days by default and sliders from none to a 2.6-hour Earth, each
  cut beforehand as the Maclaurin spheroid its spin calls for. Dust is off by
  default.
- **2026-08-25 — Cores.** Both bodies get an iron core, a mantle and a crust,
  with density as mass; the readout says what each piece is made of and how
  long the planet's day is.
- **2026-08-25 — A brighter sky.** The catalogue stars get a steeper magnitude
  curve and glows on the bright few, on by default; the impact page gets the
  same real sky instead of a hash.
- **2026-08-25 — Impact.** A second page: two planets of real mass collide
  under their own gravity, on the GPU, from the canonical Moon-forming impact
  to a hit-and-run. A readout says what stays, what orbits, and what escapes.
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
