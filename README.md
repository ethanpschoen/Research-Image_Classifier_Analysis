# R Files — occupancy modeling pipeline

R scripts and R Markdown notebooks for processing camera-trap photo identification data and fitting multi-city occupancy models (including analyses built around the [`autoOcc`](https://github.com/mfidino/autoOcc) package).

Scripts are numbered to reflect the usual **data → models → summaries → figures** order. Paths are relative to this folder: inputs are expected under `../Input Data/`, and outputs under `../Output/`, `../Species Occupancy/`, or paths noted in each file.

## Requirements

- **R** (4.x recommended)
- Core packages used across the pipeline include **dplyr**, **tidyr**, **ggplot2**, **lubridate**, **purrr**, **sf**, **terra**, **stringr** / **forcats**, and others as referenced in each notebook.
- **Occupancy modeling** (`3_OccupancyModeling.Rmd`): install [**autoOcc**](https://github.com/mfidino/autoOcc) via `devtools::install_github("mfidino/autoOcc")`.
- **Species richness** (`2_SpeciesRichness.Rmd`): **iNEXT**, **googlesheets4** (if used), **terra**.
- Site clustering / impervious extraction steps use **igraph**, **sf**, and **terra** (`1_SiteDataActivity.Rmd`).

Install missing packages in R with `install.packages(...)` before knitting or sourcing.

## Pipeline overview

| Step | File | Role |
|------|------|------|
| 1 | `1_CleanForOccupancy.Rmd` | Parse all city photo database CSVs (commas inside `{...}` fields), keep key columns, assign seasons, and output one consolidated `multi_city_data_cleaned.csv`. |
| 1 | `1_SiteDataActivity.Rmd` | Build all-city site activity and site metadata products: standardize active histories, cluster nearby cameras (~150 m), and extract imperviousness from NLCD raster buffers. Outputs `all_city_active_dates.csv` and `camera_sites_with_impervious.csv`. |
| 2 | `2_ConvertToOccupancy.Rmd` | Build species-level occupancy inputs and reports under `../Species Occupancy/` (also references running models in some versions—check the notebook). |
| 2 | `2_SpeciesRichness.Rmd` | Species richness analyses (e.g. **iNEXT**) parallel to strict occupancy prep. |
| 3 | `3_OccupancyModeling.Rmd` | **Parameterized** occupancy models: set `params` (`path`, `sp_name`, `range`, `by`, `city`, `conf`) when rendering. Writes per-city / species outputs under the path you pass. |
| 4 | `4_SummarizeModelResults.Rmd` | Aggregate AIC / model comparison tables from `../Output/` using species and city lists from inputs. |
| 5 | `5_OccupancyModelResults.Rmd` | Explore and summarize parameter estimates (example workflow includes PACA-focused filters). |
| 5 | `5_OccupancyAllParams.Rmd` | Cross-threshold / multi-species parameter summaries (e.g. model `m2`). |
| 5 | `5_GenerateOccupancyFigures.Rmd` | Pull prediction CSVs and build figure-ready summaries for chosen cities, species, and confidence thresholds. |

Run earlier numbered steps before later ones unless you are only refreshing a self-contained notebook (e.g. figures from existing `Output/`).

## Data layout (typical)

- **`../Input Data/`** — Raw and intermediate CSVs (`photo_database_fin_*.csv`, cleaned variants, active-date files, NLCD raster, etc.). Filenames match those referenced in each script.
- **`../Output/`** — Model results, parameters, and summaries produced by `3_`, `4_`, and `5_` steps (often organized by city and confidence).
- **`../Species Occupancy/`** — Occupancy report outputs from `2_ConvertToOccupancy.Rmd` when that path is used.

Adjust the `filepath` / `input_path` variables at the top of each notebook if your directory layout differs.
