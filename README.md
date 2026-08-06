# hot is the new cool

**The hottest place on Earth right now.**

Live at **https://hotplace.fyi**

A single static HTML page — no build step, no backend, no API key. It shows the
current world temperature leader with its country flag and continent silhouette,
seven runners-up, and a solar band explaining *why* that place is hottest right now.

## Where the data comes from

- **[Open-Meteo](https://open-meteo.com)** (free, CORS-enabled, CC-BY 4.0).
  The page sends one batched GET to `api.open-meteo.com/v1/forecast` asking for
  `current=temperature_2m` at **256 candidate sites** — heat-prone places in both
  hemispheres (Death Valley, the Lut, Mesopotamia, the Sahara, the Australian
  outback, …), so the page stays interesting in January as well as July.
- Readings are **model analysis at each coordinate**, not raw station reports
  (SYNOP/METAR) — typically within a degree or two of the official station value.
  Station-based sources like Ogimet and NOAA don't allow cross-origin browser
  requests, so a purely static page can't call them directly.
- Continent silhouettes are dissolved and simplified from
  [Natural Earth](https://www.naturalearthdata.com/) 110m polygons (public domain);
  the leader dot is projected with the same maths that drew the coastline.
  Flags are regional-indicator emoji derived from the ISO 3166 code — no image CDN.

## How often it refreshes

- The page re-polls Open-Meteo **every 10 minutes** (`POLL_MS` at the top of the
  script), entirely in the visitor's browser — nothing server-side to schedule.
- When a backgrounded tab comes back to the foreground and the last successful
  poll is stale, it refreshes **immediately**.
- Open-Meteo's underlying model analysis updates roughly every 15 minutes.

## How the ranking works

- All 256 sites are ranked by current temperature; the maximum takes the title.
- The runners-up list is thinned to **one entry per 500 km**
  (`distinctRegions`), so Death Valley's neighbours don't fill every row.
  Ranks are kept true, which is why numbers may skip (08, 10, 13 …) — itself a
  signal that one basin is running the table.
- The band across the middle shows longitude 180 °W–180 °E shaded by solar hour
  angle, with a dashed line at the subsolar meridian and a red tick on the
  current leader — answering "why there" without a paragraph.

## Tuning

Everything configurable sits at the top of the `<script>` in `index.html`:

| Knob | Default | Meaning |
|---|---|---|
| `POLL_MS` | 10 min | How often the page re-polls |
| `SHOWN` | 7 | Runners-up listed |
| `SITES` | 256 entries | `[name, ISO-3166 alpha-2, lat, lon]` candidates |

## Deploying

It's one file. Any static host works — this copy is served by GitHub Pages from
`main`. To update: edit `index.html`, commit, push; Pages redeploys in about half
a minute (visitors may see the cached copy for up to 10 minutes).

## Attribution

Weather data by [Open-Meteo.com](https://open-meteo.com) (CC-BY 4.0).
Map geometry from [Natural Earth](https://www.naturalearthdata.com/) (public domain).
