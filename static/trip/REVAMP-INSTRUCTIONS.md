# SEA Trip Planner — revamp instructions for a future Claude

You've been given `sea-trip-schedule.html` (a self-contained trip planner) and possibly
a `sea-trip-plan.json` (an exported save). The user wants to revamp the plan through you
rather than through the UI. Here's everything you need.

## The data format

The plan is ONE flat JSON array that strictly alternates stop and leg objects:

```
[ STOP, LEG, STOP, LEG, STOP, ..., STOP ]
```

**Invariants — the UI breaks if these are violated:**
1. First and last elements are STOPs.
2. Exactly one LEG between every pair of consecutive STOPs. Never two legs in a row,
   never two stops in a row.
3. Dates are NOT stored anywhere. They are computed at render time by walking the
   array from the trip start date, adding each stop's `n` (nights). To move a stop's
   dates, change the nights of stops before it or reorder the array.

**STOP object:**
| key | type | required | meaning |
|---|---|---|---|
| name | string | yes | display name |
| c | "vn" \| "la" \| "th" | yes | country (controls color + totals) |
| n | integer ≥ 1 | yes | nights |
| lat, lng | number | yes | map position |
| note | string | no | free text, shows in Details "Notes" |
| acc | string | no | Details "Accommodation" (multiline via \n) |
| tours | string | no | Details "Tours" |
| todo | string | no | Details "Things to do" |
| lock | true | no | hides +/−/×; use for unskippable transit/overnights |
| transit | true | no | stop is drawn in the route line but gets no map marker |

**LEG object:**
| key | type | required | meaning |
|---|---|---|---|
| leg | string | yes | free text ("2.5h", "via Lai Chau · 8–10h"). May contain HTML entities like `&middot;` `&rarr;` `&ndash;` — they render as ·, →, – |
| mode | "bus" \| "train" \| "boat" \| "fly" \| "other" | yes | "fly" draws dashed on the map |
| review | true | no | shows an amber "route changed · check this" badge |

The validator (`validPlan` in the HTML) requires: every element either has a string
`leg`, or has `name` + numeric `n` + numeric `lat`/`lng` + a valid `c`. Import of a
file that fails this silently rejects with "Couldn't read that file".

## Two ways to deliver a revamp

### Option A — new save file (non-destructive, usually right)
Edit or regenerate the JSON array and give the user a `.json` file. They tap
"Import file" in the app, review it, then "Save as my plan". The baked-in original
stays untouched as a fallback. Use this for itinerary changes on the same trip.

### Option B — bake a new base plan into the HTML (a true revamp)
Edit `sea-trip-schedule.html` directly:

1. Find `const original = [` in the `<script>` block. Replace the array contents
   with the new plan (JS object literals, same schema; keep HTML entities in `leg`
   strings if you want the typographic dots/arrows).
2. If trip dates changed, update:
   - `const START = new Date(YYYY, M, D);` — **month is 0-indexed** (7 = August).
   - `const BUDGET = <total nights>;` — nights from landing to departure day.
   - The header: the `<h1>` (e.g. "Hanoi → Bangkok"), the `.eyebrow` line, and the
     `.daterange` line are hardcoded text — update them to match.
   - The Lao-embassy / Ha Giang / Dien Bien Phu sentence in the `<footer>` if those
     constraints no longer apply.
3. Sanity-check: sum of all stops' `n` should equal `BUDGET` (the UI shows a red
   warning otherwise — fine as a draft state, but a baked base plan should be green).
4. localStorage: the save key is `const STORE_KEY = "sea-trip-plan-v2"`.
   - Schema unchanged (just different stops/nights)? **Keep the key.** The user's
     old save survives; "Restore original" now returns the new base.
   - Schema changed (new required fields, new countries)? **Bump to v3** so stale
     incompatible saves are ignored — and warn the user their in-browser save will
     reset (tell them to Export first!).

## If the revamp adds a country beyond vn/la/th
Non-trivial; touch all of these in the HTML:
- `HEX` (marker/route colors), `C` (name + CSS-var refs), and add matching
  `--xx`, `--xx-mid`, `--xx-soft` CSS variables in `:root`.
- The per-country stat cards in the header markup (`n-vn` etc.) and the
  `["vn","la","th"]` loops in `render()`.
- The map legend markup, the `<option>` list in the add-stop form,
  `countrycodes=vn,la,th` in the Nominatim search URL, and the country-guess
  heuristic in `placePick()` (it's a crude lng/lat split — replace, don't extend).
- The country-code sniffing in `pickResult()`.

## Gotchas
- Stops are addressed by array index everywhere (onclick handlers, `selectedStop`,
  the "insert after" logic). This is safe because every edit re-renders, but it means
  a hand-edited plan must preserve the strict alternation or indices go nowhere.
- Two stops may share a name and coordinates (Luang Prabang appears twice) — that's
  fine and intentional for backtracking routes.
- `transit` stops still consume a night and appear in the schedule; they're only
  hidden from map markers. The route polyline deliberately passes through them
  (e.g. Phong Nha → Hanoi → Ha Giang).
- Inserting a stop between A and B must produce `A, newLeg, X, oldLeg, B` and the
  oldLeg should get `review: true` — never assume what the displaced transport is.
- The app never geocodes baked-in stops; every stop needs real `lat`/`lng` or the
  map draws it in the Gulf of Guinea at (0,0)-ish. Nominatim
  (`https://nominatim.openstreetmap.org/search?format=jsonv2&q=...`) is what the
  in-app search uses if you need coordinates.
- Text placed into `leg`/`note` fields is injected as HTML in the schedule view
  (entities render), but Details textareas and map popups are escaped. Keep any
  user-provided text free of raw `<` `>` in leg/note to be safe.

## Quick validation snippet
Run against a candidate plan before delivering:

```javascript
const p = require("./sea-trip-plan.json");
let ok = p.length && p[0].name && p[p.length-1].name;
for (let i = 0; i < p.length - 1; i++) {
  const a = "leg" in p[i], b = "leg" in p[i+1];
  if (a === b) { ok = false; console.log("alternation broken at", i); }
}
const nights = p.filter(x => x.name).reduce((s, x) => s + x.n, 0);
console.log({ok, nights}); // nights should equal BUDGET
```
