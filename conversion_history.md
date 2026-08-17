# Conversion & Analysis History

**Project:** eBird analysis — Bludenz, Suchardstraße 5
**Source file:** `BludenzSuchardstr5_bisOkt19_2025-corrected.xlsx`
**Environment:** R 4.6.1 (Positron), eBird taxonomy v2025 via `auk` 0.9.x
**Date:** 2026-08-16

This file records every transformation applied to convert the raw eBird Excel
export into the cleaned dataset used by `Bludenz_Birds_Analysis.qmd`, so the
pipeline is reproducible and auditable.

---

## 1. Source description

- Format: eBird "My eBird Data" export (single sheet `bisOkt19_2025`).
- Dimensions: 6,324 rows × 23 columns.
- Grain: one row per species observation on a checklist.
- Coverage: 810 checklists, 78 species, single location
  (Bludenz, Suchardstraße 5), 2016-12-27 to 2025-10-19.

## 2. Packages installed

Installed into the user library (not present in the renv lockfile):

```
readxl, auk, tidyr, lubridate, stringr, forcats, readr
```

`dplyr`, `ggplot2`, `knitr`, `rmarkdown` were already available.

## 3. Cleaning / conversion steps

| # | Field(s) | Problem in raw data | Conversion applied |
|---|----------|---------------------|--------------------|
| 1 | `Latitude`, `Longitude` | Stored as integers scaled by 10^7 (e.g. 471604564) | Divided by 1e7 → `latitude` = 47.1605, `longitude` = 9.8216 |
| 2 | `Time` | POSIXct carrying a spurious 1899-12-31 date | Extracted `hour` with `lubridate::hour()` |
| 3 | `Date` | POSIXct with UTC tag | Coerced to `Date`; derived `year`, `month` (labelled factor) |
| 4 | `Count` | Text column (eBird uses "X" for present) | `as.numeric()` → `count_num`; **no "X" values present**, all numeric |
| 5 | `Duration (Min)` | Renamed for tidiness | → `duration_min` (218 NA, mostly casual observations) |
| 6 | `Scientific Name` | No higher taxonomy | Left-joined `auk::ebird_taxonomy` to add `family`, `order`; **all 78 species matched** |

### Columns dropped from analysis (not converted)
- `County` — 100% missing (6,324 NA).
- `Area Covered (ha)` — 100% missing.
- `Distance Traveled (km)` — 93% missing (only traveling counts).
- `Breeding Code`, `Observation Details`, `ML Catalog Numbers` — mostly missing, not used.

## 4. Derived analytical tables

- `birds` — cleaned observation-level table with taxonomy attached.
- `checklists` — one row per `Submission ID` (date, hour, duration, protocol,
  species richness).
- `sp_freq` — per-species detection frequency = share of checklists detected in.
- `effort` — checklist counts by year × month (effort heatmap).
- `season` — detection frequency by species × month (seasonality heatmap).

## 5. Outputs produced

- Effort bar chart (checklists per year).
- Effort heatmap (checklists per year × month).
- Histograms: checklist duration, start hour, species richness per checklist.
- Species composition bar chart (top 25 by detection frequency).
- Species-per-family bar chart.
- Seasonality heatmap (top 20 species × month).
- `Bludenz_Birds_Analysis.qmd` → rendered `Bludenz_Birds_Analysis.html`.

## 6. Reproduction

The project is renv-managed and self-contained. All analysis packages are
recorded in `renv.lock` and installed in the project library, so rendering
uses renv automatically (via the project `.Rprofile`) — no library workaround
is required:

```bash
quarto render Bludenz_Birds_Analysis.qmd
```

To reproduce the environment on a fresh clone, restore the project library
from the lockfile first:

```r
renv::restore()
```

Note: the packages were added with `renv::install()` + `renv::snapshot()`.
Because the interactive Positron console was not renv-activated, that step
updated `renv.lock` but left the project library empty; `renv::restore()` (run
in a renv-activated process) populated the project library, after which the
report rendered under renv.

## 7. Caveats

- Sampling effort is highly uneven across years/months; trends are
  effort-sensitive.
- Detection frequency reflects both abundance and detectability.
- Single-location data — not generalisable beyond this patch.
- Common names are German as exported by eBird.
