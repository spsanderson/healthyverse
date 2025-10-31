
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

The **healthyverse** is a comprehensive ecosystem of R packages
designed for healthcare data analysis, time-series forecasting, machine
learning, and statistical modeling. Inspired by the tidyverse
philosophy, the healthyverse packages work in harmony because they share
common data representations and API design patterns.

The **healthyverse** package itself is a meta-package that makes it
easy to install and load all core healthyverse packages in a single
command, similar to how the tidyverse package works for data science
workflows.

### Why healthyverse?

- **🏥 Healthcare Focus**: Purpose-built tools for analyzing
  administrative healthcare data, patient records, and medical
  time-series
- **🔄 Consistent API**: All packages follow similar design patterns,
  making them easy to learn and use together
- **📊 Comprehensive Toolkit**: From data cleaning to machine learning,
  all tools you need in one ecosystem
- **⚡ Workflow Efficiency**: Load all packages at once and get notified
  of any function conflicts
- **🔍 Dependency Management**: Built-in tools to check package versions
  and update the entire suite

## Installation

You can install the released version of healthyverse from
[CRAN](https://CRAN.R-project.org):

``` r
install.packages("healthyverse")
```

Or install the development version from GitHub:

``` r
# install.packages("devtools")
devtools::install_github("spsanderson/healthyverse")
```

## Core Packages

Loading `library(healthyverse)` will install and attach the following
core packages:

### Data Analysis & Management

- **[healthyR](https://www.spsanderson.com/healthyR/)** - Tools for
  analyzing common data problems in healthcare administrative data.
  Includes functions for calculating length of stay, readmission rates,
  and other healthcare-specific metrics.

- **[healthyR.data](https://www.spsanderson.com/healthyR.data/)** -
  Provides simulated healthcare datasets for testing and learning.
  Perfect for educational purposes and developing proof-of-concept
  analyses.

### Time-Series & Forecasting

- **[healthyR.ts](https://www.spsanderson.com/healthyR.ts/)** -
  Comprehensive time-series analysis and forecasting functions optimized
  for healthcare data patterns. Includes tools for seasonal
  decomposition, trend analysis, and automated forecasting.

### Machine Learning & AI

- **[healthyR.ai](https://www.spsanderson.com/healthyR.ai/)** - AI and
  machine learning utilities specifically designed for healthcare
  applications. Features automated model building, evaluation, and
  prediction workflows.

- **[tidyAML](https://www.spsanderson.com/tidyAML/)** - Automated
  machine learning (AutoML) framework built on tidymodels. Streamlines
  the process of training, tuning, and comparing multiple models
  simultaneously.

### Statistical Distributions & Simulation

- **[TidyDensity](https://www.spsanderson.com/TidyDensity/)** - Generate
  and visualize probability distributions in a tidy format. Makes it
  easy to work with, compare, and plot various statistical
  distributions.

- **[RandomWalker](https://www.spsanderson.com/RandomWalker/)** -
  Simulate and analyze random walk processes. Useful for modeling
  stochastic processes and understanding random behavior in systems.

## Usage

### Getting Started

Simply load the healthyverse to get started:

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

This single command loads all seven core packages and displays: - ✅
Which packages were loaded and their versions - ⚠️ Any conflicts with
functions from other loaded packages

### Managing Conflicts

You'll get an automatic summary of function conflicts when you load the
healthyverse. You can check for conflicts at any time:

``` r
library(MASS)
healthyverse_conflicts()
#> ── Conflicts ─────────────────────────────────────── healthyverse_conflicts() ──
#> ✖ tidyAML::check_duplicate_rows() masks TidyDensity::check_duplicate_rows()
#> ✖ tidyAML::quantile_normalize()   masks TidyDensity::quantile_normalize()
```

### Keeping Packages Updated

Check if any healthyverse packages need updating:

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

### Getting a System Report

Generate a comprehensive report of your R environment and healthyverse
package versions:

``` r
healthyverse_sitrep()
```

This is particularly useful when reporting bugs or asking for help, as
it provides all relevant version information.

## Example Workflows

Here are some common use cases for the healthyverse:

**Healthcare Data Analysis**

``` r
library(healthyverse)

# Analyze patient length of stay
# Calculate readmission rates  
# Generate healthcare metrics reports
```

**Time-Series Forecasting**

``` r
library(healthyverse)

# Load historical patient volume data
# Perform seasonal decomposition
# Generate forecasts with confidence intervals
# Visualize trends and predictions
```

**Automated Machine Learning**

``` r
library(healthyverse)

# Prepare healthcare dataset
# Automatically train multiple models
# Compare model performance
# Select best model for deployment
```

## Getting Help

If you encounter issues or have questions:

1.  **📖 Documentation**: Each package has extensive documentation at
    `https://www.spsanderson.com/{package-name}/`
2.  **🐛 Bug Reports**: File issues at
    [github.com/spsanderson/healthyverse/issues](https://github.com/spsanderson/healthyverse/issues)
3.  **💬 Discussions**: Use GitHub Discussions for questions and
    community support
4.  **📧 Contact**: Reach out to the maintainer at
    <spsanderson@gmail.com>

When asking for help, please run `healthyverse_sitrep()` and include the
output in your issue.

## Contributing

We welcome contributions! Here's how you can help:

- 🐛 **Report bugs** and suggest features via [GitHub
  Issues](https://github.com/spsanderson/healthyverse/issues)
- 📝 **Improve documentation** by submitting pull requests
- 🧪 **Add tests** to increase code coverage
- 💡 **Share use cases** and examples with the community
- ⭐ **Star the repo** to show your support

Please note that the healthyverse project is released with a
[Contributor Code of
Conduct](https://www.contributor-covenant.org/version/2/1/code_of_conduct/).
By contributing to this project, you agree to abide by its terms.

## Citation

If you use the healthyverse in your research or work, please cite it:

``` r
citation("healthyverse")
```

## Related Projects

The healthyverse is part of a broader ecosystem of R packages for
healthcare analytics:

- **[tidyverse](https://www.tidyverse.org/)** - The inspiration for
  healthyverse's design philosophy
- **[tidymodels](https://www.tidymodels.org/)** - Framework for modeling
  and machine learning
- **[forecast](https://pkg.robjhyndman.com/forecast/)** - Time-series
  forecasting methods

## Package Status

The healthyverse is in active development. The current version is stable
and suitable for production use. See [NEWS.md](NEWS.md) for detailed
change logs.

------------------------------------------------------------------------

**Author**: Steven P. Sanderson II, MPH  
**License**: MIT  
**Website**:
<https://www.spsanderson.com/healthyverse/>  
**ORCID**: [0009-0006-7661-8247](https://orcid.org/0009-0006-7661-8247)
