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
Canada doesn't have this repo's `data-status` pattern yet and was pointed
here to borrow it), check whether the Canada repo has the same problem or
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

Unlike the Canada repo, this rule isn't stated anywhere in this tool's own
UI copy — it's enforced only by which rows exist, nowhere written down
until now. When vetting a "new park" candidate (see Recurring maintenance
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
  Canada's sibling repo could not get the same treatment yet: there's no
  verified-real (not pre-projected-for-display) province boundary dataset
  reachable from a standard sandboxed session — check that repo's own
  CLAUDE.md before assuming otherwise.
- Park data lives in the HTML, not a data array — a future refactor to a
  `PARKS` array literal (matching the Canada repo's pattern) would make
  bulk edits and consistency checks easier, but that's a real structural
  change under the PMR standard's "ask before restructuring" rule, not
  something to do incidentally while fixing one park's entry.
