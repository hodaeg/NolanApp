# Enhancements backlog

Documentation only — `index.html` never reads this file. Logged here so the
ideas from the visual-flair review aren't lost between sessions.

## Deferred visual flair (not yet built)

Ordered roughly by expected payoff:

1. **Solve + unchain + bolts celebration.** Right now a solved lock is a
   toast plus a static "Open" stamp on the next render; Panel 9's chain and
   the exit door's chain both just vanish. This is the highest-payoff pass:
   animate the stamp slamming down on solve, the Panel 9 `.chain` overlay
   snapping instead of disappearing once `discoveryDone()` flips true, and
   the door's `#door-chain` breaking when the bolts are thrown. All doable
   as CSS `@keyframes` triggered by class toggles — no new state needed.
2. **Codex & progress-rail polish.** Keyword slots type/stamp in instead of
   swapping instantly (`renderCodex()`), the `.codex .bar i` progress fill
   pulses as it grows, the clock gets a subtle per-second beat, and the
   "Locks Open" counter rolls over instead of just re-rendering.
3. **Per-puzzle micro-interactions.** Ink flows along the Panel 1 maze
   trail as it's drawn, the Panel 2 cipher wheel eases into rotation
   instead of snapping, the Panel 9 cryptex rotors spin through letters,
   and Panel 7's lens plates slide into their bays.
4. **Ambient room atmosphere.** The Ben-Day dot layer and vignette were
   rebuilt (see "Studio light" below) to stop shimmering, but they're still
   inert otherwise — no slow drift, no idle "breathing" on artifact tiles.
   Cheapest to add, easiest to overdo; needs a light touch.
5. **Certificate reveal.** The seal stamps on with a rotate-and-thud, the
   double border draws itself in, the stats count up. Print output must
   stay exactly as-is — this is reveal-on-open only.

## Studio light (built this session)

Implemented: one implied lamp anchored off-page up-left of the title
plate, expressed as a background glow (`.lamp`) plus a computed rim
(`--rim-l` / `--rim-dx` / `--rim-dy`, written by `relight()`) on `.stat`,
`.artifact`, `.codex` and `.door-splash`. `.title-plate` — the light source
itself — gets a literal gradient border. A single `--flick` custom property
on `body` drives a randomized 4–6s rest / 2–3-flicker burst cycle; reduced
motion holds it permanently lit. Root cause of the old shimmer (a 0.2px
antialias band in `.atmos`'s tiled radial-gradient dots) was fixed in the
same pass.

**Not done, and worth a deliberate look if the light gets revisited:**
- Gradient borders only exist on `.title-plate`. Every other participating
  card expresses the light as a box-shadow rim instead (keeps the black
  comic frames intact, per the "frame" being a taught curriculum term).
  Flagged during planning as a deviation from a literal reading of "the
  same gradient replicated on subsequent cards" — revisit if the rim reads
  as too subtle in practice.
- Scope is the studio floor only (banner, status rail, room tiles, codex,
  exit door). The modal sheet, handbook and certificate intentionally do
  not participate.
- Tuning (`LIGHT_RADIUS`, `LIGHT_FALLOFF`, `LIGHT_FLOOR`, offsets) was set
  by eye at 1360px. Re-check at narrow/mobile widths and at the exit door,
  which sits far enough down the page to be the worst-case falloff.

## Incidental findings (not flair, but worth fixing)

- **`.panel` CSS is dead code** (originally lines 69–81). No element in the
  file uses `class="panel"`, so the "newsprint tooth" texture — including
  a `mix-blend-mode:multiply` pass — never renders anywhere. Either delete
  the rule or apply it to `.artifact` (which already has a free `::after`)
  to actually get the texture the comment promises.
- **`setInterval(tickClock, 1000)` is registered twice** — once right after
  `tickClock` is defined, again in the boot block at the bottom of the
  script. Harmless today (the function just repaints idempotently) but
  it's two intervals doing one job.
