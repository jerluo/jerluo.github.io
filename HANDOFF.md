# Handoff: deploy SEA trip planner to jerluo.github.io

## Context
This repo is a personal site served by GitHub Pages (user site, deploys from the
default branch automatically — no build step, no CI config needed). We're adding a
private-ish, self-contained trip-planner web app so it can be installed to an
iPhone home screen via Safari's Add to Home Screen.

The app is a single HTML file: `sea-trip-schedule.html`. It is fully
self-contained (inline CSS/JS; Leaflet + fonts from CDNs; no build, no framework).
All user data lives in the browser's localStorage — nothing is ever written to
this repo by the app. It already includes home-screen meta tags
(apple-mobile-web-app-capable, title "SEA Trip", theme-color #F6F8F7) and
`<meta name="robots" content="noindex, nofollow">`.

The user should have provided `sea-trip-schedule.html` (and optionally
`REVAMP-INSTRUCTIONS.md` + `sea-trip-plan-example.json`) alongside this handoff.
If they're missing, stop and ask for them.

## Task
1. Create a folder `trip/` at the repo root.
2. Move `sea-trip-schedule.html` into it as `trip/index.html` (rename — the URL
   should be clean: https://jerluo.github.io/trip/).
3. If `REVAMP-INSTRUCTIONS.md` and `sea-trip-plan-example.json` were provided,
   put them in `trip/` too — they're documentation for future edits, safe to
   host. Do NOT ever commit files matching `sea-trip-plan*.json` that look like
   real saves with personal data (booking refs, addresses in `acc` fields); the
   example file with placeholder data is fine.
4. Add a `.gitignore` entry as a guardrail:
   ```
   # personal trip-plan exports — never commit real saves
   trip/sea-trip-plan.json
   ```
5. Optional but nice — home-screen icon: generate a 180x180 PNG
   (`trip/apple-touch-icon.png`). Simple design: background #F6F8F7, a route
   motif of three connected dots/line segments in #0F6E56 → #534AB7 → #C24E24
   (the app's Vietnam/Laos/Thailand colors), rounded feel, no text. Then add
   inside `<head>` of trip/index.html:
   ```html
   <link rel="apple-touch-icon" href="apple-touch-icon.png">
   ```
   (Relative href is correct since the icon sits next to index.html.)
6. Verify before committing:
   - `trip/index.html` parses (open it with a quick local server, e.g.
     `python3 -m http.server`, and check: tabs switch, the schedule renders 18
     stops totalling 42 nights, no console errors on load. Map tile/font loads
     may fail offline — that's expected and fine.)
   - The `<head>` still contains the noindex meta and the apple-* metas.
7. Commit with a clear message and push to the default branch. Nothing else in
   the repo should be touched.

## After deploy (tell the user)
- URL: https://jerluo.github.io/trip/  (Pages can take ~1–10 min to update)
- iPhone install: open the URL **in Safari** → Share → Add to Home Screen.
- Their data stays on-device in localStorage; the Export button in the app is
  their backup mechanism (exports go to the Files app on iOS).
- To update the app later: replace trip/index.html and push. If the phone shows
  a stale version, wait ~10 min for the Pages cache, then force-quit and reopen.

## Do not
- Do not rename, minify, split, or "clean up" the HTML — single-file is the
  design. Formatting churn makes future diffs (edited by other Claude instances
  against REVAMP-INSTRUCTIONS.md) painful.
- Do not change `const STORE_KEY = "sea-trip-plan-v2"` — bumping it wipes the
  user's saved plan.
- Do not add analytics, service workers, or a manifest — iOS uses the apple-*
  meta tags already present, and a service worker adds cache-invalidation pain
  for zero benefit here.
