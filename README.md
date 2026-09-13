# BJJ Positional State Machine

Single-file flashcard and positional roadmap app for no-gi Brazilian Jiu-Jitsu, organized around the Submeta / Lachlan Giles positional hierarchy. Open `index.html` in a browser. There is no build step; Tailwind loads from its CDN.

## What it does

- Roadmap view: every position ranked by tier (0 neutral through 5 ultimate control), color-coded by family. Click a node for the deep-dive drawer with top promotion route, bottom recovery route, dangers, the key wedge or frame, and the grips you will hear called there.
- Grips view: 21 grips grouped by type (collar and lapel, sleeve and pants, clinch and ties, hand clasps, leg and foot). Each one covers how it is held, what it controls, where it shows up, what it sets up, how to break it, and what it is commonly confused with.
- Flashcard view: pick the positions deck, the grips deck, or both. Answer sections stay hidden until revealed. "Got it" and "Needs review" drive a Leitner-style queue that persists in localStorage.
- Call view: simulates a positional sparring call such as "Half Guard, bottom player starts with cross collar grip." Say both players' destinations out loud, then reveal the routes and what the grip changes. Random or hand-picked position, holder, and grip.
- Filter chips: family and tier for positions, grip type and gi / no-gi for grips.
- Keys: space reveals, 1 got it, 2 needs review, n new call, esc closes the drawer.

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

Grips live in the `GRIPS` array. Each grip has `id` (prefixed `g-`), `name`, `aliases`, `type` (`collar`, `sleeve`, `clinch`, `clasp`, `legs`), `gi` (`gi`, `nogi`, `both`), `usedBy` (`top`, `bottom`, `both`), `positions` (node ids where it shows up), and the text fields `held`, `controls`, `setsUp`, `breaking`, `confuse`.

Every `@id`, edge, and grip position must point at an existing node, and grip ids must not collide with node ids. The verification script used during development checks this in the browser console.

## Design decisions

- Tier and family are independent axes. SLX is filed under Guards but scored Tier 2 because it already controls the knee line.
- Standing and Turtle form a fifth "hub" family so the roadmap spans Tier 0 to Tier 5 and the turtle wrestle-up / sit-to-guard fork is a first-class node. That makes 17 nodes: the 16 requested plus Standing.
- Grips are a separate deck rather than fields on positions because one grip appears in many positions and the confusion (collar grip vs cross grip vs cross collar) is about the grip itself, not the position.
- Spaced repetition is a five-box Leitner queue measured in cards rather than days, because a study session is a few minutes long. Intervals are 1, 3, 6, 12, 24 cards.
