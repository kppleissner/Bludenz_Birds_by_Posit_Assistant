# Bludenz Birds Analysis

Analysis of a personal eBird "My eBird Data" export for a single location —
**Bludenz, Suchardstraße 5** (47.16 °N, 9.82 °E), Vorarlberg, Austria. The data
are garden/patch checklists spanning **December 2016 – October 2025**:
6,324 observations across 810 checklists and 78 species.

## What the analysis does

- **Cleans the raw export**: rescales latitude/longitude (stored as integers
  ×10⁷), parses the timestamp, and derives year, month, and hour fields.
- **Enriches taxonomy**: joins the eBird taxonomy from the
  [`auk`](https://cornelllabofornithology.github.io/auk/) package to attach
  family and order to every species (all 78 matched).
- **Summarises effort**: checklists per year and a year × month effort heatmap,
  since recording effort is uneven and shapes every other result.
- **Describes checklists**: histograms of duration, start hour, and species
  richness per checklist.
- **Species composition**: the 25 most frequently detected species (by share of
  checklists) and species richness per family.
- **Seasonality**: a species × month detection-frequency heatmap that separates
  year-round residents from summer migrants (e.g. Mönchsgrasmücke, Zilpzalp,
  Mauersegler, Rauchschwalbe).

## Files

| File | Description |
|------|-------------|
| `Bludenz_Birds_Analysis.qmd` | Self-contained Quarto report (cleaning, plots, analysis) |
| `Bludenz_Birds_Analysis.html` | Rendered report |
| `BludenzSuchardstr5_bisOkt19_2025-corrected.xlsx` | Source eBird export |
| `conversion_history.md` | Documented, reproducible record of every cleaning step |
| `renv.lock` | Package versions for a reproducible environment |

## Requirements

- R 4.6.x and [Quarto](https://quarto.org/)
- Packages are managed with [`renv`](https://rstudio.github.io/renv/); the
  project `.Rprofile` activates it automatically.

## How to render

The project is renv-managed and self-contained. On a fresh clone, first restore
the package library from the lockfile:

```r
renv::restore()
```

Then render the report from a terminal in the project directory:

```bash
quarto render Bludenz_Birds_Analysis.qmd
```

This produces `Bludenz_Birds_Analysis.html`. renv is picked up automatically via
the project `.Rprofile`, so no extra library configuration is needed.

## Notes and caveats

- Sampling effort is highly uneven across years and months; interpret trends
  cautiously.
- Detection frequency reflects both true abundance and detectability.
- This is single-location data — results describe one patch and do not
  generalise.
- Common names are in **German** as exported by eBird; scientific names and the
  `auk` taxonomy provide the language-neutral link.
