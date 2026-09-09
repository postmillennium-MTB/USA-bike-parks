# US Lift-Served Bike Parks

A single-file interactive guide to every lift-served mountain bike park in
the United States. One `index.html` — no build step, no dependencies beyond
Leaflet (inlined) and Google Fonts (CDN) — embedded via iframe on
postmillenniumrenaissance.com and in Pinkbike articles. Read
`.claude/skills/pmr-build-standard` (if present) or the equivalent PMR
build-standard skill before making structural changes: one file, zero
unnecessary dependencies, one contiguous data block, registry pattern for
anything that repeats, mobile-first, iframe-safe.

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

- **CARTO tiles require an API key now.** The map used to use CARTO's free
  dark raster basemap (`basemaps.cartocdn.com/dark_all/...`). CARTO gated
  that behind an account API key at some point, and the failure is sneaky:
  an unauthenticated request comes back **HTTP 200**, not an error, with
  "API KEY REQUIRED" burned directly into the tile image — so it looked
  like a rendering bug, not a dead tile source. Fixed by switching to
  OpenStreetMap's standard tile server (free, no key, no account). OSM
  only ships light cartography, so the permanent dark map here now comes
  from a CSS filter (`invert(1) hue-rotate(180deg) brightness(0.92)
  contrast(0.88) saturate(0.65)`) applied to Leaflet's tile pane, tuned by
  eye rather than pixel-verified — nudge the numbers if the map ever looks
  off, no logic change needed. If CARTO ever becomes genuinely free again,
  that's a reason to *reconsider*, not a reason to assume the old code was
  fine — check the actual tile response before reverting.
- Park data lives in the HTML, not a data array — a future refactor to a
  `PARKS` array literal (matching the Canada repo's pattern) would make
  bulk edits and consistency checks easier, but that's a real structural
  change under the PMR standard's "ask before restructuring" rule, not
  something to do incidentally while fixing one park's entry.
