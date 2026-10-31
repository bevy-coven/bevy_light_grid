# The examples

Moved from the README on 2026-10-31, unchanged, when the README was brought to the [org documentation guide](https://github.com/bevy-coven/.github/blob/main/DOCUMENTATION.md).

```sh
cargo run -p bevy_light_grid --example shadow  # terminal: bake + compose, occlusion behind pillars
cargo run -p bevy_light_grid --example taxis   # terminal: photophobic / philic / tropic, side by side
cargo run -p bevy_light_grid --example beam    # the gif above. Needs a GPU.
```

`shadow` and `taxis` print the field as ASCII — no window, no GPU, so they run anywhere. `shadow` supplies a Bresenham line-of-sight closure and sweeps a flashlight cone over a cached static bake. `taxis` releases a photophobic walker into the light and prints it descending the gradient, then eases a phototropic body toward a lamp under its per-tick rate cap.

`beam` is the same three ideas on screen at once, with a legend: the static bake, the moving wedge composed on top of it every frame, and sixteen photophobic plus five photophilic creatures steering on nothing but `light_push_at`. It is the only example here that opens a window, and it is a **dev-dependency** that gives it one — the crate itself still takes `bevy_math` and `bevy_ecs` and nothing that draws, which `tests/leaf.rs` enforces by reading `[dependencies]` only.

## References

- Majercik et al., *Dynamic Diffuse Global Illumination*, JCGT 2019 — visibility-based leak suppression
- Björk & Michelsen, FDG 2014 — the flashlight cone as a vision/deterrent field
- Nakagaki et al., PRL 2007 — Physarum photoavoidance (the taxis this field supports)
- Zhang et al., PLoS ONE 10:e0123025 (2015) — light-gated fungal fruiting
- Chilimbi, Hill & Larus, *Cache-Conscious Structure Layout*, PLDI 1999 — restricting hot scans
