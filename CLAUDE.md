# GIC Social (Gathered Social)

Social media & email planning app for **Gathered in Christ: Southern Utah Women's Conference**.
Owner: Nicole (Nik). Other users: Kaylee, Amber (anyone can add their name with "+ Someone else").
Live at **https://authornjk.github.io/GIC-Social/** (GitHub Pages, branch `main`, root).

Started as a copy of Soiree Social (Bookish Summer Soirée) but is now fully separate:
no Soiree HQ, no Author Info, its own Firebase project.

## How it's built
- Single-file app: `index.html` (HTML + CSS + JS). `sw.js` is the service worker, `manifest.json` the PWA manifest.
- **Version bump on every change:** `APP_VERSION` in index.html and `CACHE_NAME` in sw.js must match
  (`gathered-social-vN`). Settings shows the number; phones update when they reopen the app.
- Firebase project **gic-social** (Spark/free plan). Firestore collections:
  `ss_posts2` (posts), `ss_cds2` (countdowns), `ss_meta` (`cats`, `deleted` tombstones, `confs` ticket dates),
  `ss_imgs2` (small graphics), `ss_presence` (who's on).
- Firestore rules: `ss_posts2` create needs a numeric `rev`; update needs `rev >=` the stored one; delete allowed;
  the other four collections are open. Every post edit goes through `touch(p, fields)` so the rev is stamped.

## Conferences
- Two conferences in `CONFS`: **feb** = Feb 27, 2027 and **sept** = Sept 18, 2027 (sept window starts Feb 28).
- Every post has `p.conf` ('feb' | 'sept'). The post form asks "Which conference?" first, then the date type.
- Date types: `fixed`, `premar1` = **Pre-tickets**, `postmar1` = **Post-tickets**, `flexible`.
  (Internal names are left over from the Soirée app; labels are what the user sees.)
- Ticket sale date per conference is set in **Settings → Conferences** (shared via `ss_meta/confs`).
  Until it's set, Pre/Post-tickets don't split the window.
- Lists are grouped under a heading per conference, the **next upcoming conference first** (`confOrder()`).
- Scheduler (`computeProposal`) runs once per conference; Fixed posts never move.

## Look
- Logo/colors from the Gathered in Christ logos: deep olive `#3C4A35`, sage, cream, terracotta, rose.
- Category colors (date tab + pill): Speaker rose `#B8636E`, Registration rust `#A65A32`, Event deep olive `#3C4A35`,
  Inspiration teal `#4F7C82`, Fun mustard `#C9A043`. Custom categories use sage.
- Conference heading colors: Feb `#3C4A35`, Sept `#B0603A`.

## Speaker posts
3 speakers + 1 musical guest. Nicole renames the slots herself as speakers are confirmed.

## Working notes
- Test locally with Playwright (Chromium is preinstalled); Firestore isn't reachable from the cloud
  container, so check live behaviour in the user's browser when the session is linked to her Mac.
- Commit as Claude with Nicole's email; push to `main` (that publishes the site).
