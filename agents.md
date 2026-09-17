# AGENTS.md — The Gutter & Glory Studio

## What this repo is

One deliverable: `index.html`. A self-contained digital escape room about the elements
of the graphic novel. There is no build step, no dependency manifest, and no server
code. `netlify.toml` publishes the repo root with an empty build command.

## Hard constraint: single-file portability

`index.html` must keep working when opened straight off the filesystem with
`file://`, with no network. This is a product requirement, not a preference. It means:

- **No external requests of any kind** — no webfonts, no CDN scripts, no image files,
  no `fetch`. All artwork is inline SVG authored by hand in the file.
- **No build tooling.** Do not introduce npm, a bundler, TypeScript, or a preprocessor.
  Edit the HTML/CSS/JS in place.
- **No new files that the page depends on.** README, AGENTS and netlify.toml are
  documentation and deploy config; the game never reads them.

If a change cannot be made without breaking this, say so rather than working around it.

## Architecture of `index.html`

Read it top to bottom; it is ordered deliberately.

1. `<head>` — all CSS. Section banner comments (`/* ===== PUZZLE 7 — lens rack ===== */`)
   mark per-puzzle blocks. Palette, typography and frame geometry are CSS custom
   properties on `:root`. Fonts are **local stacks only** (Impact / Trebuchet MS /
   Courier New / Palatino) — characterful, but zero-dependency.
2. Static body markup — masthead, status rail, `#room` (empty; injected), the
   `#door-splash` exit-door splash panel (hand-authored SVG), the codex `<aside>`,
   the `#veil` modal shell, `#cert-veil`, and the toast stack.
3. One `<script>` containing a single IIFE, in this order:
   - `SV` / `art()` and the `ART` object — nine inline SVG artifact illustrations.
   - `KEYPACK` / `KEY` — the answer key.
   - `PANELS` — the panel registry (`id, name, tag, art, kicker, hints[2]`, plus
     `locked` on panel 9).
   - State: `S`, `load()`, `save()`, `solvedCount()`, `discoveryDone()`, `hintCount()`.
   - Chrome: `fmt()`, `tickClock()`, `toast()`, `renderCodex()`, `esc()`, `renderRoom()`.
   - Modal shell: `openSheet()`, `closeSheet()`, `briefing()`, `answerBar()`,
     `wireAnswerBar()`, `work()`, `boxRow()`, `openPanel()`.
   - `BUILDERS` / `WIRES` — dispatch tables keyed by panel id, then nine
     `BUILDERS[n]` / `WIRES[n]` pairs with their puzzle data above each pair.
   - `renderDoor()`, the certificate (`verifyCode()`, `openCert()`, `closeCert()`),
     `openHandbook()`, and the boot block.

### The builder/wire contract

Every panel is exactly two functions:

- `BUILDERS[n]()` returns an **HTML string** and must be pure apart from calling
  `work(n, initialScratchState)`. It never touches the DOM.
- `WIRES[n]()` runs immediately after that string is in the DOM and attaches all
  listeners. It must end by calling `wireAnswerBar(n)` (optionally with an `onSolve`
  callback) unless the answer bar is gated, in which case it calls `wireAnswerBar(n)`
  at the moment it injects the bar.

`openPanel(n)` is the only caller of either. Re-opening a panel re-runs both, so
builders must render correctly from persisted scratch state — this is how resume works.

### Non-obvious decisions

- **`openSheet()` clones `#sheet-body` before writing.** Several panels delegate events
  from `sheetBody` itself rather than from an inner node. Cloning drops the previous
  panel's listeners instead of stacking a new set on every open. If you add a panel that
  delegates from `sheetBody`, this is why it works.
- **`KEY` is base64 in `KEYPACK`, not plaintext.** Validation is client-side because the
  file must run offline; base64 only stops View Source from handing over an answer sheet.
  Do not "improve" this into a real secret — it cannot be one here. Do not make it
  plaintext either.
- **Scratch state lives in `S.work[n]`**, is mutated in place by wires, and is saved with
  `save()` after every change. `work(n, init)` seeds it once and returns the live object.
- **The answer chain is closed.** Panels 4, 2, 8, 6 and 7 feed the Panel 9 cryptex
  (`ROTORS`), and panels 5, 9, 1, 3 feed the door dials (`KEY.dials`). Changing any
  keyword breaks two downstream locks. The relationships are:
  - `ROTORS` → letter positions inside `KEY[4]`, `KEY[2]`, `KEY[8]`, `KEY[6]`, `KEY[7]`
    spell `KEY[9]`.
  - `KEY.dials` → letter *counts* of `KEY[5]`, `KEY[9]`, `KEY[1]`, `KEY[3]`.
  - `KEY.door` === `KEY[9]`.
  - `REBUS[i].word.charAt(REBUS[i].key)` in card order spells `KEY[4]`.
  - `BOARD.black` letters sorted by `.g` spell `KEY[5]`.
  - `FIX[i].word.charAt(FIX[i].key)` in order spells `KEY[6]`.
  - `SHOTS[i].L` in index order (tightest → widest) spells `KEY[7]`.
  - `MOODS[i]` → `TUBES` letter spells `KEY[8]`.
  - `HOMO[i].opts[HOMO[i].right].num` through a Caesar wheel at the offset where
    `5 → G` spells `KEY[2]`.
  - The maze `MZ` must have **exactly one** simple path from `MZ.start` to `MZ.finish`;
    its letters spell `KEY[1]`. Cell (2,3) is blocked specifically to kill a second route.
- **The wheel's inner ring is a sequential 1–26**, so `5 = G` is a genuine Caesar offset
  the student can derive. The source material used a scrambled ring, which is not
  solvable from that one hint.
- **Panel 8's paint-tube letters are stencilled marks, not colour initials.** Colour
  initials cannot spell the needed keyword; the tube letters can.
- **Panel 9's answer input is `readOnly`** and driven entirely by the rotors.
- **`localStorage` on purpose, not a database.** Nothing here is shared or
  server-authoritative; it is one student's session on one machine.

## Conventions

- ES5-compatible JavaScript: `var`, `function`, no arrow functions, no template
  literals, no `let`/`const`, no optional chaining. It reads consistently with the rest
  of the file and keeps the "opens anywhere" promise literal.
- Build markup by string concatenation into a local `h`, then assign once.
- Escape any student-supplied text with `esc()` before it reaches `innerHTML`. Puzzle
  copy is author-controlled and inlined directly.
- Curriculum wording is quoted from the source PDF and should stay verbatim. If you
  reword a definition, you are changing the lesson.
- Two hints per panel, stored in `PANELS[n].hints`, ordered gentle → explicit.

## Verifying a change

There is no test runner and no build to run. The practical check is to open
`index.html` in a browser and play the affected panel. If you touch the answer chain,
re-derive every relationship in the "answer chain is closed" list above before you
consider it done — a wrong letter there produces a lock that cannot be opened at all,
with no error message.
