# BOLIDE/SIM — Asteroid Impact Simulator

A single-file WebGL / Three.js simulation of an asteroid entering Earth's atmosphere and striking the surface: procedural planet, physically based entry and impact model, and a cinematic, log-time aftermath with permanent surface alteration.

**This is a fun test for frontier models.** Each model gets the same one-shot prompt (below) and has to produce a complete, working single HTML file. This particular version was built by **Claude Opus 5.5 on High effort**.

## Run it

Open `index.html` in a modern desktop browser (Chrome, Edge or Firefox with WebGL2). Three.js r160 is loaded from the jsDelivr CDN, so an internet connection is required. Nothing else is fetched: the Earth, clouds, atmosphere, stars, asteroid and all effects are generated procedurally in shaders and code.

If your browser blocks ES modules from `file://`, serve the folder locally, e.g. `python -m http.server`, then visit `http://localhost:8000`.

## Controls

| Input | Action |
|---|---|
| Click the globe | Set ground zero (land or ocean is detected from the terrain under the cursor) |
| Drag | Pan over the globe |
| Right-drag / Shift-drag | Rotate heading and tilt toward the horizon |
| Mouse wheel | Change altitude |
| **LAUNCH** | Run the event (impacts accumulate on the planet, up to 6 craters) |
| **RESET** | Remove all craters, dust and effects and free their GPU resources |
| Cinematic camera | Automatic chase / witness / aftermath camera; dragging takes control |
| Show global dust veil | Hide the stratospheric dust and impact winter to inspect the crater and damage (the simulation itself is unaffected) |
| Speed | Playback speed, 0.25× to 4× |

Presets: Chelyabinsk, Tunguska, Barringer (Meteor Crater), Nördlinger Ries, an Apophis-class body, Eltanin (ocean), Chicxulub, and a long-period comet. The planet is procedural, so presets move ground zero to the nearest suitable land or ocean rather than the real-world site.

## The model

**Inputs:** diameter, composition, bulk density, yield strength, entry velocity, entry angle, approach bearing and impact site (water depth and target material are derived from the terrain at the site).

**Atmospheric entry:** a 2-D integration in the trajectory plane around Earth's centre with central gravity, drag, ablation, and Chyba / Hills–Goda "pancake" fragmentation once ram pressure exceeds the body's strength. Each run is classified as an intact impact, a fragmented impact, an airburst, or a grazer that skips back to space. Sanity checks: Chelyabinsk bursts at ~35 km, Tunguska at ~15 km, and Barringer leaves a ~1.1 km crater (the real one is 1.2 km).

**Effects** (Collins, Melosh & Marcus 2005 scaling laws): transient and final crater size, depth and morphology (simple bowl, central peak, peak ring), melt volume, fireball radius, thermal radiation radii, air-blast overpressure rings (1 / 5 / 20 psi), seismic magnitude, ejecta thickness, tsunami amplitude and travel time, and recurrence interval.

**Aftermath** plays on a logarithmic clock, from milliseconds to days, so each phenomenon appears at its physical time:
- impact flash and blackbody fireball cooling into a sunlit debris cloud
- Sedov–Taylor blast wave decaying to acoustic speed, with a condensation dome, ground ring and screen-space refraction
- crater excavation and collapse, glowing melt sheet, ballistic ejecta curtain over a curved Earth, and rays with the oblique-impact "forbidden zone"
- scorched land, destroyed city lights, Tunguska-style tree-fall pattern for airbursts
- tsunami wave train for ocean impacts
- global dust veil and ejecta re-entry glow for extinction-scale events

## Deliberate visual liberties

- Terrain relief is exaggerated ~55× and crater / tsunami relief ~4×; true relief is nearly invisible from orbit.
- Asteroids and meteor trails smaller than a pixel are drawn at a minimum on-screen size.

## Rendering highlights

Eroded fBm terrain with analytic gradients and distance-based LOD, PCG-hashed gradient noise mirrored bit-for-bit in JS for CPU land/ocean classification, ray-marched Rayleigh + Mie + aerosol single scattering, logarithmic depth buffer with view-space-origin particle transforms for metre-scale precision, instanced GPU ballistic particles, and an HDR bloom + ACES post chain.

## The prompt

> Write a complete, single-file HTML/JS WebGL application using Three.js that simulates an asteroid impact on a 3D Earth. This is a benchmark test of your 3D graphics, procedural generation, physics simulation, and creative problem-solving capabilities.
>
> REQUIREMENTS:
> - Output entirely in a single HTML file containing all CSS and JavaScript.
> - Use Three.js via a CDN.
> - Zero external assets: Do not load external images, textures, or 3D models. The Earth (oceans, landmasses, clouds), asteroid, and background must be procedurally generated using GLSL shaders, noise functions, or native Three.js geometry.
> - A 3D Earth viewed from low orbit
> - Implement a user interface overlay to configure the impact. You must determine and implement the most critical physical variables necessary for a scientifically accurate simulation.
> - Simulate the atmospheric entry and surface impact with a strict focus on realism, visual fidelity, and accurate physics.
> - You have full creative control over how the destruction is rendered. Utilize advanced WebGL techniques (such as custom shaders, particle systems, geometry displacement, or post-processing) to visualize the transfer of energy, atmospheric effects, and permanent alteration to the planetary surface.
> - The resulting sequence should be beautiful, physically grounded, and demonstrate a high level of graphical mathematics.
> - Include a way to reset the scene and trigger the event again without memory leaks.
>
> Do not write placeholders or partial snippets. Deliver the complete, functional code ready to run in a browser.
