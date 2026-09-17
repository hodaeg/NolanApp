# The Gutter & Glory Studio

A single-file, zero-dependency digital escape room that teaches the elements of the
graphic novel. Students are locked in the after-hours studio of founding letterer
Odessa Krahl and have to open nine panel locks and one master exit door to get out.
Opening the master lock issues a printable certificate of completion.

**Target audience:** 12th grade. **Target time:** 50 minutes.

## How to run it

Open `index.html` in any modern browser. That is the whole install step — there is no
build, no server, no package manager, and no network request. The file is
self-contained: all artwork is inline SVG, all CSS and JavaScript are inline, and all
fonts are local system stacks.

It can equally be emailed, dropped on a flash drive, put in an LMS as a file
attachment, or served statically (this repo deploys to Netlify with `publish = "."`).

## How the game is structured

**Stage one — non-linear discovery (Panels 1–8).** The studio floor is a point-and-click
room of nine artifacts. Panels 1–8 can be opened in any order. Each solved panel logs
a keyword into the **Studio Progress Master Codex** in the right-hand rail.

| Panel | Artifact | Mechanic | Curriculum |
|---|---|---|---|
| 1 | Odessa's sketchbook | Letter maze — trace the one legal path | Panels & gutters |
| 2 | Homophone degree wheel | Pick the correct homophone, then calibrate a Caesar wheel with the hint `5 = G` | Captions |
| 3 | Letterpress slate | Reassemble nine out-of-order riddle slugs | Bleed |
| 4 | Rebus pictographs | Picture + letter arithmetic; shaded letters spell the ingredient | Splash page, balloons, sound effects |
| 5 | Blue-line drafting board | Filter out non-repro blue, then read the graphite letters in drafting-guide order | Layout |
| 6 | Proofing galley | Flag five misspellings in a production memo and correct them | Frame |
| 7 | Lens rack | Order seven shot-distance plates tightest → widest | Close-up / long shot |
| 8 | Colourist's rack | Name six balloon types to unmask the paint rack, then map mood lines to hues | Coloration, balloon types |

**Stage two — linear climax (Panel 9 + master lock).** Panel 9, the five-rotor cryptex,
stays chained until all eight discovery panels are open. Each rotor plaque names a
codex keyword and a letter position; winding all five spells the cryptex word. The
master exit door then wants four brass dials (letter counts of four codex keywords)
plus that cryptex word. Throwing the bolts issues the certificate.

Every panel carries two progressive hints. Hints are counted and printed on the
certificate, so a student cannot hint their way out invisibly.

## Features

- **Point-and-click room**, not a quiz: an asymmetric comic-page grid of artifacts with
  real gutters, Ben-Day dot atmosphere, and a splash-panel exit door.
- **Studio Handbook** (top-right button): the full curriculum — layout, panel, frame,
  gutter, bleed, splash page, captions, speech and thought balloons, the six balloon
  shapes, sound effects, motion lines, shot distance, and the complete coloration
  colour-meaning table — with original SVG diagrams. Always available; the clock keeps
  running.
- **Resume support**: progress, per-puzzle scratch state, hint counts and the clock are
  persisted to `localStorage` under `gutter-glory-studio-v1`. Closing the tab and
  coming back picks up where the student left off. "Reset Studio" clears it.
- **Count-up clock** that turns red past 50:00.
- **Printable certificate**: student name, elapsed time, hints drawn, whether they came
  in under 50 minutes, all nine codex keywords, instructor signature line, and a
  deterministic verification code. A `@media print` block hides the entire game and
  prints the certificate alone.

## Key technologies

Hand-written HTML5, CSS3 (custom properties, CSS Grid, `@keyframes`, `@media print`),
inline SVG, and ES5-compatible vanilla JavaScript in a single IIFE. No frameworks, no
bundler, no webfonts, no external images, no analytics, no network calls.

## A note on the artwork

All illustration in the file is original inline SVG. The diagrams in the Studio
Handbook are new drawings of the concepts the source curriculum PDF labels; the
terminology and definitions are carried over from that PDF verbatim, but the PDF's own
embedded images are published comic pages and book covers (Captain Marvel, *New Kid*,
*Almost American Girl*, *Artemis Fowl: The Graphic Novel*) and are not reproduced here.
