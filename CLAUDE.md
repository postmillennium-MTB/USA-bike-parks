# US Lift-Served Bike Parks

A single-file interactive guide to every lift-served mountain bike park in
the United States. One `index.html` — no build step, no dependencies beyond
Leaflet (inlined) and Google Fonts (CDN) — embedded via iframe on
postmillenniumrenaissance.com and in Pinkbike articles. Read
`.claude/skills/pmr-build-standard` (if present) or the equivalent PMR
build-standard skill before making structural changes: one file, zero
unnecessary dependencies, one contiguous data block, registry pattern for
anything that repeats, mobile-first, iframe-safe.

## Sibling tool

This is one of two twin PMR bike-park guides by the same author, sharing
the same build standard and the same class of structural bugs but **not**
the same data schema:
- **This repo (USA):** English only, park data is authored directly in
  the HTML as `.park-row` elements, not an array.
- **Canada — `postmillennium-MTB/Canada-mtb-parks`:** bilingual (EN/FR),
  park data is a JS array literal (`const PARKS = [...]`) — see that
  repo's own `CLAUDE.md` for its badge/theme registries.

If you're fixing a structural/dependency bug (the CARTO tile-provider
issue below is exactly this kind — it hit both repos identically) or
introducing a new maintenance convention (e.g. how closures are tracked —
Canada now has a `closed` badge modelled on this repo's `data-status` pattern), check whether the Canada repo has the same problem or
would benefit from the same fix. The two repos share no code, so nothing
here propagates there automatically. That repo isn't attached to your
session by default — use `add_repo` (or ask Jon) before assuming its
current state.

## Scope: what counts as a "bike park" here

"Bike park" is an overloaded term in the industry. Many facilities that
call themselves a "bike park" are pump tracks, dirt-jump lines, or skills
areas with no real descent — Frisco Bike Park in Frisco, CO (pump track +
jump lines, no lift) is the canonical example. This guide's definition is
narrower:

> A "bike park" here means **lift-access, downhill-oriented mountain bike
> riding** — a chairlift, gondola, or similar fixed lift carries riders
> (and usually bikes) uphill so they can ride trails back down.

Explicitly excluded, even when the operator's own marketing says "bike
park":
- Pump tracks, dirt jump lines, skills parks, BMX-style facilities — no
  lift, no real vertical descent.
- **Shuttle access** — a truck or van with bike racks driving riders to a
  trailhead. No fixed lift, so it doesn't qualify no matter how good the
  descent is. (A park that's mostly lift-served but uses a shuttle for one
  lower section — e.g. the Schweitzer row's "Chairlift + lower shuttle" —
  is still in-scope; the test is whether the *primary* access is a lift.)

Like the Canada repo, this rule is now stated in the page footer (the footer
copy is deliberately identical in both guides). When vetting a "new park" candidate (see Recurring maintenance
below), apply it explicitly: the test is always "is there a lift," never
"does the operator call it a bike park."

## Who you're working with

Jon (repo owner) has no coding background and edits through GitHub's web UI,
not git. That means:
- Deliver complete files, not diffs/patches — he pastes whole files.
- Never invent park data. A gap ("trail miles unknown") is honest; a guessed
  number isn't. If you can't verify something, say so and leave it out or
  flag it, don't fill it in.
- Ask before restructuring anything beyond the data rows — a refactor means
  he has to re-paste and re-verify the whole file.

## Where things live

Unlike the sibling Canada repo, park data here is **not** a JS array — it's
authored directly in the HTML as `.park-row` blocks inside `.state-section`
containers (search for `data-state="` to find a state, `class="park-row"`
for an individual park). `PARKS` in the script is built by parsing that
markup at runtime (search `row.dataset.status`) — so to add, edit, or
retire a park, edit the HTML row, not a data array. Each row carries:
- `data-status="open" | "coming" | "closed"` — the field this file already
  has for exactly the maintenance work described below.
- `data-new="true"` — "new" ribbon (opened/announced recently).
- `data-loam="true"` — on the Loam Pass multi-resort pass.
- Inside `.park-details`, a `.park-stats-grid` of label/value pairs
  (Vertical Drop, Trail Miles, Lifts, Season, etc. — whatever's relevant to
  that park) and a `.park-note` sentence of free-text context.
- `.park-tags` holds the visible pills (`tag-lift`, `tag-new` "Coming" or
  "New", `tag-closed` "Closed").

## Recurring maintenance: park openings & closures

This is a living guide of a fast-moving industry. When asked to update this
tool, or periodically on your own initiative, check both directions:

**New parks (opened or announced).**
- Apply the Scope definition above first — a pump track, skills park, or
  shuttle-access operation calling itself a "bike park" doesn't belong
  here even if it's popular or newly opened.
- Sources: the resort's own site/press release, Trailforks region pages,
  Pinkbike/Bike Magazine/Freehub news coverage, local news for the region,
  NSAA (National Ski Areas Association) reporting, and Jon's own industry
  contacts — see `git log --grep=Bluewood` for the established precedent:
  Bluewood was added as under-construction, then corrected with confirmed
  specs sourced from a direct GM letter, and both steps were named in the
  commit message.
- Not yet open but announced/under construction → add a full `.park-row`
  with `data-status="coming" data-new="true"`, a `tag-new` pill reading
  "Coming," and stats that say what's confirmed (est. opening year,
  acreage, investment, lift manufacturer if known) rather than guessing
  anything unconfirmed — mirror the "Trails at Mena" row as a template.
- Once a `coming` park actually opens: flip `data-status` to `"open"`,
  swap the "Coming" tag for "New" (keep `data-new="true"` for a season),
  and replace estimated stats with confirmed ones from a primary source —
  cite it in the commit message.

**Temporary closures** (a season skipped for financial, lift-mechanical,
wildfire/flood, or ownership-transition reasons — not normal off-season,
which is just what the `Season` stat already says).
**Permanent closures** (the resort shut the bike operation down for good).
- The exact pattern already exists — use it as-is, don't invent a new one.
  Mirror the Telluride Bike Park row (search `Telluride Bike Park`):
  add the `closed` class to `.park-row` (`class="park-row closed"`), set
  `data-status="closed"`, swap the tag pill to `tag-closed` "Closed," add
  a "Status" stat ("Closed 2026") and, if a return is expected, a
  "Reopens" stat, and write a one-line `.park-note` explaining why and
  when it's expected back. If the closure looks permanent, say so plainly
  in the note instead of implying a reopening that isn't real.
- Keep closed parks in the list rather than deleting them — the row's own
  dimmed styling (`.park-row.closed`, ~65% opacity) already exists so a
  closed park stays visible as historical/reference context without
  looking active. Removal is Jon's call, not a default.
- Update the relevant `.state-header`'s `.state-count` (and
  `.state-loam-count` if it changes) to match — these are hand-authored
  counts, not auto-derived, so they drift silently if you forget.
- Cite the source in the commit message (same style as the Bluewood and
  Telluride commits), and update README.md's park-count language if the
  national total shifts enough to matter.
- **Then run the park sync** (see "Park sync between the two guides"): any add, close,
  reopen, rename or move changes what the other guide's road trips show. If you can't run it,
  end the handover with the reminder line given there.

## Gotchas (recorded so nobody re-introduces them)

- **The map has no tile server at all now — this is the second basemap
  rewrite, and the reason there won't need to be a third.** Originally used
  CARTO's free dark raster basemap, which CARTO later gated behind an
  account API key (an unauthenticated request came back HTTP 200 with "API
  KEY REQUIRED" burned into the tile image, not an error — looked like a
  rendering bug). Swapped to OpenStreetMap's tile server as a stopgap, but
  that carries the same risk one layer down: OSM's own usage policy says
  that server isn't meant for embedded production use at any real traffic,
  so it was only ever a matter of time before something broke again. Fixed
  for good by dropping raster tiles entirely — `US_STATES_GEO` (search for
  it, right above the `MAP` section) is real WGS84 state-boundary polygons,
  converted once from the `us-atlas` npm package (public-domain Census
  Bureau data, ISC-licensed tooling) and simplified down to ~52KB, baked
  directly into this file. Leaflet renders it as an ordinary GeoJSON layer,
  the exact same way it already renders park markers from real lat/lng —
  no server to ever fetch from, ever, so there's no pricing/rate-limit
  cliff left to fall off. Fill/stroke come from `.us-state-shape` in CSS,
  reading the page's own `--card-bg`/`--card-border` custom properties, so
  it re-themes on every theme-switcher click for free, with no JS
  re-render. `vector-effect: non-scaling-stroke` on that rule keeps borders
  a crisp 1px at every zoom level and every container size — without it,
  state borders visually disappear when the SVG is scaled down (found this
  the hard way building the preview; it's not optional).
  Previous (OSM tile) version is preserved at commit
  `483fc82cea02ea4a708feed5b940160bb827119e` if this ever needs reverting —
  `git show 483fc82:index.html > index.html` restores it wholesale, or
  `git diff 483fc82 HEAD -- index.html` shows exactly what changed.
  The Canada guide got the same treatment in November 2026 (Natural Earth 1:50m provinces,
  `CA_LAND_GEO`), so neither guide uses a tile server now.
- **Road-trip mode (Map tab) runs on a baked-in highway graph, `NA_ROADS`** — search
  `MAP DATA — US + Canada highway network`; the engine is `rtSnap` / `rtParksNear` and the UI is the
  `trip*` functions right after it. Same no-server rule as `US_STATES_GEO`: don't swap it
  for a routing API (OSRM, Mapbox, Google), which would bring back the dependency risk above.
  The data is Natural Earth 1:10m roads, rebuilt by `tools/build-road-network.py`. Natural Earth
  has real gaps: I-80 stops ~26 km short in Utah, and the builder's gap-closing step exists
  for that. If you rebuild, re-run a long east–west and a Wasatch route to check
  connectivity. Trip pins read `TRIP_PARKS` (`MAPPED` plus the Canada snapshot), so a new US park
  appears in trip results as soon as it has `COORDS`. The US map also draws `CA_OUTLINE_GEO`
  (Canada, Natural Earth 1:50m, orientation only) behind the states. Do not invent drive times or road-mile precision: the page says ±10%.
  The engine (`rt*` functions) is a **byte-for-byte twin** of the Canada guide's: fix it in
  one, make the same change in the other. `rtClass` is shared too; only the `trip*` UI is per-guide.
  **Cross-border trips (November 2026).** Both guides route on one network, `NA_ROADS` (search
  `MAP DATA — US + Canada highway network`): US interstates, US routes and Alaska's highways plus
  every Canadian road class and ferries, built together by `tools/build-road-network.py ... 0.01
  4 250 NA` so the border crossings connect. It is byte-identical in both pages, as are the engine
  and `rtClass` (one set of road costs). Each guide also carries a snapshot of the OTHER guide's
  parks, `OTHER_PARKS` (between the `OTHER_PARKS:BEGIN` / `OTHER_PARKS:END` markers), so trips
  list, pin and route through parks across the border, tagged with their country and linked to
  their own guide. Each guide stays the only place its own parks are edited. **Keeping that
  snapshot current is the park sync: see "Park sync between the two guides" below, and follow
  it exactly.**
  **Park access roads (November 2026).** Natural Earth stops short of many resorts (CO-135 to
  Crested Butte and US-26 over Mt. Hood aren't in it), so `NA_ROADS` also holds ~50 access
  roads from OpenStreetMap: `tools/park-access.geojson`, fetched once from the public OSRM
  router by `tools/fetch-park-access.py` for every park more than 15 km from the network, up to
  three roads per park in different directions, and committed. The page fetches nothing; the
  roads are baked in like the rest. ODbL requires the "(c) OpenStreetMap contributors" credit
  (USA: footer credit line; Canada: map attribution): keep it while these roads are in the data.
  Every park was within 14 km of a routable road after this change; the park sync check flags
  any new or moved park that isn't.
  `rtSnapTrip` returns a route or `{ error: 'short' | 'noroad' | 'disconnected' | 'none' }`;
  sharp corners (>= 60 deg, see `rtCorners`) are must-visit points. Alaska's state highways
  are in `NA_ROADS` (named AK-n); Alaska has no US routes.
  A drawn end that runs past the roads (border, sea) is trimmed back to where the line leaves the
  road network, so a trip stops at the crossing the line used. Trip ends ignore road fragments
  under 300 km (`compKm`). Colour check for trip mode: `node tools/check-trip-colours.js` (needs
  Playwright). It measures the rendered colours in every theme and print, and the same file is
  in both repos.
  "Add to my trip": after a route is drawn every other park stays on the map as a tappable dot
  (`.trip-ghost-live`); ticking "Stop" in the list, or Add / Remove in a pin or dot popup, re-plans
  the trip through that park (`rtSnapTrip(G, sketch, vias)`; vias slot into the sketch in travel
  order and become must-visit points) and makes it a Google Maps stop (max 9). A park you added
  stays listed even if its road is outside the band.
  Whole-line coverage: after planning, the drawn point the route strays farthest from becomes a
  must-visit point when that gap is over 8% of the line (100–250 km) and a road is within 40 km;
  repeats up to 8 times. This is what makes a narrow loop (down the coast, back up the Rockies)
  get driven instead of cut across. Ordinary drawings never reach the threshold.
  **Open idea, not scheduled:** Jon wants to keep the option of moving road-trip mode
  to its own Routes tab later (see README, "Open idea: a separate Routes tab"). Don't do it
  incidentally; it's a layout change under the ask-first rule.
- Park data lives in the HTML, not a data array — a future refactor to a
  `PARKS` array literal (matching the Canada repo's pattern) would make
  bulk edits and consistency checks easier, but that's a real structural
  change under the PMR standard's "ask before restructuring" rule, not
  something to do incidentally while fixing one park's entry.

## Park sync between the two guides (required; follow this exactly)

The two guides depend on each other. Each page carries a snapshot of the OTHER guide's parks
(`OTHER_PARKS`) so road trips can list, pin and route through parks across the border. The
snapshot is only refreshed by running the sync, so after a park is added, removed, closed or
reopened, renamed, moved or has its vertical changed in either guide, the other guide's road
trips keep showing the old details until someone runs it. Use the steps below. Don't improvise
or explore: the commands do the comparing, and you never need to open either `index.html` to sync.

**Reminder rule.** Whenever a session changes the park list in either guide (any of the edits
above), run the sync as the last step of that same change if both repos are in the session
(steps 1-5). If the other repo isn't attached and can't be added, end the handover to Jon with
this line, word for word:
> A park sync is recommended: the other guide's road trips still show the old details for
> <park(s)>. Ask any Claude session to "run the park sync".

**When Jon asks for a sync ("run the park sync", "sync the guides"):**
1. Both repos must sit side by side (`../USA-bike-parks`, `../Canada-mtb-parks`). If one is
   missing, attach it with `add_repo` (`postmillennium-MTB/USA-bike-parks` or
   `postmillennium-MTB/Canada-mtb-parks`) and clone it next to the other on the same branch.
   Playwright is pre-installed in cloud sessions; don't install it.
2. From either repo root, run the read-only check (it writes nothing):
   `node tools/sync-other-parks.js --check ../USA-bike-parks ../Canada-mtb-parks`
   It prints, for each guide, when its snapshot was last synced and every park added, removed
   or changed since then (with the field that changed), or "up to date".
3. Report to Jon: the last sync date for each direction, and the differences, copied from the
   output.
4. **If both say "up to date": take no action.** No files change and there's no commit. Report
   that and ask Jon whether more review is warranted. A check for real-world park openings and
   closures is the separate "Recurring maintenance" task above, not part of a sync, and costs
   far more (web research), so only do it if he says yes.
5. **If there are differences:** run the same command without `--check`. It rewrites only the
   snapshot(s) that are out of date. Commit in each repo that changed, with a message naming the
   parks (e.g. "Sync OTHER_PARKS snapshot: Telluride, Powder Mountain reopened"), and push to
   the session's branch.
6. **Only if the check printed `FAR FROM ROADS`** for a park (new or moved, more than 15 km from
   the road-trip network): it needs an access road, or trips can't end there. Ask Jon before
   doing this, because it rebuilds the road data in both pages. Run these from the USA repo root
   (the scratch folder can be any temp folder):
   - download and unzip Natural Earth roads: `https://naciscdn.org/naturalearth/10m/cultural/ne_10m_roads.zip`
     (`pip install pyshp shapely` if needed)
   - `python3 tools/build-road-network.py <ne>/ne_10m_roads <tmp>/roads-base.json 0.01 4 250 NA`
   - `python3 tools/fetch-park-access.py <tmp>/roads-base.json index.html ../Canada-mtb-parks/index.html tools/park-access.geojson`
     (keeps every existing access road and only fetches the new far parks)
   - `python3 tools/build-road-network.py <ne>/ne_10m_roads <tmp>/roads.json 0.01 4 250 NA tools/park-access.geojson`
   - `python3 tools/install-road-network.py <tmp>/roads.json index.html ../Canada-mtb-parks/index.html`
     (must print "identical in all pages")
   - copy `tools/park-access.geojson` to the Canada repo, update the km/KB totals in the
     comment above `NA_ROADS` in both pages if they moved, draw one trip to the new park in a
     browser, and commit both repos naming the park and "access road (OpenStreetMap)".
   Without the 7th argument `build-road-network.py` reproduces the base network exactly; with
   it, it reproduces the shipped `NA_ROADS` exactly. If either doesn't match, stop and report;
   don't push a mismatched road network.

## Mountain chart and page texture (shared with the Canada guide)

- **Mountain tab = `renderPeakChart`** (search `PEAK CHART`; styles are the
  `.pk-*` rules). It is a **twin copy**: the same block, byte-for-byte, sits in
  the Canada repo. Fix or tune it in one, make the same change in the other. Each
  repo only supplies a small "park → item" function (`drawMountainStage` /
  `drawMtnStage`) and a few `--pk-*` theme variables. It draws SVG at 1:1 pixel
  scale (never shrunk to fit) and picks a label style from the pixel width per
  peak: horizontal names (wide) → 90°-rotated names and values (rot) → no printed
  labels, hover/tap only (dense). Hover or keyboard focus floats the name over the
  peak; on touch, first tap shows it and a second tap removes the peak. A peak
  that was not on the previous render plays a one-off shimmer (`pk-fresh`).
- **Page texture = two layers from the PMR texture catalogue** (`postmillennium-MTB/pmr-website`
  → `textures/`), designed in the texture studio: `.texture` is `crowe-1884-14685` (US Design
  Patent 14,685, Eugene A. Crowe, 1884; genuine drawing) at 136px, strongest at the left edge
  and fading right; `.texture-2` is `us-d268339` (US Design Patent 268,339, line art) at 64px,
  strongest at the right edge and fading left. Both are the first children of `<body>`. Retint a
  theme by changing only `--tex-ink`, `--tex-opacity`, `--tex-2-opacity` (opacities are higher
  than the catalogue's 4–7% because each layer is faded and layer 2 is thin line art; at full
  strength they leave muted text at ≥ 5.9:1 and main text at ≥ 10:1). `position:absolute`, never
  fixed (Pinkbike embeds clip it). Both layers are hidden in the print palette. The Mountain
  chart backdrop reuses mask 1 at its own quieter strength (`--tex-opacity` scoped on
  `.pk-stage`). Both credit lines in the footer are required; 268,339's inventor and grant date
  are not verified in the catalogue, so cite it by number only until they are.
- `low-pile-carpet.css` (repo root of the **Canada** repo) is an unused, reusable
  procedural texture kit for small accents (cards, plates). Not wired into the
  page.

## Color schemes and contrast ("would this survive being printed?")

Standard for every theme, solved numerically against that theme's page and card
surfaces (not by eye) — keep it when retinting or adding a theme:
- `--chalk` ≥ 11.5:1, `--chalk-dim` ≥ 9:1, `--chalk-mute` ≥ 7:1 (solid replacement for
  the old `rgba(255,255,255,.3–.4)` greys), `--warn` ≥ 8:1, `--link` ≥ 7:1.
- The accent has two jobs, so two tokens: `--dirt` is the accent as a **fill** (white
  text on it ≥ 6:1, so tabs/pills/buttons stay legible), `--link` is the accent as
  **text, borders and chart strokes** on dark surfaces. Don't use `--dirt` as text.
- `--edge` (≥ 3:1) is for control borders (buttons, search, chips); `--card-border`
  (≥ 2.2:1) is for card outlines.
- No dimming text with `opacity`. Closed park rows are dimmed only to `.88` and get a
  dashed border — a cue that survives grayscale.
- Theme 142 (neon) keeps its bright fills but uses near-black text on them (≥ 5.6:1).
- `@media print` (search `PRINT / SAVE-AS-PDF PALETTE`) prints every theme on white
  with solved dark text, because browsers drop dark backgrounds when printing. No print
  button exists by design.
- Audit method: load each theme in a browser, walk every visible text node, composite
  its colour over the effective background, compare to 4.5 (3 for large text) and 7.
  Status at last run: zero text below 4.5:1 on any theme; body text ≥ 7:1; the only
  items under 7:1 are white-on-fill pills (6:1). Print: zero below 4.5:1.

## License and notice (do not remove)

This repo is licensed under PolyForm Noncommercial 1.0.0 (`LICENSE`). Every edit and rebuild must keep: the `Required Notice:` line at the top of `LICENSE`; the (c) / SPDX comment right after the doctype of each HTML page; and the visible `.pmr-license` footer on each page. Do not remove, reword or hide them. New pages get the same footer. Third-party material keeps its own license: never relicense it, and keep its notices. Jon holds the copyright and decides any license change.
