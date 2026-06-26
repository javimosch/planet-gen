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

**Terrain**: 6-faced cube-sphere grid (20×20 per face, 2,400 surface
cubes). Each cube is displaced outward by a 4-octave sine-wave fbm
function that produces continent-scale landmasses, mountain ranges,
and ocean basins.

**Biomes**: Height-mapped — deep ocean → shore/sand → grassland →
forest → mountain rock → snow caps.

**Atmosphere**: Wireframe sphere shells at the atmosphere edge and
cloud layer altitude.

**Flight**: 6-DOF camera with pitch, yaw, and roll. Altitude, speed,
and zone indicator in the HUD.

## Architecture

Single MFL file (`planet.src`) compiled against raylib. No mesh
manipulation — pure `DrawCube` calls for every terrain cell.
