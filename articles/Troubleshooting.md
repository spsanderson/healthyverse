# Troubleshooting Guide

Solutions to common issues when using the healthyverse.

## Table of Contents

- [Installation Issues](#installation-issues)
- [Loading Issues](#loading-issues)
- [Function Conflicts](#function-conflicts)
- [Performance Issues](#performance-issues)
- [Memory Problems](#memory-problems)
- [Error Messages](#error-messages)
- [Platform-Specific Issues](#platform-specific-issues)
- [Getting Help](#getting-help)

---

## Installation Issues

### Problem: Package Installation Fails

**Symptom**: Error messages during `install.packages("healthyverse")`

**Solutions**:

1. **Update R**:
   ```r
   # Check your R version
   R.version.string
   
   # If < 3.4.0, download latest R from CRAN
   ```

2. **Update packages**:
   ```r
   update.packages(ask = FALSE, checkBuilt = TRUE)
   ```

3. **Clear package cache**:
   ```r
   # Remove old packages
   update.packages(checkBuilt = TRUE)
   ```

4. **Check repository**:
   ```r
   # Ensure CRAN mirror is set
   options(repos = c(CRAN = "https://cloud.r-project.org"))
   install.packages("healthyverse")
   ```

### Problem: Compilation Errors

**Symptom**: "compilation failed for package 'X'"

**Solutions by Platform**:

**Windows**:
```r
# Install Rtools
# Download from: https://cran.r-project.org/bin/windows/Rtools/
# Then restart R and retry
```

**macOS**:
```bash
# Install Xcode Command Line Tools
xcode-select --install
```

**Linux (Ubuntu/Debian)**:
```bash
sudo apt-get update
sudo apt-get install build-essential
sudo apt-get install r-base-dev
sudo apt-get install libcurl4-openssl-dev libssl-dev libxml2-dev
```

**Linux (Fedora/RHEL)**:
```bash
sudo yum groupinstall "Development Tools"
sudo yum install libcurl-devel openssl-devel libxml2-devel
```

### Problem: Dependency Installation Fails

**Symptom**: "dependency 'X' is not available"

**Solutions**:

1. **Install dependencies manually**:
   ```r
   # Core dependencies
   install.packages(c("dplyr", "purrr", "tibble", "rlang", "magrittr"))
   
   # Then try healthyverse again
   install.packages("healthyverse")
   ```

2. **Check package availability**:
   ```r
   available.packages()[,"Package"]
   ```

3. **Install from archive** (if package was removed from CRAN):
   ```r
   # Find package on CRAN archive
   url <- "https://cran.r-project.org/src/contrib/Archive/package_name/"
   # Download and install manually
   ```

### Problem: Permission Denied

**Symptom**: "permission denied" or "cannot create directory"

**Solutions**:

1. **Run as administrator** (Windows) or use sudo (macOS/Linux)

2. **Install to user library**:
   ```r
   # Check library paths
   .libPaths()
   
   # Install to user library
   install.packages("healthyverse", lib = Sys.getenv("R_LIBS_USER"))
   ```

3. **Create user library**:
   ```r
   dir.create(Sys.getenv("R_LIBS_USER"), recursive = TRUE)
   .libPaths(Sys.getenv("R_LIBS_USER"))
   ```

---

## Loading Issues

### Problem: Package Not Loading

**Symptom**: `library(healthyverse)` fails

**Solutions**:

1. **Check installation**:
   ```r
   # Is it installed?
   "healthyverse" %in% installed.packages()[,"Package"]
   
   # Reinstall if FALSE
   install.packages("healthyverse")
   ```

2. **Check library paths**:
   ```r
   .libPaths()
   # Ensure package is in one of these directories
   ```

3. **Load explicitly**:
   ```r
   library(healthyverse, lib.loc = .libPaths()[1])
   ```

### Problem: Namespace Load Failed

**Symptom**: "Error: namespace 'X' is not available"

**Solutions**:

1. **Install missing namespace**:
   ```r
   install.packages("X")  # Replace X with package name
   ```

2. **Reinstall healthyverse**:
   ```r
   remove.packages("healthyverse")
   install.packages("healthyverse")
   ```

3. **Check for broken packages**:
   ```r
   # Find broken packages
   broken <- installed.packages()[,"Package"]
   lapply(broken, function(x) {
     tryCatch(
       library(x, character.only = TRUE),
       error = function(e) message(x, " is broken")
     )
   })
   ```

### Problem: Core Packages Not Loading

**Symptom**: Some healthyverse packages don't load

**Solutions**:

1. **Check which failed**:
   ```r
   library(healthyverse)
   # Look at startup messages
   
   # Try loading individually
   library(healthyR)
   library(healthyR.data)
   # etc.
   ```

2. **Install missing packages**:
   ```r
   install.packages(c("healthyR", "healthyR.data", "healthyR.ts", 
                     "healthyR.ai", "TidyDensity", "tidyAML", "RandomWalker"))
   ```

3. **Check versions**:
   ```r
   healthyverse_sitrep()
   ```

---

## Function Conflicts

### Problem: Wrong Function Being Called

**Symptom**: Function behaves unexpectedly or gives wrong results

**Solutions**:

1. **Check for conflicts**:
   ```r
   healthyverse_conflicts()
   ```

2. **Use explicit references**:
   ```r
   # Instead of:
   result <- select(data, columns)
   
   # Use:
   result <- dplyr::select(data, columns)
   ```

3. **Use conflicted package**:
   ```r
   library(conflicted)
   conflict_prefer("select", "dplyr")
   ```

4. **Check which function is active**:
   ```r
   # See which package the function comes from
   methods(select)
   ```

### Problem: Too Many Conflict Warnings

**Symptom**: Startup messages are overwhelming

**Solutions**:

1. **Suppress messages**:
   ```r
   options(healthyverse.quiet = TRUE)
   library(healthyverse)
   ```

2. **Load packages selectively**:
   ```r
   # Only load what you need
   library(healthyR)
   library(healthyR.ts)
   ```

3. **Resolve conflicts proactively**:
   ```r
   library(conflicted)
   conflict_prefer("filter", "dplyr")
   conflict_prefer("select", "dplyr")
   library(healthyverse)
   ```

---

## Performance Issues

### Problem: Code Runs Slowly

**Symptom**: Analysis takes too long

**Solutions**:

1. **Profile your code**:
   ```r
   library(profvis)
   profvis({
     # Your slow code here
   })
   ```

2. **Optimize data operations**:
   ```r
   # Use data.table for large data
   library(data.table)
   dt <- as.data.table(df)
   
   # Or use dtplyr
   library(dtplyr)
   result <- lazy_dt(df) %>%
     filter(condition) %>%
     as_tibble()
   ```

3. **Reduce unnecessary operations**:
   ```r
   # Bad: Repeated filtering
   df %>% filter(x > 5) %>% ... %>% filter(x > 5)
   
   # Good: Filter once
   df %>% filter(x > 5) %>% ...
   ```

4. **Use parallel processing**:
   ```r
   library(future)
   library(furrr)
   
   plan(multisession, workers = 4)
   results <- future_map(data_list, process_function)
   ```

### Problem: Machine Learning is Slow

**Symptom**: tidyAML or healthyR.ai models take too long

**Solutions**:

1. **Use parallel processing**:
   ```r
   library(doParallel)
   cl <- makeCluster(detectCores() - 1)
   registerDoParallel(cl)
   
   # Your model code
   
   stopCluster(cl)
   ```

2. **Sample your data**:
   ```r
   # Use subset for initial exploration
   train_sample <- slice_sample(training_data, prop = 0.1)
   ```

3. **Reduce hyperparameter grid**:
   ```r
   # Smaller grid for faster tuning
   # Adjust your tuning parameters
   ```

4. **Use simpler models first**:
   ```r
   # Start with linear models
   # Move to complex models only if needed
   ```

---

## Memory Problems

### Problem: Out of Memory Errors

**Symptom**: "cannot allocate vector of size X"

**Solutions**:

1. **Increase memory limit** (Windows):
   ```r
   memory.limit(size = 16000)  # 16GB
   ```

2. **Clear workspace**:
   ```r
   rm(list = ls())
   gc()  # Garbage collection
   ```

3. **Process in chunks**:
   ```r
   # Instead of loading all data
   process_chunk <- function(chunk) {
     # Process chunk
     result <- analyze(chunk)
     return(result)
   }
   
   results <- map(data_chunks, process_chunk)
   ```

4. **Use databases**:
   ```r
   library(DBI)
   library(RSQLite)
   
   con <- dbConnect(SQLite(), "data.db")
   # Query only what you need
   data <- dbGetQuery(con, "SELECT * FROM table WHERE condition")
   ```

5. **Monitor memory**:
   ```r
   library(pryr)
   
   mem_used()  # Current memory usage
   object_size(data)  # Size of specific object
   ```

### Problem: R Session Crashes

**Symptom**: R crashes without error message

**Solutions**:

1. **Update R and packages**:
   ```r
   update.packages(ask = FALSE, checkBuilt = TRUE)
   ```

2. **Check for corrupted packages**:
   ```r
   # Reinstall healthyverse
   remove.packages("healthyverse")
   install.packages("healthyverse")
   ```

3. **Disable parallel processing**:
   ```r
   # If crashes during parallel operations
   # Run sequentially instead
   ```

4. **Increase stack size**:
   ```r
   Sys.setenv(R_C_STACK_SIZE = 100000000)
   ```

---

## Error Messages

### Error: "object not found"

**Solutions**:

1. **Check object exists**:
   ```r
   ls()  # List all objects
   ```

2. **Check spelling and case**:
   ```r
   # R is case-sensitive
   mydata != MyData
   ```

3. **Load required package**:
   ```r
   library(healthyverse)
   ```

4. **Check scope**:
   ```r
   # Variable might be in different environment
   ```

### Error: "could not find function"

**Solutions**:

1. **Load required package**:
   ```r
   library(healthyverse)
   ```

2. **Check function name**:
   ```r
   # Use tab completion or ?function_name
   ```

3. **Use explicit reference**:
   ```r
   package::function_name(args)
   ```

4. **Install missing package**:
   ```r
   install.packages("package_name")
   ```

### Error: "non-numeric argument to binary operator"

**Solutions**:

1. **Check data types**:
   ```r
   str(data)
   class(variable)
   ```

2. **Convert to numeric**:
   ```r
   data$column <- as.numeric(data$column)
   ```

3. **Remove NA values**:
   ```r
   data <- na.omit(data)
   ```

### Error: "subscript out of bounds"

**Solutions**:

1. **Check dimensions**:
   ```r
   dim(data)
   nrow(data)
   ncol(data)
   ```

2. **Use safe indexing**:
   ```r
   # Instead of data[100, ]
   if (nrow(data) >= 100) {
     result <- data[100, ]
   }
   ```

3. **Check for empty data**:
   ```r
   if (nrow(data) > 0) {
     # Process data
   }
   ```

---

## Platform-Specific Issues

### Windows Issues

**Problem: Rtools not found**

```r
# Install Rtools from:
# https://cran.r-project.org/bin/windows/Rtools/

# Check if installed:
Sys.which("make")

# Should show path to make.exe
```

**Problem: Path too long**

```r
# Use shorter directory names
# Install R in C:\R instead of default path
```

### macOS Issues

**Problem: Compiler errors**

```bash
# Install Xcode Command Line Tools
xcode-select --install

# Or install full Xcode from App Store
```

**Problem: Library not loaded**

```bash
# Install Homebrew packages
brew install openssl libxml2 libgit2
```

### Linux Issues

**Problem: Missing system libraries**

```bash
# Ubuntu/Debian
sudo apt-get install \
  libcurl4-openssl-dev \
  libssl-dev \
  libxml2-dev \
  libfontconfig1-dev \
  libharfbuzz-dev libfribidi-dev

# Fedora/RHEL
sudo yum install \
  libcurl-devel \
  openssl-devel \
  libxml2-devel \
  fontconfig-devel
```

---

## Getting Help

### Before Asking for Help

1. **Run diagnostics**:
   ```r
   healthyverse_sitrep()
   sessionInfo()
   ```

2. **Create minimal example**:
   ```r
   # Simplify your code to smallest example that shows the problem
   library(healthyverse)
   
   # Minimal reproducible example
   data <- data.frame(x = 1:5, y = 6:10)
   # Code that produces error
   ```

3. **Search existing issues**:
   - GitHub Issues: https://github.com/spsanderson/healthyverse/issues
   - Stack Overflow: Tag `[r] [healthyverse]`

### How to Report Issues

Include in your report:

1. **System information**:
   ```r
   healthyverse_sitrep()
   ```

2. **Reproducible example**:
   ```r
   # Minimal code that reproduces the problem
   ```

3. **Expected behavior**: What you expected to happen

4. **Actual behavior**: What actually happened

5. **Error messages**: Complete error text

6. **Steps taken**: What you've already tried

### Where to Get Help

1. **GitHub Issues**: https://github.com/spsanderson/healthyverse/issues
2. **GitHub Discussions**: https://github.com/spsanderson/healthyverse/discussions
3. **Email**: spsanderson@gmail.com
4. **This Wiki**: Browse other pages for guidance

---

## Still Having Issues?

If you've tried these solutions and still have problems:

1. Create a [GitHub Issue](https://github.com/spsanderson/healthyverse/issues/new)
2. Include all diagnostic information
3. Provide a minimal reproducible example
4. Be patient and check back for responses

---

**Last Updated**: November 2025  
**Version**: 1.1.0.9000
