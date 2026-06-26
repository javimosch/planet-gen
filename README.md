# planet-gen — procedural planet flyby

Start in space, fly down to the surface with 6-DOF controls. Seamless
transition from orbit to surface.

```
./build.sh && ./planet-gen
```

## Controls

| Key | Action |
|-----|--------|
| W/S | Thrust forward/backward |
| A/D | Strafe left/right |
| SPACE / L-CTRL | Ascend/descend |
| Mouse | Pitch/yaw |
| Q/E | Roll |
| = / - | Speed up/down (0.125× to 64×) |
| TAB | Quit |
| ESC | Release cursor |

## How it works

**Terrain**: real 3D Perlin noise (`noise3`) layered into fbm, sampled on a
6-faced cube-sphere (96×96 cells per face). A domain-warped continent layer,
ridged-noise mountain belts masked to land, and a fine detail octave displace
each surface point outward — seamless across cube faces because the height
comes from the normalized 3D direction.

**Rendering**: the whole planet is baked into **one GPU mesh** (~110k triangles)
uploaded once. Per triangle a flat-shading sun term (surface normal · light) is
written into the vertex colors, so relief reads with light and shadow — then it's
a single `DrawModel` per frame, not thousands of cubes.

**Biomes**: height + latitude ramp — depth-shaded ocean → shore → grassland →
forest → rock → mountain → snow, with polar ice caps.

**Flight**: 6-DOF camera (pitch, yaw, roll) from orbit to the surface; altitude,
speed, and zone in the HUD.

## Architecture

Single MFL file (`planet.src`) compiled against raylib. The surface is a GPU
mesh built with the pointer/array FFI (`alloc` + `poke_f32`/`poke_u8` → `Mesh` →
`UploadMesh`/`LoadModelFromMesh`) — the same approach as the
[cyberpunk demo](https://github.com/javimosch/machin-game-demo-cyberpunk)'s chunk
terrain. `PLANET_SHOT=<prefix> ./planet-gen` auto-captures two views and exits.
