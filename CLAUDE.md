# BJJ Positional State Machine

Single-file app: `index.html` (vanilla JS, Tailwind via CDN, no build). `README.md` documents the data schema. All content lives in arrays inside `index.html`: `NODES` (positions), `GRIPS`, `MEDIA_SEED` (photos), `MEDIA_OVERRIDES`.

## Conventions

- Keep it one file, no build step, no external scripts beyond the Tailwind CDN.
- Every `@id`, `topNext`, `bottomNext`, and grip `positions` entry must reference an existing node id. Grip ids are prefixed `g-`.
- Verify in headless Chromium before committing. The scratch script approach that works: build a local Tailwind CSS from `index.html` classes and route `https://cdn.tailwindcss.com` to it in Playwright, then check for page errors, dangling ids, and horizontal overflow at 390px.

## Open task: verify and complete the photos

Context: the photo seeds in `MEDIA_SEED` were found by file name only, from a sandbox that could not fetch or view images. Every entry is `verified: false`. This machine has real network access, so finish the job:

1. For each entry in `MEDIA_SEED`, download the image (`https://commons.wikimedia.org/wiki/Special:FilePath/<file>?width=900`) to a scratch folder and look at it. Confirm it shows the named position or grip with real people, not a drawing. Set `verified: true`, tighten the `note`, and fill `credit` from the Commons file page (author and license) via the API: `https://commons.wikimedia.org/w/api.php?action=query&titles=File:<file>&prop=imageinfo&iiprop=extmetadata&format=json`.
2. Drop any seed that is wrong. Replace it if Commons has a better file.
3. Find open-license photos for the cards with none: Butterfly Guard, Single Leg X, Inside Sankaku, 50/50, Knee on Belly, and the grips listed as missing in the Quiz tab's coverage panel. Sources in order of preference: Wikimedia Commons, Flickr Creative Commons (CC BY or CC BY-SA only, no NC needed but note it), Openverse. Judo and MMA competition photos often show these positions under other names.
4. Never commit stills from paid instructionals or YouTube frames. Those go in `img/`, which is gitignored, via the in-app curate panel.
5. If the user has exported curation JSON from the app, merge it into `MEDIA_OVERRIDES` or fold it back into `MEDIA_SEED`.
6. Commit with a message listing which cards were verified, replaced, or added.
