
<!-- README.md is generated from README.Rmd. Please edit that file -->

# makepipe <img src='man/figures/logo.png' align="right" height="139"/>

<!-- badges: start -->

[![Codecov test
coverage](https://codecov.io/gh/kinto-b/makepipe/branch/master/graph/badge.svg)](https://app.codecov.io/gh/kinto-b/makepipe?branch=master)
[![R-CMD-check](https://github.com/kinto-b/makepipe/workflows/R-CMD-check/badge.svg)](https://github.com/kinto-b/makepipe/actions)
[![CRAN
status](https://www.r-pkg.org/badges/version/makepipe)](https://CRAN.R-project.org/package=makepipe)
<!-- badges: end -->

The goal of `makepipe` is to allow for the construction of make-like
pipelines in R with very minimal overheads. In contrast to `targets`
(and its predecessor `drake`) which offers an opinionated pipeline
framework that demands highly functionalised code, `makepipe` is
easy-going, being adaptable to a wide range of data science workflows.

A minimal example can be found here:
<https://github.com/kinto-b/makepipe_example>

## Installation

You can install the released version of `makepipe` from
[CRAN](https://CRAN.R-project.org) with:

``` r
install.packages("makepipe")
```

And the development version from [GitHub](https://github.com/) with:

``` r
# install.packages("devtools")
devtools::install_github("kinto-b/makepipe")
```

## Building a pipeline

To construct a pipeline, one simply needs to chain together a number of
`make_with_*()` statements. When the pipeline is run through, each
`make_with_*()` block is evaluated if and only if the `targets` are
out-of-date with respect to the `dependencies` (and `source` file). But,
whether or not the block is evaluated, a segment will be added to the
Pipeline object behind the scenes. At the end of the script, once the
entire pipeline has been run through, one can display the accumulated
Pipeline object to produce a flow-chart visualisation of the pipeline.
For example:

``` r
library(makepipe)
make_with_source(
  dependencies = c("data/0_raw_data.csv", "lookup/concordance.csv"),
  source = "1 data_prep.R",
  targets = "data/1_data.Rds"
)
make_with_recipe(
    dependencies = c("data/1_data.Rds", "data/0_pop.Rds"),
    recipe = {
      dat <- readRDS("data/raw_data.Rds")
      pop <- readRDS("data/pop_data.Rds")
      merged_dat <- merge(dat, pop, by = "id")
      saveRDS(merged_dat, "data/2_data.Rds")
    },
    targets = c("data/2_data.Rds")
)
show_pipeline(labels = c(
  "lookup/concordance.csv" = "Postcode concordance",
  "data/0_raw_data.csv" = "Raw survey data"
))
```

<img src="man/figures/README-example_pipeline.png" width="75%" style="display: block; margin: auto;" />