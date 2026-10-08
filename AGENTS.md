# Maine Explorer — agent handoff

Static Leaflet map for a nurse household, the same shared app as the Kentucky, Tennessee and Massachusetts Explorers (`app.js`, `areas.js`, `perm.js`, `profiles.js`, `extras.js`, `sw.js` are byte-identical copies of `/workspace/kentucky/explorer/*`; edit them there and copy to every state). Maine-specific settings live only in `build.py` `STATE`.

Live: https://unclebill-spec.github.io/maine-explorer/ · repo unclebill-spec/maine-explorer · local preview: `python3 -m http.server 8855` in `/workspace/maine/explorer`.

## Blocks (the shared app's "counties" slot)
245 areas from `scripts/me_common.py` -> `data/statewide/raw/me_blocks_500k.zip`: 7 whole inland/northern counties (Aroostook, Franklin, Kennebec, Oxford, Penobscot, Piscataquis, Somerset; names keep " County" because Franklin and Penobscot are also towns) + 238 towns / plantations / unorganized territories of the 9 southern and coastal counties (York, Cumberland, Androscoggin, Sagadahoc, Lincoln, Knox, Waldo, Hancock, Washington). `build.py` uses block names as-is (no " County" stripping).

## Data pipeline (run from /workspace/maine)
1. `/workspace/kentucky/.venv/bin/python scripts/me_layers.py` -> `data/statewide/me_block_*.csv`, `me_hospitals_points.csv`, `data/schools/me_*`.
2. `/usr/bin/python3 scripts/me_appeal_build.py` -> `me_block_appeal.csv` (+ method json; OSRM drives cached in `data/statewide/raw/osrm_block_hospital_drives.json`). Blocks with < 100 residents rank after every lived-in block (band "Few or no residents").
3. `climate/parse.py` + `climate/build_clim.py` -> `data/clim.json` (NOAA 1991-2020 normals, 99 ME + border NH stations).
4. `data/osm/wd_act.py` (Wikidata parks / protected land / waterfalls / caves / trails), `data/osm/nces_post.json` (NCES EDGE colleges).
5. `compare/me_areas.py` -> `data/areas.json` (KY/TN areas + Portland, Lewiston, Bangor, South Portland, Auburn).
6. Publish: `cd publish && PATH=/usr/bin:$PATH ./publish.sh -m "msg"` (foreground; flock, pull/fast-forward, build, minify, secscan files + history, push, wait for Pages, CHANGELOG entry).
7. Test: `python3 perf/smoke.py BASE TAG` (screens in `perf/shots`), load: `/workspace/kentucky/perf/profile.py URL LABEL`.

## Sources and known limits
- RN wages: Maine DOL CWRI OEWS May 2025 (`Maine-AllAreas.xlsx`), county estimates with metro/nonmetro fallback. The BLS API was over its daily limit and bls.gov returns 403 to the box.
- Schools: SEDA 2025.1 district means for 2019 (latest Maine year in SEDA), each school graded by its district vs Maine (A ≥ +0.5 grade levels … F < −0.5). Maine DOE's own assessment export returned HTTP 429 and Ed Data Express sits behind a bot check, so grades are dated and district-level. Lewiston has no SEDA year after 2014.
- Hospitals: `/workspace/maine/data/hospitals.json` research (31) + CMS + 11 NH border hospitals (Dartmouth Hitchcock Level I, Elliot / Portsmouth / Concord Level II, several Level III). Maine trauma: only Maine Medical Center (Level I) and Eastern Maine Medical Center (Level II) are ACS-verified (CMMC ended its Level III); all 7 Level III pins are in NH.
- Parks etc. come from Wikidata, not OSM (Overpass was unreachable from the box in Oct 2026), so coverage is thinner than OSM.
- Home searches use the KY default caps ($300k–$500k 5+ acres, $425k 1+ acre, $325k near-hospital); no `STATE["caps"]` entry. Ask Bill before raising them.

## Ski areas + notable peaks (Oct 3, 2026)
24 ski areas and 39 peaks, from explorer/mtn.json and explorer/img/mtn/. These are shared app files from KY; full notes are in /workspace/kentucky/explorer/AGENTS.md.
- build.py has the mtn_build hook (`mtn_build.add(data)` before `write_split`, then `mtn_build.write_detail(OUT)`). Keep it if build.py is regenerated, or rerun `/workspace/mtn/scripts/hook_build.py /workspace/maine`.
- Rebuild the data with `/workspace/mtn/scripts/make_state.py ME`. Test with `/workspace/mtn/test_mtn.py BASE TAG SKI_ID PEAK_ID`.

## Border items (Oct 3, 2026)
`explorer/border.json` (from /workspace/border/scripts/make_border.py) adds pins within ~15 mi outside the state line, tagged with their state (`bst`), excluded from town/county stats. Shared code: border_build.py + build.py hook + app.js. Details and regeneration: KY explorer/AGENTS.md 'Border items'.
- Oct 3, 2026: purple bargain star on the Bargain filter button etc. (shared app.js/style.css; KY explorer/AGENTS.md 'Purple bargain star everywhere').
- Oct 3, 2026: pill row fix kyxUI4 (shared app.js/style.css; KY explorer/AGENTS.md 'Pill row fix').
- Oct 3, 2026: kyxUI5 pill row pinned bottom-left + short-window column fit (shared; KY explorer/AGENTS.md).

### 50+ acre lots under $250k (Oct 4, 2026 ~10:31 AM ET, big-land worker)
- Black-star layer `big-land` (50+ ac, < $250k, land or home), "50+ ac" button, Map key row, card; shared code from the KY explorer (see KY explorer/AGENTS.md, same date). build.py (marker BIGLAND) merges `/workspace/maine/bigland.json`.
- Refresh: `/usr/bin/python3 /workspace/bigland/bigland.py ME --refresh` before build/publish (keeps the old file if Zillow blocks). Notes: /workspace/bigland/PROGRESS.md.

### Caves and waterfalls on the property (Oct 4, 2026, cave/falls worker)
- Bill, Oct 4 2:12 PM: "add any properties that have a cave or waterfall to the maps, have a small waterfall for the waterfall and a small bat for the caves, the flying type of bat".
- Layer `cvf` (categories `cave` / `falls`; `cf` = kinds, `cfq` = the listing's own words): flying-bat pin for caves (also used when a listing has both), waterfall pin, groups, "Cave" / "Falls" buttons (hidden when the map has none), Map key row, card. A listing already on the map in another category keeps it and gets `cf`/`cfq` (shows under both). Shared code from the KY explorer (KY explorer/AGENTS.md, same date). build.py (marker CAVEFALLS) merges `/workspace/maine/cavefalls.json`; border items come from make_border.py (`CFCAP`).
- Refresh: `/usr/bin/python3 /workspace/cavefalls/cavefalls.py ME --refresh` before build/publish (Zillow keyword search + listing text check + dedupe of the same land listed twice; keeps the old file if Zillow blocks; `--offline` re-checks from the cache). Notes: /workspace/cavefalls/PROGRESS.md.

## Target stores layer (Oct 7, 2026 ~5:15 PM ET, Target worker) — shared app.js + new shared target_build.py + build.py hook
- Bill: a Target stores layer on all 11 maps. Every Target in the state + stores within ~15 mi outside the state line (border rule: `bst`/`bco`/`bmi`, no `county` field, so never in county stats).
- **Data:** `/workspace/target/` (log: PROGRESS.md). Source = Target's own store directory (`target.com/store-locator/store-directory/<state>`, the official per-state list and count) + each store's page (`target.com/sl/<slug>/<id>`: JSON-LD geo, address, regular hours, phone, services). OpenStreetMap (Overpass, brand:wikidata=Q1046951) is only used to find neighbor-state stores near the line and as a count cross-check. Scripts: `scripts/fetch_dir.py` → `fetch_sl.py <STs>` → `parse_sl.py` → `make_target.py <STs>` (→ `out/<ST>.json`, copy to `<state>/explorer/target.json`) → build → `scripts/drives.py <statedir>` (OSRM free-flow minutes from each listing to its fastest of the 3 nearest stores, written into target.json `drv`, cache `cache/osrm.json`) → build again.
- **Build:** `explorer/target_build.py` (shared, md5-identical everywhere; in each sync_shared.sh list). build.py line right after `border_build.add(data, HERE)`: `import target_build; target_build.add(data)` (re-apply: `/workspace/target/patch/hook_build.py <statedir>`). Writes `K.targets` (whole rows in core.js, ~250 B each) + `K.meta.target`, and `nearby.tg` on every property (OSRM minutes when `drv` matches the listing id + spot, else straight-line × 1.3 "approx"). No target.json → no-op.
- **App (block "Target stores" before `function kyxMtn`; re-apply `/workspace/target/patch/patch_app.py app.js`, idempotent, marker `function kyxTarget(`):** icon `G.tgt` (small red bullseye), layer key `tgt` (on by default, icons from `FULL.tgt` = 9, red faint dots below, grouped like the other kinds), right-stack button "Target" (also in the landscape wheel; shows in Bill's P1–P4 and Anna mode — profiles don't filter it), Layers row, Map key row, search ("Target …"), card `R.target` (name, address, phone, store number, regular hours, services, target.com link; border stores get the 🧭 state tag), share `#target=<store number>` (index.html#…; no share page), Back stack like every card, property cards' Nearby row "🎯 Target · ~N min drive". `BST_NAME` gained the western states (border tags now say "South Dakota" instead of "SD").
- **Test:** `/usr/bin/python3 /workspace/target/test_target.py BASE TAG [anna]` (412×915 touch + 1280×720: button, solo on/off, zoom tiers + groups, pin tap, share links in-state + border, Nearby link + ‹ Back, Bill profile, Anna mode when the map has it, wheel over the column, 3 whole pills, no console errors). Screenshots `/workspace/target/shots/`.
- **Refresh:** Target opens/closes stores rarely; rerun the scripts above (fetch_sl.py only downloads pages not in cache/sl/; delete a page to refetch it).

## Active filter on top: kyxFoc (Oct 7, 2026 ~9 PM ET) — shared app.js/style.css
- The active right-side button / Anna button / open Top 10 list draws its pins 1.4x larger (groups 1.15x) and above everything; other pins (trauma, airports, cities...) go 0.72x and underneath while it is on. No filter = unchanged. Details: /workspace/kentucky/explorer/AGENTS.md "Active filter on top"; test /workspace/filterfocus/smoke.py BASE TAG.
