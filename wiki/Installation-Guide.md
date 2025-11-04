# Installation Guide

This guide covers everything you need to know about installing the healthyverse and its dependencies.

## Table of Contents

- [System Requirements](#system-requirements)
- [Installing from CRAN](#installing-from-cran)
- [Installing from GitHub](#installing-from-github)
- [Verifying Installation](#verifying-installation)
- [Installing Specific Packages](#installing-specific-packages)
- [Troubleshooting](#troubleshooting)

---

## System Requirements

### Minimum Requirements

- **R Version**: R ≥ 3.4.0 (R ≥ 4.1.0 recommended for native pipe support)
- **Operating System**: Windows, macOS, or Linux
- **Memory**: At least 4GB RAM (8GB+ recommended for large datasets)
- **Disk Space**: ~500MB for all packages and dependencies

### Recommended Setup

- **R Version**: R ≥ 4.3.0
- **RStudio**: Latest version (optional but recommended)
- **Memory**: 8GB+ RAM
- **Internet Connection**: Required for installation

---

## Installing from CRAN

The simplest way to install healthyverse is from CRAN (The Comprehensive R Archive Network):

```r
# Install the stable release
install.packages("healthyverse")
```

This will install:
- The healthyverse meta-package
- All 7 core packages:
  - healthyR
  - healthyR.data
  - healthyR.ts
  - healthyR.ai
  - TidyDensity
  - tidyAML
  - RandomWalker
- All required dependencies

### Installation Time

Typical installation takes 2-5 minutes depending on:
- Your internet connection speed
- Number of packages already installed
- System specifications

---

## Installing from GitHub

To get the latest development version with newest features:

```r
# Install devtools if you don't have it
install.packages("devtools")

# Install healthyverse from GitHub
devtools::install_github("spsanderson/healthyverse")
```

### Installing Development Versions of Core Packages

You can also install development versions of individual core packages:

```r
# Install all development versions
devtools::install_github("spsanderson/healthyR")
devtools::install_github("spsanderson/healthyR.data")
devtools::install_github("spsanderson/healthyR.ts")
devtools::install_github("spsanderson/healthyR.ai")
devtools::install_github("spsanderson/TidyDensity")
devtools::install_github("spsanderson/tidyAML")
devtools::install_github("spsanderson/RandomWalker")
```

### Building Vignettes

To install with vignettes (documentation examples):

```r
devtools::install_github("spsanderson/healthyverse", 
                         build_vignettes = TRUE)
```

**Note**: Building vignettes increases installation time significantly (5-15 minutes).

---

## Verifying Installation

After installation, verify everything is working:

```r
# Load the healthyverse
library(healthyverse)

# You should see output like:
# ── Attaching packages ───────────────────────────── healthyverse 1.1.0.9000 ──
# ✔ healthyR      0.2.2          ✔ TidyDensity   1.5.0     
# ✔ healthyR.data 1.1.1          ✔ tidyAML       0.0.5
# ✔ healthyR.ts   0.3.0          ✔ RandomWalker  0.1.0     
# ✔ healthyR.ai   0.1.0
```

### Check Package Versions

```r
# Get a detailed system report
healthyverse_sitrep()

# Output shows:
# - R version
# - RStudio version (if applicable)
# - All package versions
# - Packages that need updating
```

### Test Basic Functionality

```r
# Test that packages are accessible
healthyverse_packages()

# Check for conflicts
healthyverse_conflicts()

# List dependencies
healthyverse_deps()
```

---

## Installing Specific Packages

If you only need specific healthyverse packages:

### For Healthcare Data Analysis
```r
install.packages("healthyR")
install.packages("healthyR.data")
```

### For Time Series Analysis
```r
install.packages("healthyR.ts")
```

### For Machine Learning
```r
install.packages("healthyR.ai")
install.packages("tidyAML")
```

### For Statistical Distributions
```r
install.packages("TidyDensity")
install.packages("RandomWalker")
```

---

## Troubleshooting

### Common Issues and Solutions

#### Issue: Package Installation Fails

**Solution**: Make sure you have the latest R version and try:

```r
# Update your package repository
update.packages(ask = FALSE)

# Then retry installation
install.packages("healthyverse")
```

#### Issue: Compilation Errors on macOS

**Solution**: Install Xcode Command Line Tools:

```bash
xcode-select --install
```

#### Issue: Compilation Errors on Windows

**Solution**: Install Rtools:

1. Download from: https://cran.r-project.org/bin/windows/Rtools/
2. Install following the wizard
3. Restart R/RStudio
4. Retry package installation

#### Issue: Compilation Errors on Linux

**Solution**: Install build essentials:

```bash
# For Debian/Ubuntu
sudo apt-get install build-essential
sudo apt-get install r-base-dev

# For Fedora/RHEL
sudo yum groupinstall "Development Tools"
```

#### Issue: Dependencies Not Installing

**Solution**: Install dependencies manually:

```r
# Install tidyverse dependencies
install.packages(c("dplyr", "purrr", "tibble", "rlang"))

# Install visualization dependencies
install.packages(c("ggplot2", "cli", "crayon"))

# Then retry healthyverse installation
install.packages("healthyverse")
```

#### Issue: Out of Memory Errors

**Solution**: Increase R memory limit:

```r
# On Windows
memory.limit(size = 16000)  # Set to 16GB

# On all platforms, close other applications and try:
install.packages("healthyverse", dependencies = TRUE)
```

#### Issue: Package Not Found on CRAN

**Solution**: The package might not be on CRAN yet. Install from GitHub:

```r
devtools::install_github("spsanderson/healthyverse")
```

---

## Updating healthyverse

### Check for Updates

```r
# Check if any packages need updating
healthyverse_update()
```

### Update All Packages

```r
# Update all healthyverse packages
update.packages(oldPkgs = healthyverse_packages())
```

### Update from GitHub

```r
# Update to latest development versions
devtools::install_github("spsanderson/healthyverse")
```

---

## Uninstalling

If you need to remove healthyverse:

```r
# Remove the meta-package
remove.packages("healthyverse")

# Optionally remove core packages
remove.packages(c("healthyR", "healthyR.data", "healthyR.ts", 
                  "healthyR.ai", "TidyDensity", "tidyAML", 
                  "RandomWalker"))
```

---

## Behind a Proxy

If you're behind a corporate proxy:

```r
# Set proxy environment variables
Sys.setenv(http_proxy = "http://proxy.company.com:8080")
Sys.setenv(https_proxy = "http://proxy.company.com:8080")

# Then install
install.packages("healthyverse")
```

---

## Offline Installation

For systems without internet access:

1. **On a connected system**, download packages:
   ```r
   download.packages("healthyverse", destdir = "~/healthyverse_pkgs",
                     type = "source")
   ```

2. **Transfer** the downloaded files to the offline system

3. **On the offline system**, install:
   ```r
   install.packages("~/healthyverse_pkgs/healthyverse_1.1.0.tar.gz",
                    repos = NULL, type = "source")
   ```

---

## Next Steps

After successful installation:

1. Read the [Quick Start Tutorial](Quick-Start-Tutorial.md)
2. Explore [Core Packages Overview](Core-Packages-Overview.md)
3. Try example workflows in [Healthcare Data Analysis](Healthcare-Data-Analysis.md)

---

## Getting Help

If you continue to have installation issues:

1. Run `healthyverse_sitrep()` and save the output
2. Check the [FAQ](FAQ.md) and [Troubleshooting](Troubleshooting.md) pages
3. Search existing [GitHub Issues](https://github.com/spsanderson/healthyverse/issues)
4. Create a new issue with:
   - Your `healthyverse_sitrep()` output
   - The exact error message
   - Steps to reproduce the problem

---

**Last Updated**: November 2025  
**Version**: 1.1.0.9000
