# The field: not a renderer

Moved from the README on 2026-10-31, unchanged, when the README was brought to the [org documentation guide](https://github.com/bevy-coven/.github/blob/main/DOCUMENTATION.md).

That distinction is the whole point. Your GPU lighting pass already knows how bright every pixel is — but the answer lives in a framebuffer, and a creature deciding whether to scuttle into shadow cannot read a framebuffer. This crate computes the same question on the CPU, at cell resolution, in a form gameplay can sample and differentiate.

```rust,ignore
use bevy_light_grid::{LightGrid, FlashlightCone, light_push_at};

let mut field = LightGrid::new(width, height, floor_cells());

// Static: expensive, event-driven. Walls cast shadow via YOUR occlusion test.
field.bake(&fixtures, |a, b| map.line_of_sight(a, b));

// Dynamic: cheap, every tick. Only the moving cones are re-added on the cached base.
field.compose(&cones, |a, b| map.line_of_sight(a, b));

let brightness = field.sample_cell(cell);
let flee = light_push_at(&field, cell, -gain);   // photophobic: descend toward the dark
```

Three marker components come with it — `Photophobic`, `Photophilic`, `Phototropic` — plus `phototropic_scale` for things that grow toward light rather than walk toward it.

## Two layers, for a reason

The **base** bake is `O(fixtures × range²)` and runs only when a fixture changes. The **compose** pass copies that cached base and adds just the moving cones, so a walking flashlight's beam sweeps live for a fraction of the cost. Splitting them is what makes a per-tick lighting field affordable at all.

## Occlusion is yours

`bake` and `compose` take a `los` closure. "Can this cell see that one" is a question about your map — tiles, portals, a BVH, whatever you have — and a lighting crate that answered it would be guessing. The closure is monomorphised at the call site, so it costs nothing over an inlined method.

## Determinism

Both passes accumulate with a non-associative `+=`, so **sort `fixtures` and `cones` by source cell before calling**. The crate sees a slice, not the query that produced it, so it cannot do this for you — and an unsorted batch is the classic way a "deterministic" simulation quietly stops being one.

Rock cells hold exactly 0.0 forever (both writers gate on the floor mask), so the hot per-tick scans visit only floor cells — bit-identical to a full-grid pass, not an approximation. `cells()` exposes the full grid so you can fold that invariant into your own hash.
