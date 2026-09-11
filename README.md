# BJJ Positional State Machine

Single-file flashcard and positional roadmap app for no-gi Brazilian Jiu-Jitsu, organized around the Submeta / Lachlan Giles positional hierarchy. Open `index.html` in a browser. There is no build step; Tailwind loads from its CDN.

## What it does

- Roadmap view: every position ranked by tier (0 neutral through 5 ultimate control), color-coded by family. Click a node for the deep-dive drawer with top promotion route, bottom recovery route, dangers, and the key wedge or frame.
- Flashcard view: name, family badge, and tier shown; the four answer sections are hidden until revealed. "Got it" and "Needs review" drive a Leitner-style queue that persists in localStorage.
- Filter chips for family and tier apply to both views.
- Keys: space reveals all, 1 got it, 2 needs review, esc closes the drawer.

## Adding or editing a position

All content lives in the `NODES` array in `index.html`. Each node:

| field | meaning |
| --- | --- |
| `id` | slug used in edges and routes |
| `name`, `family`, `tier` | family is one of `guards`, `pins`, `back`, `legs`, `hub`; tier is 0 to 5 |
| `topLabel`, `bottomLabel` | who "top" and "bottom" mean here (e.g. Attacker / Defender for leg entanglements) |
| `summary` | one paragraph shown on the card front |
| `top`, `bottom`, `dangers`, `wedge` | the four reveal sections |
| `topRoute`, `bottomRoute` | ordered steps; a step starting with `@` is a node id and renders as a clickable chip |
| `topNext`, `bottomNext` | graph edges used for the roadmap card summary and the drawer's "reached from" list |

Every `@id` and edge must point at an existing node. The verification script used during development checks this in the browser console.

## Design decisions

- Tier and family are independent axes. SLX is filed under Guards but scored Tier 2 because it already controls the knee line.
- Standing and Turtle form a fifth "hub" family so the roadmap spans Tier 0 to Tier 5 and the turtle wrestle-up / sit-to-guard fork is a first-class node. That makes 17 nodes: the 16 requested plus Standing.
- Spaced repetition is a five-box Leitner queue measured in cards rather than days, because a study session is a few minutes long. Intervals are 1, 3, 6, 12, 24 cards.
