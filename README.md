# R Files — occupancy modeling pipeline

R scripts and R Markdown notebooks for processing camera-trap photo identification data and fitting multi-city occupancy models (including analyses built around the [`autoOcc`](https://github.com/mfidino/autoOcc) package).

Scripts are numbered to reflect the usual **data → models → summaries → figures** order. Paths are relative to this folder: inputs are expected under `../Input Data/`, and outputs under `../Output/`, `../Species Occupancy/`, or paths noted in each file.

## Requirements

- **R** (4.x recommended)
- Core packages used across the pipeline include **dplyr**, **tidyr**, **ggplot2**, **lubridate**, **purrr**, **sf**, **terra**, **stringr** / **forcats**, and others as referenced in each notebook.
- **Occupancy modeling** (`5_OccupancyModeling.Rmd`): install [**autoOcc**](https://github.com/mfidino/autoOcc) via `devtools::install_github("mfidino/autoOcc")`.
- **Species richness** (`4_SpeciesRichness.Rmd`): **iNEXT**, **googlesheets4** (if used), **terra**.
- Some steps use **igraph** for spatial clustering of sites (`3_ExtractSiteData.Rmd`).

Install missing packages in R with `install.packages(...)` before knitting or sourcing.

## Pipeline overview

| Step | File | Role |
|------|------|------|
| 1a | `1_CleanForOccupancy.R` | Per city: parse photo database CSVs (commas inside `{...}` fields), keep key columns, assign seasons, write cleaned `photo_database_fin_*_cleaned.csv` files. Set `city` at the top of the script. |
| 1b | `1_FormActiveInactive.R` | **PACA-only:** convert day-based active/inactive report to the season-based format used elsewhere. |
| 1c | `1_MultiCityActiveInactive.Rmd` | Standardize multi-city observation / active history into a single active-dates workflow. |
| 2 | `2_ConsolidateMultiCityData.Rmd` | Row-bind per-city cleaned photo data into `multi_city_data_cleaned.csv`; merge PACA and multi-city active dates into `all_city_active_dates.csv`. |
| 3 | `3_ExtractSiteData.Rmd` | Cluster cameras within ~150 m (site groups), attach **impervious** surface values from NLCD GeoTIFF under `Input Data`. |
| 4 | `4_ConvertToOccupancy.Rmd` | Build species-level occupancy inputs and reports under `../Species Occupancy/` (also references running models in some versions—check the notebook). |
| 4 | `4_SpeciesRichness.Rmd` | Species richness analyses (e.g. **iNEXT**) parallel to strict occupancy prep. |
| 5 | `5_OccupancyModeling.Rmd` | **Parameterized** occupancy models: set `params` (`path`, `sp_name`, `range`, `by`, `city`, `conf`) when rendering. Writes per-city / species outputs under the path you pass. |
| 6 | `6_SummarizeModelResults.Rmd` | Aggregate AIC / model comparison tables from `../Output/` using species and city lists from inputs. |
| 7 | `7_OccupancyModelResults.Rmd` | Explore and summarize parameter estimates (example workflow includes PACA-focused filters). |
| 7 | `7_OccupancyAllParams.Rmd` | Cross-threshold / multi-species parameter summaries (e.g. model `m2`). |
| 7 | `7_GenerateOccupancyFigures.Rmd` | Pull prediction CSVs and build figure-ready summaries for chosen cities, species, and confidence thresholds. |

Run earlier numbered steps before later ones unless you are only refreshing a self-contained notebook (e.g. figures from existing `Output/`).

## Data layout (typical)

- **`../Input Data/`** — Raw and intermediate CSVs (`photo_database_fin_*.csv`, cleaned variants, active-date files, NLCD raster, etc.). Filenames match those referenced in each script.
- **`../Output/`** — Model results, parameters, and summaries produced by `5_` and `6_` (often organized by city and confidence).
- **`../Species Occupancy/`** — Occupancy report outputs from `4_ConvertToOccupancy.Rmd` when that path is used.

Adjust the `filepath` / `input_path` variables at the top of each notebook if your directory layout differs.
