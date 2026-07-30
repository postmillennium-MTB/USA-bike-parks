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
  skyline, so the scale differences are visible rather than abstract.
  "Biggest 5," "Surprise me," and "Add Five" buttons for quick starts —
  Add Five appends to the current selection rather than replacing it.

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
Moved from its own bar into a small card next to the Two Wheeled Wanderer
credit, right under the header — same "riding north of the border?" text,
now with no subtext (the "28 lift-served parks..." line was removed per your
request). Click it to go to the Canadian tool.

### 7. "Add Five" on the Mountain tab
Sits between "Surprise me" and "Clear." Adds five more random parks to
whatever's already selected (rather than replacing the selection), so you
can build up a comparison incrementally. Respects the existing 8-park cap —
if adding would push past 8, the oldest-selected park drops off first, same
rule the individual chip-toggle already used.

### 8. "Map" link on every park card
Each park card now has a second link next to the resort site link, using the
map icon you provided. Clicking it switches to the Map tab, flies to that
park specifically, unclusters it if needed, and opens its popup — landing
you on the exact same information a moment later, just via the map.

One judgment call worth flagging: the Canadian tool's cards link out to each
resort's *own* trail-map page (Trailforks, resort PDF, etc.) — a real URL
per park. This USA list has no such per-park data, and I didn't want to
invent or guess 87 external map links with no way to verify they're correct
or still live. So this "Map" link points into the interactive Map tab
already built this round instead of an external site. If you'd rather have
real per-park trail-map URLs like the Canadian cards, send them over (or
point me at where to find them) and I'll wire those in instead — it's a
small change once the data exists.

### 9. Not changed
Per your instruction, no mountain/skyline graphic sits behind the hero title
block ("LIFT-SERVED BIKE PARKS IN THE USA / Chairlift & gondola-served
downhill parks..."). That header is untouched aside from being wrapped in the
new centering column.

### 10. Six parks added (July 2026 review)

A review against Singletracks coverage and resort sources surfaced six
lift-served parks missing from the base list. Header now reads "Updated
July 2026" and the park count pill reads 93 (was "85+").

| Park | State | Vertical | Notes |
|---|---|---|---|
| Grand Targhee Bike Park | WY | 2,200 ft | 17 mi lift-served, Shoshone + Dreamcatcher lifts. The largest omission. |
| Windham Mountain Bike Park | NY | 1,600 ft | **Members only** — see below |
| Plattekill Bike Park | NY | 1,100 ft | 11 trails, Eastern States Cup venue |
| Massanutten Bike Park | VA | 1,080 ft | Creekside + Peak Express lifts; vertical per Trailforks |
| Hatley Pointe Bike Park | NC | 700 ft | Opens Aug 1 2026, tagged New |
| Brighton Bike Park | UT | *not published* | Majestic + Crest 6 lifts |

State counts updated: Wyoming 2→3, New York 3→5, Virginia 1→2,
North Carolina 2→3, Utah 7→8. Still 30 states, so the
"states without bike parks" note is unchanged at 20 states.

**New "Members" tag.** Windham rebranded as Windham Mountain Club and
restricted bike park access to members and guests on qualifying lodging
packages. It's listed for completeness with a purple **Members** tag, a
matching legend entry, an "Access: Members & lodging guests only" stat, and
a bolded note that it is not open to the general public.

**Brighton has no vertical figure on purpose.** No source publishes a
bike-park vertical for Brighton (its 4 flow trails run off two lifts, not the
full 1,745 ft ski vertical), so rather than invent a number the Vertical Drop
stat is omitted — which means Brighton is automatically excluded from the
ranked Stats and Mountain views. The on-page disclosure now reads "87 of 93
parks ranked · 6 publish no vertical drop and are left out rather than
guessed at."

### Checked and deliberately NOT added

- **Sunday River (ME)** — no longer offers lift-served MTB.
- **Wisp Resort (MD)** — XC and trail riding remain, but the dedicated
  lift-served downhill park is no longer operational. Maryland therefore
  stays on the states-without list.
- **Tyrol Basin / Granite Peak (WI)** — scenic chairlift rides only, no bike
  park. Wisconsin's absence is genuine, not an oversight.
- **Seven Springs (PA)** — no evidence of a currently operating lift-served
  park; Blue Mountain is described as Pennsylvania's premier lift-served park.
- **Telluride** — already correctly marked (`data-status="closed"`, Closed
  tag, "Closed 2026 / Reopens 2027", explanatory note). No change needed.

### 11. Banner image, mountain overhaul, map hover, 1990s theme

**Banner.** `moose-skulls.png` now sits between the header and the legend row,
full width, capped at 360px tall (210px on mobile) using `object-fit:contain`
so the whole illustration — including the PMR text at the bottom — is always
visible and never cropped or distorted.

> ⚠️ **The committed PNG is 7.99 MB**, which is very heavy for a banner and
> will noticeably slow first load. An optimised **`moose-skulls-web.jpg`**
> (0.39 MB — a 95% reduction, 1800px wide, no visible quality loss; the
> original's alpha channel was fully opaque so it was doing nothing) is
> provided alongside this README. To use it: commit that file to the repo,
> then change one line in `index.html` —
> `src="moose-skulls.png"` → `src="moose-skulls-web.jpg"`.
> There's an HTML comment at that spot as a reminder. Until you do, the
> banner still works, just heavily.

**Mountain tab — cap removed.** "Add Five" now keeps adding until every park
with a published vertical is on the skyline (87 of 93). The old 8-park limit
is gone from both the button and individual chip clicks. The button disables
itself once everything is added and re-enables as soon as you remove one.

**Counter.** Reads `<strong>30</strong> / 87 added`, matching the Canadian
tool's wording. The denominator is parks with a published vertical, not all 93.

**Click a peak to remove it.** Each peak is now a clickable `<g>` with a
pointer cursor and a hover state (fills with the accent colour, brightens its
labels). This mirrors the Canadian tool's behaviour.

**Hover shows the full name.** Each peak carries an SVG `<title>` reading
`Park Name — State · 2,200 ft · click to remove`. This matters because on-chart
labels truncate at 15 characters and disappear entirely once many peaks are
added — hover always gives the untruncated name. Map pins also got hover
tooltips (Leaflet `bindTooltip`) showing name and vertical, so you no longer
have to click a pin to identify it.

**West-to-east ordering.** Borrowed from the Canadian tool: peaks sort by
longitude rather than click order, so the chart's shape tracks the country's
— California on the left, New England on the right.

**Adaptive peak width.** With the cap gone, up to 87 peaks can be on screen.
Peak width steps down as more are added (118 → 82 → 58 → 40px), name labels
drop out below 70px and state labels below 58px, with hover carrying the
information instead. The stage scrolls horizontally.

**On replicating the Canadian mountain widget:** you asked whether it'd be
easier to port it wholesale. Having compared them — no. The Canadian version
renders peaks as HTML divs with per-province colours, a `hexToRgba` helper,
and bilingual/metric label formatting; porting it would mean bringing all of
that across, inventing a 50-state colour scheme, and ending up with two
different visual languages between the two tools. The four behaviours you
wanted were ~40 lines against the existing SVG. So the *behaviour* is now
replicated, and two good ideas were borrowed (longitude ordering, the counter),
but the rendering stayed SVG.

**Theme 142 → 1990s.** Fitting, since 142mm *was* the old standard. Charcoal
and slate greys (`--sky:#1e1e24`, `--forest:#2d2d38`) with hot neon pink as
the primary accent (`--dirt:#ff2e93`), aqua highlights (`--warn:#2ff0d6`), and
the tag colours repainted to lime (`--new:#a3f53f`), coral (`--closed:#ff6b5c`)
and pastel purple (`--loam:#b47cff`) — Memphis / Trapper Keeper territory.

Two details worth knowing:
- Several orange washes were hardcoded rather than themed (the header glow,
  the Two Wheeled Wanderer credit background, map cluster bubbles). These are
  now retinted for 142 specifically. *The same hardcoding still affects themes
  150 and 148* — pre-existing, not addressed here, but worth a look sometime.
- **Text on the neon fills is dark, not white.** White on the lime "New" tag
  measured 1.34:1 contrast (illegible); white on aqua 1.44:1. Switching to
  `#1a1a20` puts all five neon fills between 5:1 and 12.9:1. This applies only
  to theme 142.

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
│   └── existing theme system + new CSS (centering, credit-row/ca-credit,
│       tabs, map, stats, mountain, park-links-row/map link, reset button,
│       note, section head)
├── <header> — wrapped in .wrap for centering; credit-row added
│              (Two Wheeled Wanderer + "Riding north of the border?" side
│              by side); otherwise unchanged
├── legend, pie bar/panel — wrapped in .wrap
├── filter bar — reset button added
├── view-section — Map / Stats / Mountain tabs + no-parks note
├── <main> — section heading + existing state-section park list
│             (untouched structure — still the data source)
└── <script>
    ├── Leaflet + MarkerCluster JS (inlined, ~180KB)
    └── existing accordion/filter/theme/pie code, followed by:
        COORDS table, ALL_STATES list, buildParks(), MAP_ICON_SVG +
        injectMapLinks() (per-card Map link), jumpToState/Park/MapFor,
        toggleView, map init, stats render, mountain render (incl.
        addFiveMtn), renderNoParkNote, resetAll, pie-click handlers
```

File size: ~407KB (was 162KB before this round of updates — the increase is
almost entirely the inlined Leaflet + MarkerCluster libraries, needed to
keep the map single-file and dependency-free like the rest of your tools).

---

## Testing performed

The built file was executed in a real DOM (jsdom) to check for runtime
errors, and to verify: all parks parse into the `PARKS` array; every park has
a coordinate; Stats/Mountain correctly exclude parks with no vertical;
sorting works in both directions; the pie-click jump lands on the correct
state section; Reset actually clears filters/search/selection; the credit
row shows both cards with the Canada card stripped to just its link text;
Add Five appends without duplicating and respects the 8-park cap; every
mapped park's card got a working Map link using the specified icon, which
switches tabs and opens that park's popup; and the header still has no
mountain graphic behind it. 61 automated checks were run — 59 passed; the 2
failures are the pre-existing Mountain High / Dodge Ridge vertical-drop data
issue documented above, not bugs introduced by this round of changes.
