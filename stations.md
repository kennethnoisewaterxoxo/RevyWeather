# Weather Stations - Revelstoke BC & Surrounding Area

Focus: Temperature by elevation (lapse rate analysis)
Valley floor (Revelstoke): ~500m | Surrounding peaks: up to 2500m+

---

## BC Hydro Hydrometeorological Network ⭐ BEST SOURCE FOR HIGH ELEVATION

**Base URL:** `https://www.bchydro.com/info/res_hydromet/data/[CODE].txt`
- Real measured data, 15-minute intervals
- Up to 4 days of history
- Not quality-controlled but direct sensor readings
- **CORS likely blocked** — will need a proxy (Netlify function) to fetch from browser
- No API key required

| Station | Code | BC Snow ID | Elevation | Data Available | Sample Temp (2026-03-25) |
|---|---|---|---|---|---|
| Glacier NP Rogers Pass Low | `grp` | 2A34P | ~1,182m | Precip, Temp, SWE, Snow depth | -0.1°C |
| Mount Revelstoke | TBD | 2A06P | 1,850m | TBD | TBD |
| Colpitti Creek | `cpi` | 2A30P | 2,131m | Precip, Temp, SWE, Snow depth | -7.6°C |
| Caribou Creek Upper | `ccu` | 2A31P | 2,201m | Precip, Temp, SWE | -7.3°C |
| Conrad Glacier | `crg` | — | ~2,000m+ | Temp, RH, Wind speed/dir, Snow depth | -9.9°C |
| Wildcat Creek | TBD | 2A32P | 2,122m | TBD | TBD |

**Data format (tab-separated):**
```
Date (yyyy/mm/dd)  Time (PST)  Precipitation (mm)  Air Temperature (°C)  SWE (mm)  Snow depth (cm)
```

> These are the only confirmed real-time high-elevation measured temperatures in the area.
> Colpitti (2,131m) at -7.6°C and Caribou (2,201m) at -7.3°C vs Open-Meteo showing ~+1°C for Fidelity.

---

## ECCC / Environment Canada (MSC)

**API base:** `https://api.weather.gc.ca/`
- Stations: `/collections/climate-stations/items`
- Daily obs: `/collections/climate-daily`
- Hourly obs: `/collections/climate-hourly`
- No API key required. OGC/GeoJSON format.
- bbox query: `?bbox=-118.5,51.0,-117.0,51.6&limit=50`

| Station | Climate ID | Elevation | Data | Notes |
|---|---|---|---|---|
| Revelstoke A (Airport) | 49492 | ~457m | Hourly | Active, reliable |
| Glacier NP Mt Fidelity | 117CA90 | 1,890m | Daily only | Last data Feb 2024 — inactive |
| Glacier NP Rogers Pass | 1173191 | 1,330m | Daily only | Last data Feb 2024 — inactive |
| Revelstoke (town) | 1176750 | ~500m | Daily | Older record |

> ECCC climate stations for this area are daily only and went inactive in Feb 2024.
> Not useful for 48hr real-time display. Revelstoke Airport is the exception (hourly, active).

---

## Parks Canada / Avalanche Canada - Rogers Pass Network

**Web UI:** https://avalanche.ca/weather/stations/98
**API docs:** https://docs.avalanche.ca/ (forecasts only — station obs API not public)

| Station | AvCan ID | External ID | Elevation | Location |
|---|---|---|---|---|
| Rogers Pass | 98 | ROGER_60 | 1,315m | 51.300°N 117.515°W |
| Mt Fidelity | TBD | TBD | 1,905m | Above Rogers Pass |

Data collected: air temp, RH, wind speed/direction, snowpack, new snow (24hr), precipitation.

> Real data exists and is shown on avalanche.ca but no confirmed public JSON API.
> Data loads dynamically via JS — endpoint not discoverable via static fetch.
> Would need browser network tab inspection or contact with AvCan dev team.

---

## Revelstoke Mountain Resort (RMR)

**Subpeak station:** https://www.revelstokemountainresort.com/mountain/weather/subpeak-weather/

| Station | Elevation | Data Available |
|---|---|---|
| RMR Base (Village) | ~511m | Temp (°C) |
| RMR Summit (Stoke Chair) | ~2,225m | Temp (°C), wind (km/h) |
| RMR Subpeak | ~2,340m | Temp, dew point, RH, avg wind, max gust, wind direction |

- Updates every minute, hourly archive last 24hrs
- No confirmed public API — data rendered dynamically

---

## Open-Meteo (Model-derived)

**API:** `https://api.open-meteo.com/v1/forecast`
**No key required. Free.**

| Station | Lat/Lon | True Elevation | Open-Meteo Grid Elevation | Accuracy |
|---|---|---|---|---|
| Revelstoke Valley | 50.9998, -118.1956 | 511m | 457m | ✓ Good |
| RMR Mid-Mountain | 51.0550, -118.1700 | 1,400m | 1,103m | ~ OK |
| Rogers Pass | 51.3004, -117.5155 | 1,330m | 1,332m | ✓ Good |
| Mt Fidelity | 51.3500, -117.4200 | 1,905m | 954m | ✗ Wrong — 951m too low |
| RMR Subpeak | 51.0680, -118.1580 | 2,340m | 1,308m | ✗ Wrong — 1032m too low |

> Open-Meteo's 1km grid cannot resolve steep Selkirk terrain.
> Temperature and phase split data is unreliable for Fidelity and Subpeak.
> Use BC Hydro real stations for high-elevation data instead.

---

## BC Wildfire Service (BCWS) — Seasonal

**Station list:** http://www.bcwildfire.ca/Weather/stations.htm
- April–October only, not useful for winter lapse rate

---

## Elevation Reference Points

| Location | Elevation |
|---|---|
| Revelstoke (town / valley floor) | ~500m |
| Glacier NP Rogers Pass Low (BC Hydro) | ~1,182m |
| Rogers Pass summit (Hwy 1) | ~1,330m |
| RMR Mid-Mountain | ~1,400m |
| Mount Revelstoke (BC Hydro) | ~1,850m |
| Mt Fidelity (Parks Canada) | ~1,905m |
| Conrad Glacier (BC Hydro) | ~2,000m+ |
| Colpitti Creek (BC Hydro) | 2,131m |
| Wildcat Creek (BC Hydro) | 2,122m |
| Caribou Creek Upper (BC Hydro) | 2,201m |
| RMR Subpeak | ~2,340m |
| Mt Revelstoke summit | ~2,640m |

---

## Next Steps

1. **Integrate BC Hydro stations** — write a Netlify proxy function to fetch .txt files server-side (CORS workaround)
2. **Find BC Hydro codes** for Mount Revelstoke (2A06P) and Wildcat Creek (2A32P)
3. **Confirm Conrad Glacier elevation** — coldest reading, likely 2,000m+
4. **Inspect RMR subpeak network requests** in browser devtools to find JSON API
5. **Consider dropping Fidelity from Open-Meteo** — replace with Colpitti Creek (2,131m) BC Hydro real data
