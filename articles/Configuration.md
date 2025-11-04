# Configuration

Guide to configuring the healthyverse for optimal performance and user experience.

## Table of Contents

- [Package Options](#package-options)
- [RStudio Configuration](#rstudio-configuration)
- [Performance Settings](#performance-settings)
- [Environment Variables](#environment-variables)
- [Project Setup](#project-setup)
- [Best Practices](#best-practices)

---

## Package Options

### Startup Messages

Control healthyverse startup messages:

```r
# Suppress all startup messages
options(healthyverse.quiet = TRUE)
library(healthyverse)

# Re-enable messages (default)
options(healthyverse.quiet = FALSE)
library(healthyverse)
```

### Conflict Management

```r
# Use conflicted package for explicit conflict resolution
library(conflicted)

# Set preferences before loading healthyverse
conflict_prefer("filter", "dplyr")
conflict_prefer("select", "dplyr")
conflict_prefer("check_duplicate_rows", "tidyAML")

library(healthyverse)
```

### Global Options

```r
# Set default CRAN mirror
options(repos = c(CRAN = "https://cloud.r-project.org"))

# Increase timeout for large downloads
options(timeout = 300)  # 5 minutes

# Set number of digits to print
options(digits = 4)

# Disable scientific notation
options(scipen = 999)
```

---

## RStudio Configuration

### .Rprofile

Create or edit `~/.Rprofile` for user-level settings:

```r
# ~/.Rprofile

# Set CRAN mirror
options(repos = c(CRAN = "https://cloud.r-project.org"))

# Customize startup
.First <- function() {
  cat("\nWelcome to R!\n")
  cat("Loading healthyverse...\n")
  suppressMessages(library(healthyverse))
  cat("healthyverse loaded successfully.\n\n")
}

# Customize closing
.Last <- function() {
  cat("\nGoodbye!\n")
}

# Useful aliases
h <- function() healthyverse_sitrep()
u <- function() healthyverse_update()
```

### Project-Level .Rprofile

For project-specific settings, create `.Rprofile` in project root:

```r
# .Rprofile (project level)

# Set project-specific options
options(
  healthyverse.quiet = FALSE,
  scipen = 999,
  digits = 4
)

# Load required packages
if (interactive()) {
  suppressMessages({
    require(healthyverse)
    require(here)
  })
  
  cat("Project environment loaded.\n")
  cat("Working directory:", getwd(), "\n")
}
```

### RStudio Preferences

**Tools > Global Options**:

1. **General**
   - Restore .RData: No
   - Save workspace: Never
   
2. **Code**
   - Use native pipe operator: Yes (R ≥ 4.1.0)
   - Auto-indent: Yes
   - Diagnostics: All enabled

3. **Appearance**
   - Choose theme that works with healthyverse colors

4. **Pane Layout**
   - Customize to your workflow

---

## Performance Settings

### Memory Management

```r
# Check current memory usage
pryr::mem_used()

# Check memory limit (Windows)
memory.limit()

# Increase memory limit (Windows)
memory.limit(size = 16000)  # 16GB

# Garbage collection
gc()

# Monitor memory during operations
library(profmem)
prof <- profmem({
  # Your code here
})
print(prof)
```

### Parallel Processing

```r
# Set up parallel backend
library(parallel)
library(doParallel)

# Detect cores
n_cores <- detectCores()
cat("Available cores:", n_cores, "\n")

# Use n-1 cores (leave one for system)
cl <- makeCluster(n_cores - 1)
registerDoParallel(cl)

# Your parallel code here

# Clean up
stopCluster(cl)

# Set default for future sessions
options(mc.cores = n_cores - 1)
```

### Data.table Settings

```r
# If using data.table with healthyverse
library(data.table)

# Set number of threads
setDTthreads(detectCores() - 1)

# Check current settings
getDTthreads()
```

---

## Environment Variables

### Setting Environment Variables

**In .Renviron file** (`~/.Renviron`):

```bash
# Database credentials
DB_HOST=localhost
DB_PORT=5432
DB_NAME=hospital_db
DB_USER=analyst
DB_PASS=secure_password

# API keys
API_KEY=your_api_key_here

# Custom paths
DATA_DIR=/path/to/data
OUTPUT_DIR=/path/to/output

# R configuration
R_MAX_VSIZE=16Gb
R_LIBS_USER=~/R/library
```

### Accessing Environment Variables

```r
# Read environment variables
db_host <- Sys.getenv("DB_HOST")
api_key <- Sys.getenv("API_KEY")

# Set environment variable in session
Sys.setenv(TEMP_VAR = "value")

# Check if variable exists
if (Sys.getenv("API_KEY") == "") {
  stop("API_KEY not set in environment")
}
```

---

## Project Setup

### Using renv for Dependency Management

```r
# Initialize renv in project
renv::init()

# Install healthyverse
install.packages("healthyverse")

# Snapshot current state
renv::snapshot()

# Restore from snapshot
renv::restore()

# Update packages
renv::update()

# Check status
renv::status()
```

### Directory Structure

```
project/
├── .Rprofile           # Project settings
├── .Renviron          # Environment variables
├── renv/              # renv files
├── data/              # Raw data
│   ├── raw/
│   └── processed/
├── R/                 # R scripts
│   ├── 01-import.R
│   ├── 02-clean.R
│   └── 03-analyze.R
├── output/            # Results
│   ├── figures/
│   └── tables/
├── models/            # Saved models
├── docs/              # Documentation
└── README.md
```

### Using here Package

```r
library(here)

# All paths relative to project root
data <- read.csv(here("data", "raw", "patients.csv"))
saveRDS(model, here("models", "readmission_model.rds"))
ggsave(here("output", "figures", "plot1.png"))
```

---

## Best Practices

### 1. Session Management

```r
# Always start with clean session
# Session > Restart R

# Check for loaded packages
search()

# Detach packages if needed
detach("package:old_package", unload = TRUE)

# Clear workspace
rm(list = ls())
gc()
```

### 2. Reproducibility

```r
# Always set seed for reproducibility
set.seed(123)

# Document session info
sessionInfo()
healthyverse_sitrep()

# Use renv for dependency management
renv::init()
renv::snapshot()
```

### 3. Code Organization

```r
# Load packages at the top
library(healthyverse)
library(here)

# Source helper functions
source(here("R", "helpers.R"))

# Set options
options(scipen = 999)

# Define constants
TRAIN_PROP <- 0.8
CV_FOLDS <- 10

# Your analysis code here
```

### 4. Error Handling

```r
# Set options for errors
options(
  error = rlang::entrace,
  rlang_backtrace_on_error = "full"
)

# Use tryCatch for production code
result <- tryCatch({
  # Your code
}, error = function(e) {
  message("Error occurred: ", e$message)
  NULL
})
```

### 5. Logging

```r
# Simple logging
log_file <- here("logs", paste0("analysis_", Sys.Date(), ".log"))

log_message <- function(msg) {
  timestamp <- format(Sys.time(), "%Y-%m-%d %H:%M:%S")
  cat(paste0("[", timestamp, "] ", msg, "\n"), 
      file = log_file, append = TRUE)
  message(msg)
}

# Use in code
log_message("Starting analysis")
log_message("Data loaded successfully")
```

---

## Configuration Templates

### Minimal .Rprofile

```r
# ~/.Rprofile (minimal)
options(
  repos = c(CRAN = "https://cloud.r-project.org"),
  healthyverse.quiet = FALSE
)

if (interactive()) {
  suppressMessages(require(healthyverse))
}
```

### Complete .Rprofile

```r
# ~/.Rprofile (complete)

# Set options
options(
  repos = c(CRAN = "https://cloud.r-project.org"),
  healthyverse.quiet = FALSE,
  scipen = 999,
  digits = 4,
  max.print = 100,
  timeout = 300,
  mc.cores = parallel::detectCores() - 1
)

# Custom startup
.First <- function() {
  if (interactive()) {
    # Load packages
    suppressMessages({
      require(healthyverse)
      require(here)
    })
    
    # Display info
    cat("\n")
    cat("R version:", R.version.string, "\n")
    cat("Working directory:", getwd(), "\n")
    cat("\n")
    
    # Helpful aliases
    .GlobalEnv$h <- function() healthyverse_sitrep()
    .GlobalEnv$u <- function() healthyverse_update()
  }
}

# Custom cleanup
.Last <- function() {
  if (interactive()) {
    cat("\nSaving workspace...\n")
    # Add any cleanup code
  }
}
```

### .Renviron Template

```bash
# ~/.Renviron

# R configuration
R_LIBS_USER=~/R/library
R_MAX_VSIZE=16Gb

# CRAN mirror
R_REPOS=https://cloud.r-project.org

# Database
DB_HOST=localhost
DB_PORT=5432
DB_NAME=hospital_db
DB_USER=analyst
DB_PASS=secure_password

# Paths
DATA_DIR=/path/to/data
OUTPUT_DIR=/path/to/output
MODEL_DIR=/path/to/models

# API Keys (DO NOT commit to git)
API_KEY=your_api_key
```

---

## Troubleshooting Configuration

### Check Current Configuration

```r
# Check options
options()

# Check specific option
getOption("repos")
getOption("healthyverse.quiet")

# Check environment variables
Sys.getenv()

# Check specific environment variable
Sys.getenv("R_LIBS_USER")

# Check library paths
.libPaths()

# Check loaded packages
loadedNamespaces()
```

### Reset Configuration

```r
# Reset options to defaults
options(repos = "@CRAN@")  # Use RStudio's default
options(healthyverse.quiet = NULL)

# Detach all packages
# (Note: Be careful, this affects current session)
lapply(paste0('package:', names(sessionInfo()$otherPkgs)), 
       detach, character.only = TRUE, unload = TRUE)

# Restart R session
# Session > Restart R (in RStudio)
```

---

## Platform-Specific Settings

### Windows

```r
# In .Rprofile
if (Sys.info()["sysname"] == "Windows") {
  memory.limit(size = 16000)
  options(download.file.method = "wininet")
}
```

### macOS

```r
# In .Rprofile
if (Sys.info()["sysname"] == "Darwin") {
  # macOS-specific settings
  options(device = "quartz")
}
```

### Linux

```r
# In .Rprofile
if (Sys.info()["sysname"] == "Linux") {
  # Linux-specific settings
  options(device = "X11")
}
```

---

## Next Steps

- Review [Performance Optimization](Performance-Optimization.md) for advanced tuning
- Check [Best Practices](Best-Practices.md) for workflow recommendations
- Explore [Development Setup](Development-Setup.md) for contribution

---

**Last Updated**: November 2025  
**Version**: 1.1.0.9000
