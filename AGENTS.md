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
