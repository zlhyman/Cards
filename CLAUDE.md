# BJJ Positional State Machine

Single-file app: `index.html` (vanilla JS, Tailwind via CDN, no build). `README.md` documents the data schema. All content lives in arrays inside `index.html`: `NODES` (positions), `GRIPS`, `MEDIA_SEED` (photos), `MEDIA_OVERRIDES`.

## Conventions

- Keep it one file, no build step, no external scripts beyond the Tailwind CDN.
- Every `@id`, `topNext`, `bottomNext`, and grip `positions` entry must reference an existing node id. Grip ids are prefixed `g-`.
- Verify in headless Chromium before committing. The scratch script approach that works: build a local Tailwind CSS from `index.html` classes and route `https://cdn.tailwindcss.com` to it in Playwright, then check for page errors, dangling ids, and horizontal overflow at 390px.

## Open task: fill the photos from the Ladder stills

Say "do the open task" and this is it. Every card needs one verified photo of real people. The sources are on this Mac:

- `~/Ladder` is the Ladder pipeline repo (github.com/zlhyman/ladder). `~/Ladder-build` holds its artefacts: `bjj-frames/` and `bjj-inbox/` (frames from the user's own training clips), and the raw clips under `The Gentle Art - 巴西肉松/BJJ inbox/` (there or under `~/Documents`). `python3 ~/Ladder/scripts/find_video.py "<partial name>"` resolves a clip wherever it lives. Any directory of stills whose names end in `_<seconds>s.jpg` came from `still_receiver.py`, captured from Nomadic Grappling, a paid instructional.
- Tools: `swift ~/Ladder/scripts/frames.swift <video> <out-dir> [count] [maxEdge] [--from S] [--to S]`; `python3 ~/Ladder/scripts/contact_sheet.py <video> <out.png> [from to every]` for a timestamped grid of a clip; `python3 ~/Ladder/scripts/stills_sheet.py <dir> <out.png>` for captured stills; `scripts/crop.swift` to crop. Always read a full contact sheet before choosing a frame.
- Do not touch the unrelated Chinese-vocabulary "Cards" project in the Obsidian vault at `01_Projects/Cards`.

Procedure:

1. Inventory every frame and still directory under `~/Ladder-build` and the clips in the BJJ inbox. Build and look at a contact sheet per source.
2. For each card id below, pick one still that shows the named thing unambiguously. Own footage first. Crop tight to the two bodies, long edge 900px, save as `img/<id>.jpg` in this repo. Leave a card empty if no source has it, and say so.
   - Positions: standing, closed-guard, half-guard, butterfly, slx, ashi, inside-sankaku, fifty-fifty, side-control, north-south, knee-on-belly, mount, s-mount, back-control, body-triangle, back-mount-belly, turtle.
   - Grips: g-collar, g-cross-collar, g-cross-grip, g-sleeve, g-collar-sleeve, g-lapel, g-pants, g-belt, g-underhook, g-overhook, g-collar-tie, g-wrist, g-crossface, g-seatbelt, g-gable, g-s-grip, g-kimura, g-front-headlock, g-bodylock, g-heel, g-monkey.
3. Wire each in as the first entry for that id in `MEDIA_SEED` in `index.html`: `{ src: 'img/<id>.jpg', page: '', credit: 'own training footage' or 'Nomadic Grappling still, personal use only', note: '<one clause>', verified: true }`. Keep existing Commons entries after it.
4. For every remaining Commons entry, download `https://commons.wikimedia.org/wiki/Special:FilePath/<file>?width=900`, look at it, and either set `verified: true` with `credit` from the Commons API (`action=query&titles=File:<file>&prop=imageinfo&iiprop=extmetadata&format=json`, fields Artist and LicenseShortName) or delete it.
5. Open `index.html` in the browser: thumbnails load on Roadmap and Grips, the Quiz coverage panel shows the new counts, the quiz runs.
6. Commit and push `index.html` only. `img/` stays gitignored because this repo is public and the Nomadic Grappling stills must not be published. The commit message lists which ids got own-footage stills, which got instructional stills, which Commons seeds were verified or dropped, and which ids are still empty.
7. Tell the user which cards are still missing, and that phone access to the photos means either making this repo private and committing own-footage stills only, or copying `img/` to the phone another way.
