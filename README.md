# Climate Model Ranking for Monitoring Drought

Reproducible **R / RStudio project template** for rebuilding the workflow used in:

> Rayhan, M. & Afroz, R. (2024). *Evaluating climate models to analyze drought conditions in the western region of Bangladesh*. Progress in Disaster Science, 23, 100356.

## What this repository does

The workflow is organized to match the main steps of the published study:

1. Read monthly **BMD observed precipitation**
2. Read/extract **CORDEX climate-model precipitation**
3. Match model grid cells to the nearest BMD station
4. Apply **quantile-mapping bias correction**
5. Evaluate precipitation using:
   - RMSE
   - Pearson correlation
   - Taylor diagram
   - Mann-Whitney U test
6. Calculate **SPI-3, SPI-6, SPI-12 and SPI-24**
7. Evaluate SPI using:
   - t-test
   - Taylor diagram for SPI-3
8. Rank climate models
9. Create a **top-three model ensemble**
10. Analyze future short-term drought under **RCP 8.5**
11. Generate publication-style graphs and summary tables

## Important note

This is a **reconstructed reproducible code template**, not the original undergraduate-thesis code.

The published paper explains the methodology, but it does not provide the original scripts or every implementation detail. Therefore, a few coding decisions in this repository are transparent reconstruction choices.

The paper also states that the study data are confidential. For that reason, BMD observations are **not included** in this repository. Add your own authorized BMD data under `data/raw/bmd/`.

## Study setup

Historical period:

- 1971-2005

Future periods:

- 2006-2040: near future / 2020s
- 2041-2070: mid future / 2060s
- 2071-2100: far future / 2080s

Climate models:

- MIROC
- NOAA
- MPI
- IPSL
- CCCma

Published final ranking:

1. MIROC
2. NOAA
3. MPI
4. IPSL
5. CCCma

The published future analysis used an ensemble of the top three models: **MIROC, NOAA and MPI**.

## Repository structure

```text
Climate-Model-Ranking-for-monitoring-drought/
├── Climate-Model-Ranking.Rproj
├── README.md
├── .gitignore
├── run_all.R
├── R/
│   ├── 00_setup.R
│   ├── 01_read_bmd.R
│   ├── 02_read_cordex.R
│   ├── 03_bias_correction.R
│   ├── 04_precip_evaluation.R
│   ├── 05_spi_analysis.R
│   ├── 06_model_ranking.R
│   ├── 07_future_ensemble.R
│   ├── 08_drought_events.R
│   └── 09_plots.R
├── data/
│   ├── README.md
│   ├── stations.csv
│   ├── raw/
│   │   ├── bmd/
│   │   └── cordex/
│   │       ├── historical/
│   │       └── rcp85/
│   └── processed/
└── outputs/
    ├── figures/
    └── tables/
```

## Required R packages

```r
install.packages(c(
  "tidyverse",
  "lubridate",
  "ncdf4",
  "SPEI",
  "plotrix",
  "here"
))
```

## Expected BMD file

Put this file here:

```text
data/raw/bmd/bmd_monthly_precipitation.csv
```

Required columns:

```text
station,date,precip_mm
Rajshahi,1971-01-01,12.4
Rajshahi,1971-02-01,18.1
...
```

`date` should represent the first day of each month.

## Expected CORDEX NetCDF organization

A simple recommended layout is:

```text
data/raw/cordex/historical/MIROC/*.nc
data/raw/cordex/historical/NOAA/*.nc
data/raw/cordex/historical/MPI/*.nc
data/raw/cordex/historical/IPSL/*.nc
data/raw/cordex/historical/CCCma/*.nc

data/raw/cordex/rcp85/MIROC/*.nc
data/raw/cordex/rcp85/NOAA/*.nc
data/raw/cordex/rcp85/MPI/*.nc
data/raw/cordex/rcp85/IPSL/*.nc
data/raw/cordex/rcp85/CCCma/*.nc
```

The NetCDF reader assumes common names such as:

- precipitation variable: `pr`
- longitude: `lon`
- latitude: `lat`
- time: `time`

Change those names in `R/02_read_cordex.R` if your files use different variables.

## How to run in RStudio

Open:

```text
Climate-Model-Ranking.Rproj
```

Then run:

```r
source("run_all.R")
```

Or run scripts one by one in this order:

```r
source("R/00_setup.R")
source("R/01_read_bmd.R")
source("R/02_read_cordex.R")
source("R/03_bias_correction.R")
source("R/04_precip_evaluation.R")
source("R/05_spi_analysis.R")
source("R/06_model_ranking.R")
source("R/07_future_ensemble.R")
source("R/08_drought_events.R")
source("R/09_plots.R")
```

## Main outputs

Processed data:

```text
data/processed/bmd_monthly.csv
data/processed/cordex_historical_raw.csv
data/processed/cordex_future_raw.csv
data/processed/cordex_historical_corrected.csv
data/processed/cordex_future_corrected.csv
data/processed/spi_observed.csv
data/processed/spi_models_historical.csv
data/processed/future_ensemble_spi3.csv
data/processed/future_drought_events.csv
```

Tables:

```text
outputs/tables/precipitation_metrics.csv
outputs/tables/mann_whitney_results.csv
outputs/tables/spi_ttest_results.csv
outputs/tables/model_ranking_reconstructed.csv
outputs/tables/future_drought_summary.csv
```

Figures:

```text
outputs/figures/
```

## Reproducibility notes

### Bias correction

The paper reports QUANT quantile mapping. This repository uses an empirical quantile-mapping function written directly in R so the workflow is easy to understand and does not depend on a specialized bias-correction package.

### Final ranking

The paper reports the final order MIROC > NOAA > MPI > IPSL > CCCma, but it does not provide one explicit equation combining all evaluation metrics into a single score.

`R/06_model_ranking.R` therefore creates a clearly labeled **reconstructed average-rank score** using RMSE, correlation, Mann-Whitney p-value and SPI-3 t-test p-value. Keep the published ranking separate from this reconstructed score.

### Future SPI

The paper describes future SPI-3 analysis using the top-three RCM ensemble under RCP 8.5. Exact code-level details of the SPI reference period are not provided in the article. The template calculates SPI-3 from the future ensemble series. If you want a fixed 1971-2005 reference climatology, modify this step before claiming an exact reproduction.

## Suggested GitHub description

> R workflow for evaluating CORDEX regional climate models against BMD precipitation, ranking model performance, calculating SPI drought indices, and analyzing future drought conditions in western Bangladesh.

## Suggested GitHub topics

`climate-models` `drought` `spi` `cordex` `bangladesh` `r` `climate-change` `bias-correction` `rcp85`
