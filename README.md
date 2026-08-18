# Trace Ring, Fragment Sync & Liquid State — GenLayer Loading Screens

Three standalone, animated loading screens for GenLayer. All use the exact
traced path of the GenLayer mark — pixel-accurate to the official logo,
not an approximation — and are self-contained single HTML files with no
build step.

## Files

| File | Concept | Mechanic |
|---|---|---|
| `trace-ring.html` | **Trace Ring** | The mark draws itself in with an animated stroke, then holds as a filled, pulsing glow inside two counter-rotating dashed rings. |
| `fragment-sync.html` | **Fragment Sync** | The mark's three pieces (left wing, right wing, core) fly in from offset positions and snap together, backed by a drifting grid and a real progress bar. |
| `liquid-state.html` | **Liquid State** | A wave fills the mark from the bottom up like liquid in a glass, clipped exactly to the mark's silhouette, synced to a real percentage counter. |

## Trace Ring

**Sequence:**
1. The mark's outline draws itself via animated `stroke-dashoffset`
   (~1.8s), then fills solid white.
2. Two dashed rings — one fast, one slow, opposite directions — orbit
   the mark continuously.
3. A soft glow pulses on the mark, looping indefinitely.
4. Status text: `INITIALIZING GENLAYER` with animated ellipsis, subtext
   `compiling intelligent contract`.

**Customizing:**
- Ring speed/direction: `.ring` and `.ring--slow` animation durations in
  the `@keyframes spin` / `spin-rev` rules.
- Draw-in speed: the `draw` animation duration on `.logo path`.
- Copy: the two `.label` divs in the HTML.
- Color: single `--fg` variable controls mark, rings, and glow.

No JavaScript — pure CSS/SVG. Loops forever as-is; there's no fixed "done"
state, so it's best suited to loads of unknown/variable length.

## Fragment Sync

**Sequence:**
1. Right wing, left wing, and core piece fly in from different offsets
   and rotations, converging with a slight overshoot on the core (~1.1s).
2. A single scan-line sweeps top-to-bottom once during assembly.
3. A real progress bar fills 0→100% over 2.4s, driven by
   `requestAnimationFrame`, with status text updating from
   `Assembling node` → `Syncing state` → `Ready`.
4. Background: a faint grid, masked to a radial fade, drifting slowly.

**Customizing:**
- Piece entry offsets/rotation: `.piece--right`, `.piece--left`,
  `.piece--core` initial `transform` values and their `@keyframes`.
- Progress duration: `duration` constant in the `<script>` block (currently
  `2400`ms) — keep it in sync with the CSS `.bar span` animation duration
  if you change it.
- Status copy: the `messages` array in the script.
- Grid density/speed: `.grid` `background-size` and `drift` animation.

Has a defined end state (`Ready` at 100%) — better suited to loads with a
roughly known duration, or wire the progress bar to real load events by
replacing the `requestAnimationFrame` timer with your actual progress
signal.

## Liquid State

**Sequence:**
1. A faint outline of the mark is visible immediately (`.outline`), so the
   shape reads before any liquid arrives.
2. A wave — generated from real sine geometry, not a CSS trick — rises
   from the bottom of the mark to the top over 3s, clipped exactly to the
   mark's silhouette via `clipPath`. The wave also drifts sideways
   continuously for a liquid feel.
3. Rise progress and the on-screen `pct` counter are driven by the same
   eased value in `<script>`, so they're always in sync — swap the easing
   source for your real load progress and the fill follows it exactly.
4. On reaching 100%: status changes to `State synced` and the mark gets a
   looping soft glow (`.glow-final.done`).

**Customizing:**
- Fill duration: `duration` constant in the `<script>` block (currently
  `3000`ms).
- Wave shape/speed: the wave path's `d` attribute (period/amplitude baked
  into the coordinates) and the `wave-drift` animation duration.
- Copy: `status` text default (`Filling state`) and the final string
  (`State synced`) in the script.
- Color: single `--fg` variable controls the outline, wave fill, and glow.

Like Fragment Sync, this has a real percentage tied to actual motion — the
`ease` value in `frame()` is the single source of truth for both the
liquid's height and the printed `%`, so it's the easiest of the three to
wire up to genuine load progress instead of a timer.

## Using any of the three

```html
<iframe src="trace-ring.html" style="border:0;width:100%;height:100%"></iframe>
```

or inline the `<body>` markup directly into your own page — all three are
self-scoped and won't leak styles as long as class names stay intact.

## Accessibility

All three respect `prefers-reduced-motion`: Trace Ring shows the mark
fully drawn and filled with no motion; Fragment Sync shows all pieces
already in place with the bar already full; Liquid State shows the mark
fully filled at 100%. No motion plays in any of them.

## Browser support

Native SVG, CSS custom properties, `prefers-reduced-motion`, and
(Fragment Sync and Liquid State) `requestAnimationFrame`. Works in current
Chrome, Safari, Firefox, and Edge. No IE support.
