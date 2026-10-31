# Media: every image, with the command that made it

| File | What it shows | Command | Captured | Shows |
|---|---|---|---|---|
| `docs/beam.gif` | the README's hero: a flashlight wedge sweeps a dark room, pale creatures flee it and amber ones chase it; 7 s | not recorded when it was made; `cargo run --example beam` shows the same scene live | 2026-08-09 (`1d951d1`) | 0.1.0 |
| `docs/media/beam.png` | the README's still: frame 40 of the hero, with its legend | `ffmpeg -i docs/beam.gif -vf "select=eq(n\,40)" -frames:v 1 docs/media/beam.png` | 2026-10-31 | 0.1.0 (from the GIF) |

`docs/beam.gif` stays at that path: other pages already link it.
