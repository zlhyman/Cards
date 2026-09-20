# BJJ Positional State Machine

Single-file flashcard and positional roadmap app for no-gi Brazilian Jiu-Jitsu, organized around the Submeta / Lachlan Giles positional hierarchy. Open `index.html` in a browser. There is no build step; Tailwind loads from its CDN.

## What it does

- Roadmap view: every position ranked by tier (0 neutral through 5 ultimate control), color-coded by family. Click a node for the deep-dive drawer with top promotion route, bottom recovery route, dangers, the key wedge or frame, and the grips you will hear called there.
- Grips view: 21 grips grouped by type (collar and lapel, sleeve and pants, clinch and ties, hand clasps, leg and foot). Each one covers how it is held, what it controls, where it shows up, what it sets up, how to break it, and what it is commonly confused with.
- Flashcard view: pick the positions deck, the grips deck, or both. Answer sections stay hidden until revealed. "Got it" and "Needs review" drive a Leitner-style queue that persists in localStorage.
- Quiz view: shows a photo, asks which position or grip it is, then asks where top goes and where bottom recovers. Identification results feed the same Leitner boxes as the flashcards.
- Photos: every position and grip card can carry photos of real people. Seeds are hotlinked from Wikimedia Commons under open licenses. Each card's drawer has a curate panel to replace the photo with any image URL, a local file such as `img/half-guard.jpg`, or a YouTube link with `&t=` seconds, and to mark it verified. Edits are saved in the browser and can be exported from the Quiz tab as JSON to paste into `MEDIA_OVERRIDES`.
- Route videos: every top and bottom transition on the roadmap (90 edges) can carry one free YouTube video that teaches it. They live in `ROUTE_VIDEOS` in `index.html`, keyed `from|top|to` or `from|bottom|to`, and show up as a play mark next to the next-position chips in a position's drawer and the Call view, and as a "watch" link under the answer in quiz steps 2 and 3. The app only links out; nothing is copied. The Quiz tab's coverage panel counts how many transitions have one.
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
| `watch` (optional) | `[{ side: 'top' or 'bottom', url, title, channel, note }]`: a free video for something that is not a transition, such as the finish from that position; shown under that side's route and under the quiz answer |

Grips live in the `GRIPS` array. Each grip has `id` (prefixed `g-`), `name`, `aliases`, `type` (`collar`, `sleeve`, `clinch`, `clasp`, `legs`), `gi` (`gi`, `nogi`, `both`), `usedBy` (`top`, `bottom`, `both`), `positions` (node ids where it shows up), and the text fields `held`, `controls`, `setsUp`, `breaking`, `confuse`.

Every `@id`, edge, and grip position must point at an existing node, and grip ids must not collide with node ids. The verification script used during development checks this in the browser console.

## Photos and copyright

Only openly licensed photos are committed to this repo. Stills from paid instructionals (Submeta, BJJ Fanatics) or YouTube are copyrighted, so they are not committed. For personal study you can screenshot them into an `img/` folder next to `index.html` and point the curate panel at the file. Keep that folder out of git if the repo is public. YouTube clips can be embedded by pasting the video URL with a timestamp; embedding is allowed, copying the frames is not.

Every seeded Commons photo was located by file name only and starts as unverified. Open each card, look at the photo, and either mark it verified or replace it. The quiz can be restricted to verified photos.

Positions with no open-license photo found: Butterfly Guard, Single Leg X, Inside Sankaku, 50/50, Knee on Belly. Most gi grips and the wrestling ties also have none.

## Design decisions

- Tier and family are independent axes. SLX is filed under Guards but scored Tier 2 because it already controls the knee line.
- Standing and Turtle form a fifth "hub" family so the roadmap spans Tier 0 to Tier 5 and the turtle wrestle-up / sit-to-guard fork is a first-class node. That makes 17 nodes: the 16 requested plus Standing.
- Grips are a separate deck rather than fields on positions because one grip appears in many positions and the confusion (collar grip vs cross grip vs cross collar) is about the grip itself, not the position.
- Spaced repetition is a five-box Leitner queue measured in cards rather than days, because a study session is a few minutes long. Intervals are 1, 3, 6, 12, 24 cards.
