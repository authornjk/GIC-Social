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
- Built-in conferences in `BASE_CONFS`: **feb** = Feb 27, 2027 and **sept** = Sept 18, 2027. More are added in
  **Settings → Conferences → + Add** (key `cYYYYMMDD`); edits/additions live in `_confExtra`, shared via `ss_meta/confs`
  (`extra` + `confs[key].tix`). `rebuildConfs()` merges them into `CONFS`; each window starts the day after the
  previous conference (`lo`). A conference's name defaults to the label of the countdown on its date.
- One sheet (`cd-modal`) edits a conference (name, date, tickets on sale, color) and its countdown together;
  "Other countdowns" lists only countdowns that aren't on a conference date.
- Every post has `p.conf` (a key of `CONFS`). The post form asks "Which conference?" first, then the date type.
- Date types: `fixed`, `premar1` = **Pre-tickets**, `postmar1` = **Post-tickets**, `flexible`.
  (Internal names are left over from the Soirée app; labels are what the user sees.)
- Ticket sale date per conference is set in **Settings → Conferences** (shared via `ss_meta/confs`).
  Until it's set, Pre/Post-tickets don't split the window.
- Lists are grouped under a heading per conference, the **next upcoming conference first** (`confOrder()`).
- Scheduler (`computeProposal`) runs once per conference; Fixed posts never move.

## Look
- Brand guide colors: olive `#6C7255` (banner bg is `#6C7355`), burgundy `#541B1E`, rose `#A04C54`, cream `#E2DEC9`,
  terracotta `#A56F52`, light terracotta `#C08A61`. Headings use Domine (free stand-in for SuperClarendon).
- Top bar: `header-banner.png` (cropped from the newsletter signup banner), centered on olive.
- App icons (`icon-*.png`, `favicon.ico`): lamp emblem on olive.
- Default category colors: Speaker rose, Registration terracotta, Event olive, Inspiration dusty blue `#6F8A9A`,
  Fun mustard `#C9A043`; emails burgundy. Nicole can change any category's color in Settings → Categories (tap the dot):
  11 distinct swatches (`PALETTE`: olive, sage, burgundy, rose, light rose, terracotta, tan, mustard, dusty blue, plum,
  espresso; light ones get dark text via `inkFor()`) plus her own custom colors, stored as `colors`/`custom` in `ss_meta/cats`; `renderCatStyles()` applies them.
- Conference colors are set per conference in its sheet (`renderConfStyles()`).

## Speaker posts
3 speakers + 1 musical guest. Nicole renames the slots herself as speakers are confirmed.

## Working notes
- Test locally with Playwright (Chromium is preinstalled); Firestore isn't reachable from the cloud
  container, so check live behaviour in the user's browser when the session is linked to her Mac.
- Commit as Claude with Nicole's email; push to `main` (that publishes the site).
