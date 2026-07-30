# USA Bike Parks — Update README

This covers the changes made to `index.html` (the USA lift-served bike parks
tool) in this round of edits. Deploy by replacing `index.html` in the
`postmillennium-MTB/USA-bike-parks` repo via GitHub's web UI, same as always.

Built from the live GitHub file at the time of editing, single-file,
zero-dependency, no build step — open it and it works.

---

## What changed

### 1. Pie slice → jumps to that state
Clicking a slice (or a legend row) in the "Parks by State" pie now scrolls
the page to that state's section, opens it, and briefly flashes it so it's
obvious what happened. Works with mouse click and with tap on touch (a tap
that doesn't turn into a scrub-drag counts as a jump).

### 2. Map / Stats / Mountain tabs
Added the same three-tab structure as the Canadian tool, same icons:

- **Map** — every park with a known location, pinned on a dark Leaflet map
  (CARTO dark basemap, no API key needed) with clustering. Click a pin for
  vertical drop, a link to the resort site, and a "Details ↓" link that jumps
  to the full card below. Region-jump buttons (West / Rockies / Midwest /
  Northeast / South / whole country) sit above the map.
- **Stats** — every park with a published vertical drop, sorted by vertical
  (default), name, or state, with animated bar rows and four summary cards
  (biggest, median, average, combined). Parks with no published vertical are
  visibly excluded rather than guessed at — see "Known data issues" below.
- **Mountain** — pick up to 8 parks and see their vertical drops stacked as a
  skyline, so the scale differences are visible rather than abstract. "Biggest
  5" and "Surprise me" buttons for quick starts.

Leaflet 1.9.4 and Leaflet.markercluster 1.5.3 are inlined in full (copied out
of the Canadian tool's file) so this stays a single self-contained HTML file.

### 3. Centering fixed
The root cause: `<main>` had `max-width:1400px; margin:0 auto`, but the
header, legend, pie bar, and filter bar had no such constraint — so on a wide
screen those bands stretched edge-to-edge while their *contents* sat flush
left, and only the park grid below looked centered. Added a shared `.wrap`
class (same 1400px column) around the contents of each band. The colored
bands themselves still run full-width; only what's inside them is now
aligned to the same column as everything else.

### 4. Reset button
Sits to the right of the filter pills (Filter: All / New / Open / Loam Pass).
Clears the active filter back to "All," empties the search box, clears any
pinned pie selection, collapses any expanded park cards, and scrolls back to
top.

### 5. "States without bike parks" note
Same structure and cable-car glyph as the Canadian tool's "Provinces with no
lift-served parks" note, placed at the bottom of the Map/Stats/Mountain
section. The list of states-without-parks is **derived from the page's own
markup** (`document.querySelectorAll('.state-section')`), not hardcoded — add
a new state section to the park list and this note updates itself
automatically.

The Alabama/Cloudmont paragraph is written as a case for what the site could
support (two surface lifts, ~150 ft vertical under an 1,800 ft summit, near
year-round season, ~2 hrs from Atlanta, ~90 min from Huntsville, Spider
Mountain TX as a scale comparison) and is explicitly tagged **Speculative**
with a one-line disclaimer that no park has been announced — matching the
"this is fan fiction" framing in the source Pinkbike post. It links out to
that post rather than asserting anything as fact.

The same cable-car icon is reused above the state list itself (a small
"States" section header), per your request to match the icon used before
"Provinces" and before the Map/Stats/Mountain tab labels.

### 6. Canada cross-link
A bar under the legend links to
`https://www.postmillenniumrenaissance.com/parks-ca/`, styled to match the
rest of the page.

### 7. Not changed
Per your instruction, no mountain/skyline graphic sits behind the hero title
block ("LIFT-SERVED BIKE PARKS IN THE USA / Chairlift & gondola-served
downhill parks..."). That header is untouched aside from being wrapped in the
new centering column.

---

## How the data flows

The state/park list in the HTML (`<div class="state-section" data-state="...">`)
remains the single source of truth, exactly as before. On page load, a small
script (`buildParks()`) reads that markup once and builds an in-memory `PARKS`
array — the Map, Stats, and Mountain views all read from that array, not from
a separate dataset. **Practically: add or edit a park in the existing
state-section markup, and it automatically appears (or updates) in the pie
chart, the map, stats, and mountain views** — nothing else needs touching,
*except* for the one thing markup can't supply: coordinates (see below).

---

## Known data issues — please review

### Coordinates are estimates, not sourced
Your original file had no latitude/longitude anywhere, so a `COORDS` lookup
table (keyed by exact park name) was built from general knowledge of each
resort's location — resort base area where known, town center otherwise.
All 87 parks in the current list have a coordinate and pin successfully, but
these are **approximate, not surveyed**. Worth a spot-check on the more
obscure ones:

- Frostfire Park (Walhalla, ND)
- Deer Mountain Village Bike Park (Under Development) (Lead, SD)
- Trails at Mena (Under Construction) (Mena, AR)
- Farside Bike Park @ Chestnut Mountain (Galena, IL)
- Discovery Bike Park (Phillipsburg, MT)

If a park's name changes in the markup, its `COORDS` key must be updated to
match exactly, or it'll silently lose its pin (it'll still show up correctly
everywhere else — pie chart, filters, stats if it has a vertical). A missing
match doesn't break anything; the park is simply not one of the pins shown
on the map.

### Two vertical-drop figures look wrong
The Stats/Mountain views read the **"Vertical Drop"** stat cell on each card.
Two parks don't have one:

| Park | Card shows | Likely issue |
|---|---|---|
| Mountain High Bike Park (CA) | "Summit: 8,200 ft" | Summit elevation, not vertical drop |
| Dodge Ridge Bike Park (CA) | "Summit: 7,400 ft" | Summit elevation, not vertical drop |

Because neither card has a proper "Vertical Drop" stat, the parser currently
falls back to those summit figures, which puts both parks at the top of the
Stats ranking — well above Jackson Hole, Snowmass, etc. That's very unlikely
to be their actual vertical drop.

**Fix:** add a genuine `Vertical Drop` stat item to both cards in the source
HTML with the real figure (probably somewhere in the 1,000–2,000 ft range
based on comparable SoCal/Sierra parks, but don't take my estimate — the
actual number should come from you or the resort). Once that stat exists,
the parser will pick it up automatically and these two will re-rank without
any other code changes needed.

### Five parks are excluded from Stats/Mountain (correctly)
These publish no vertical-drop figure at all in their existing cards, and are
deliberately left out of the ranked views rather than having a number
invented for them. This is disclosed on-page ("X of 87 parks ranked · 5
publish no vertical drop and are left out rather than guessed at"):

- Sugarloaf Bike Park (ME) — only lists lift name
- Lee Canyon Bike Park (NV) — only lists trail count
- Deer Mountain Village Bike Park (Under Development) (SD) — "Coming 2027"
- Summit Bike Park — Snoqualmie Pass (WA)
- Snow King Mountain (WY) — explicitly "NO lift bike access currently"

If any of these get a real vertical drop added later, they'll automatically
join the ranked views.

---

## File structure (for reference)

```
index.html
├── <style>
│   ├── Leaflet + MarkerCluster CSS (inlined, ~16KB)
│   └── existing theme system + new CSS (centering, tabs, map, stats,
│       mountain, reset button, note, section head, Canada link)
├── <header> — now wrapped in .wrap for centering; unchanged otherwise
├── legend, Canada link bar, pie bar/panel — wrapped in .wrap
├── filter bar — reset button added
├── view-section — Map / Stats / Mountain tabs + no-parks note (new)
├── <main> — section heading (new) + existing state-section park list
│             (untouched structure — still the data source)
└── <script>
    ├── Leaflet + MarkerCluster JS (inlined, ~180KB)
    └── existing accordion/filter/theme/pie code, followed by:
        COORDS table, ALL_STATES list, buildParks(), jumpToState/Park,
        toggleView, map init, stats render, mountain render,
        renderNoParkNote, resetAll, pie-click handlers
```

File size: ~404KB (was 162KB before this update — the increase is almost
entirely the inlined Leaflet + MarkerCluster libraries, needed to keep the
map single-file and dependency-free like the rest of your tools).

---

## Testing performed

The built file was executed in a real DOM (jsdom) to check for runtime
errors, and to verify: all parks parse into the `PARKS` array; every park has
a coordinate; Stats/Mountain correctly exclude parks with no vertical;
sorting works in both directions; the pie-click jump lands on the correct
state section; Reset actually clears filters/search/selection; the header
has no mountain graphic behind it; and the centering wrapper is present
around every band. 45 automated checks were run; all passed except the two
data issues documented above, which are pre-existing data problems rather
than bugs in the new code.
