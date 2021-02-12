
<!-- README.md is generated from README.Rmd. Please edit that file -->

# tidyverse <a href='https://tidyverse.tidyverse.org'><img src='man/figures/logo.png' align="right" height="138.5" /></a>

<!-- badges: start -->

[![CRAN\_Status\_Badge](http://www.r-pkg.org/badges/version/healthyverse)](https://cran.r-project.org/package=healthyverse)
![](http://cranlogs.r-pkg.org/badges/healthyverse?color=brightgreen)
![](http://cranlogs.r-pkg.org/badges/grand-total/healthyverse?color=brightgreen)
[![Lifecycle:
stable](https://img.shields.io/badge/lifecycle-stable-brightgreen.svg)](https://lifecycle.r-lib.org/articles/stages.html##stable)
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

-   [healthyR](https://www.spsanderson.com/healthyR/), for analyzing
    common data problems in administrative data.
-   [healthyR.data](https://www.spsanderson.com/healthyR.data), for a
    simulated data-set.
-   [healthyR.ts](https://www.spsanderson.com/healthyR.ts), for
    time-series functions.

You also get a condensed summary of conflicts with other packages you
have loaded:

``` r
library(healthyverse)
#> -- Attaching packages ------------------------------- healthyverse 0.0.0.9000 --
#> v healthyR      0.1.1.9000     v healthyR.ts   0.1.1     
#> v healthyR.data 1.0.0
#> 
```

You can see conflicts created later with `healthyverse_conflicts()`:

``` r
library(MASS)
healthyverse_conflicts()
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
