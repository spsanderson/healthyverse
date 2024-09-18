
<!-- README.md is generated from README.Rmd. Please edit that file -->

# healthyverse <img src="man/figures/logo.png" width="147" height="170" align="right" />

<!-- badges: start -->

[![CRAN_Status_Badge](http://www.r-pkg.org/badges/version/healthyverse)](https://cran.r-project.org/package=healthyverse)
![](http://cranlogs.r-pkg.org/badges/healthyverse?color=brightgreen)
![](http://cranlogs.r-pkg.org/badges/grand-total/healthyverse?color=brightgreen)
[![Lifecycle:
stable](https://img.shields.io/badge/lifecycle-stable-brightgreen.svg)](https://lifecycle.r-lib.org/articles/stages.html##stable)
[![PRs
Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat-square)](https://makeapullrequest.com)
<!-- badges: end -->

## Overview

The healthyverse is a set of packages that work in harmony because they
share common data representations and API design. The **healthyverse**
package is designed to make it easy to install and load core packages
from the healthyverse in a single command.

## Installation

``` r
# Install from CRAN
install.packages("healthyverse")
# Or the development version from GitHub
# install.packages("devtools")
devtools::install_github("spsanderson/healthyverse")
```

## Usage

`library(healthyverse)` will load the core healthyverse packages:

- [healthyR](https://www.spsanderson.com/healthyR/), for analyzing
  common data problems in administrative data.
- [healthyR.data](https://www.spsanderson.com/healthyR.data/), for a
  simulated data-set.
- [healthyR.ts](https://www.spsanderson.com/healthyR.ts/), for
  time-series functions.
- [healthyR.ai](https://www.spsanderson.com/healthyR.ai/), for ai
  related functions.
- [TidyDensity](https://www.spsanderson.com/TidyDensity/), for tidy
  style r\_ distribution data.
- [tidyAML](https://www.spsanderson.com/tidyAML/), for tidymodels
  auto-ml.
- [RandomWalker](https://www.spsanderson.com/RandomWalker/), for random
  walk functions.

You also get a condensed summary of conflicts with other packages you
have loaded:

``` r
library(healthyverse)
#> ── Attaching packages ─────────────────────────────── healthyverse 1.0.4.9000 ──
#> ✔ healthyR      0.2.2          ✔ TidyDensity   1.5.0     
#> ✔ healthyR.data 1.1.1          ✔ tidyAML       0.0.5.9000
#> ✔ healthyR.ts   0.3.0.9000     ✔ RandomWalker  0.1.0     
#> ✔ healthyR.ai   0.1.0
#> Warning: package 'healthyR.data' was built under R version 4.3.3
#> Warning: package 'TidyDensity' was built under R version 4.3.3
#> Warning: package 'parsnip' was built under R version 4.3.3
#> Warning: package 'RandomWalker' was built under R version 4.3.3
#> ── Conflicts ─────────────────────────────────────── healthyverse_conflicts() ──
#> ✖ tidyAML::check_duplicate_rows() masks TidyDensity::check_duplicate_rows()
#> ✖ tidyAML::quantile_normalize()   masks TidyDensity::quantile_normalize()
```

You can see conflicts created later with `healthyverse_conflicts()`:

``` r
library(MASS)
healthyverse_conflicts()
#> ── Conflicts ─────────────────────────────────────── healthyverse_conflicts() ──
#> ✖ tidyAML::check_duplicate_rows() masks TidyDensity::check_duplicate_rows()
#> ✖ tidyAML::quantile_normalize()   masks TidyDensity::quantile_normalize()
```

And you can check that all tidyverse packages are up-to-date with
`healthyverse_update()`:

``` r
healthyverse_update()
#> The following packages are out of date:
#>  * healthyR (0.4.0 -> 0.4.1)
#>  * healthyR.ts   (0.4.1 -> 0.5)
#>  * healthyR.data  (0.12.6 -> 0.12.7)
#> Update now?
#> 
#> 1: Yes
#> 2: No
```
