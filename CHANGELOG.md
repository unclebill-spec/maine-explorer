# Maine Explorer — change log

Newest first. Times are ET.

## 2026-10-04
- 07:37 ET: State switcher: add New Hampshire (10 maps); border items now come from the New Hampshire map (NH homes and RN jobs at this map's caps)
- 06:20 ET: State switcher: add Utah (9 maps)

## 2026-10-03
- 20:59 ET: State switcher: add Idaho (8 maps)
- 18:25 ET: State switcher: Montana and Wyoming added
- 17:13 ET: Pin the bottom Top 10 pill row snug against the bottom-left edge of the map (5 px + safe area, same place on every window size, after cards open/close, resizes and full screen); zoom, scale and OpenStreetMap credit move up with it and stay uncovered; on short windows (e.g. 1366x600 above the Windows taskbar) the right-hand filter column now fits above Areas instead of being cut off
- 16:37 ET: Fix the bottom Top 10 pill row on desktop: after a window resize or full-screen change while a card was open the pills were measured while hidden and collapsed into two tiny overlapping pills at the left; the row now re-measures when it is shown again, pills size to their full titles and the row widens to hold exactly 3 whole pills (one pill per mouse-wheel notch, swipe and snap on phones unchanged)
- 15:57 ET: Bargains are purple with a star everywhere: the right-side Bargain filter button (column and landscape wheel), the Map key heading, the deal note on cards and the Top 10 'Bargain' mark now use the same purple star (#8e24aa) as the bargain pins and Deals pill instead of the yellow emoji
- 15:38 ET: Border items: pins up to ~15 mi outside the state line that meet this map's own criteria, from New Hampshire, Québec and New Brunswick (hospitals, schools, colleges, parks). Same icons, filters, Top 10s and share links; each card is tagged with its state; county/town stats and appeal scores unchanged (explorer/border.json, shared border_build.py + build.py hook + app.js/style.css)
- 14:40 ET: Purple bargain icon (pins, groups, Map key, Deals pill); bottom Top 10 pills show exactly 3 whole buttons and snap one button or one page at a time; right-side filter buttons scroll with the mouse wheel and wheel events over them no longer zoom the map
- 14:07 ET: Add ski areas and notable mountain peaks layers: ski/peak icons, cards with trails, lifts, vertical, snowfall, season, ticket and pass prices (season + source labeled), discounts, special days; peaks with elevation, prominence, activities, estimated summit weather; Ski and Peaks solo buttons, Map key, zoom tiers, share links #ski= / #peak=
- 12:53 ET: State switcher: add Vermont (KY / MA / ME / TN / VT); manifest short name ME Explorer
- 12:23 ET: Maine Phase 4: attractions (57: zoos/amusement, water park, aquarium, museums, state-park and Acadia campgrounds), 9 airports, 21 businesses for sale under $1M and 6 odd buildings under $600k (Crexi), more waterfalls (19) and trails (19)
- 12:08 ET: Maine Phase 3: jobs. Permanent RN jobs (blue hats; 438 jobs, 357 pass the default filter) from MaineGeneral, Covenant, Northern Light, York, Prime/CMH and independent hospitals; travel RN jobs (red hats; 165 at 18 hospitals from Vivian + Advantis) with the same exclusions as the other maps
- 11:55 ET: Maine Phase 2: homes at $550k caps (5+ ac $300k-$550k, 1+ ac and near-hospital under $550k) + new Cabin category (over 25 ac, 1+ bd/1+ ba, log-cabin icon, own layer/solo button/Map key/profiles/bargains/share links); one category per listing; shared app gains generic per-state ST.cabin/ST.caps
- 10:35 ET: Phase 1 base map: 245 areas (7 whole counties + 238 southern/coastal towns) with appeal score, 42 hospitals incl. 11 NH border centers and trauma levels, 593 schools graded by SEDA 2019 district results vs Maine and the U.S., 42 colleges, Wikidata parks/waterfalls/trails, NOAA climate and Compare areas

