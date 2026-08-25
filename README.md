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
simulation. Two planets of real mass — a proto-Earth and an impactor you size
from 0.02 to 1 M⊕ — approach under their own gravity and collide. They are
made of 16k to 262k body-particles that all pull on each other; touching
particles push back like a stiff spring and bleed energy into heat, so the
material stays incompressible, splashes, glows where it was hit, and clumps
back together. Both bodies are differentiated: an iron core carrying a third
of the mass at two and a half times the mantle's density (Earth's numbers),
a mantle, and a crust; the impactor has a Mars-like density. Density is mass
— every particle has the same size, the core's weigh 2.5× — so cores sink
and the readout can say what the pieces are made of. Time runs in real hours:
the unit is √(R³/GM) for Earth, 13.4 minutes, and the clock in the corner
counts them.

Presets: **theia** (the canonical Moon-forming impact, 0.1 M⊕ at 45°),
**head-on**, **hit & run** (grazing and fast — the impactor survives and
leaves), **twins** (equal masses), **shatter** (2.6× escape speed). Both
bodies arrive turning about their axes, prograde, with days of about eight
hours — a young planet's. The readout groups particles by contact every second or so and reports
the largest body — its mass, how much of it came from the impactor, its iron
fraction and the length of its day from its spin — what is bound in orbit
around it, what is escaping, and the second body once one has formed, with
the same make-up: the numbers the literature judges a Moon-forming impact by.
A **moon** line says what Moon the disk in orbit would make, by Ida, Canup
& Stewart's fit to disk-accretion runs (from the disk's mass and angular
momentum: the months the sim cannot run, in one number). Under those, the
books: the barycentre's drift, the angular momentum in units of today's
Earth–Moon system and how much of it the run has kept, the mechanical
energy, the heat the contacts have made, and whether the two add up to what
they were at the start — plus how the contact cells are doing and how much
of the loose material the pairwise pass is holding.
*Advanced* has the mass ratio, the angle, the speed in units of escape
velocity, each body's spin as a fraction of its breakup rate (shown as the
day it makes; none, retrograde, or up to Ćuk & Stewart's 2.6-hour Earth),
the body count, the restitution, the core mass fraction and the impactor's
density. A spinning body is cut as the Maclaurin spheroid its spin calls for,
so it arrives already in equilibrium.

It is not SPH — no shocks, no vaporization — but everything the eye picks out
of the real runs is here: the deformation, the tidal arm, the disk,
re-accretion. The disk clumps in hours where the real Moon took months; the
clock is honest about the hours, not about that.

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

A single self-contained `index.html` (~790 KB) — no dependencies, no build step.
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

`collision.html` is WebGL 2, also dependency-free, and has no compute shaders
or atomics to lean on, so the two hard parts are done with tricks. Contacts:
a hashed grid of cells one neighbour-radius wide, filled by depth-peeling —
every particle draws a one-pixel point at its cell and the depth test keeps
the lowest index; eight passes give eight slots a cell (the books show eight
are never short, not even at the moment of impact), and a particle then
searches its 27 cells instead of the whole world. Gravity: particle-mesh —
mass is splatted onto a 64³ mesh by additive blending (cloud-in-cell), the
acceleration at every occupied cell is the direct sum over the 20³ fine cells
around it (3.75 R⊕: the whole planet and its near disk), taken at the cell's
centre of mass so that cell on cell is equal and opposite, plus the 16³
coarse cells' centres of mass beyond, and particles read it back trilinearly;
off the mesh, everything on it acts as one mass, and pulls back on it with
the equal and opposite. A mesh cell is 0.19 R⊕ — five particle diameters at
131k — so a moonlet a cell or two across is one the mesh can only smear, and
the first version's moonlets came apart into puffs. So the loose material —
everything beyond 1.3 radii of the planet's centre, up to 8k particles, the
farthest first — gets its gravity among itself corrected pairwise,
P³M-style: what Newton gives a pair less what the mesh already gave it, out
to five cells. The mesh's own pair force is measured at load by putting two
particles on the grid and repeating its arithmetic on the CPU, so the table
stays right whatever the mesh does. Moonlets bind as they should; the
planet, ten cells across, stays on the mesh. The approach is a two-body problem solved
on the CPU with the planets carried rigid, so the expensive part starts at
first touch. Symplectic Euler, a step set by the contact spring's period; the
cells are rebuilt every other step (a particle moves under a fifth of a radius
a step, the search radius has more than half to spare) and the mesh every
fourth (gravity changes on the scale of a time unit, a step is a thousandth of
one), which halves the cost of a step without changing a digit of the result.
The mesh's pull is given all at once, on the step it is measured, as the
impulse for the four steps it stands for: held for four steps instead it
lags, and a turning body feeling its own field a little behind it is braked
by it like by a tide — 0.4 % of the angular momentum in six time units,
which the books caught; as an impulse it keeps L to 0.01 % over nine hours.
The energy line's balance is what the step itself eats — the dashpot's work
is booked with the velocity before the kick — about a fifth of the heat at
this step size.

The picture is three screen-space passes borrowed from fluid rendering:
sphere impostors with per-fragment depth, a bilateral blur two particles wide
that melts them into a skin without crossing a silhouette, and normals from
the smoothed depth, lit by one sun. Attribute-less dust points can ride on
the bodies over the top (off by default — *advanced* turns them on), and a
friends-of-friends pass in a worker reads the positions back every second or
so to tell the planet from its disk and to keep the books.

## Changelog

Broad strokes, newest first; the commit history tells each one in full.

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
