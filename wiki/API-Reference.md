# API Reference

Complete reference for all healthyverse meta-package functions.

## Table of Contents

- [Package Management](#package-management)
- [Conflict Management](#conflict-management)
- [System Information](#system-information)
- [Utility Functions](#utility-functions)
- [Operators](#operators)

---

## Package Management

### `healthyverse_packages()`

List all packages in the healthyverse.

#### Usage

```r
healthyverse_packages(include_self = TRUE)
```

#### Arguments

- `include_self`: Logical. Include "healthyverse" in the list? Default: `TRUE`

#### Value

Character vector of package names.

#### Examples

```r
# List all packages including healthyverse
healthyverse_packages()
# Returns: c("healthyR", "healthyR.data", "healthyR.ts", "healthyR.ai", 
#            "TidyDensity", "tidyAML", "RandomWalker", "healthyverse")

# List only core packages
healthyverse_packages(include_self = FALSE)
# Returns: c("healthyR", "healthyR.data", "healthyR.ts", "healthyR.ai", 
#            "TidyDensity", "tidyAML", "RandomWalker")
```

---

### `healthyverse_deps()`

List all healthyverse dependencies.

#### Usage

```r
healthyverse_deps(recursive = FALSE, repos = getOption("repos"))
```

#### Arguments

- `recursive`: Logical. If `TRUE`, also list all dependencies of healthyverse packages. Default: `FALSE`
- `repos`: Character. The repositories to use to check for updates. Default: `getOption("repos")`

#### Value

A tibble with columns:
- `package`: Package name
- `cran`: CRAN version
- `local`: Locally installed version
- `behind`: Logical indicating if local version is behind CRAN

#### Examples

```r
# Check core dependencies
deps <- healthyverse_deps()
print(deps)

# Check all dependencies recursively
all_deps <- healthyverse_deps(recursive = TRUE)
nrow(all_deps)  # Shows total number of dependencies

# Filter packages that are behind
library(dplyr)
deps %>% filter(behind)
```

---

### `healthyverse_update()`

Update healthyverse packages.

#### Usage

```r
healthyverse_update(recursive = FALSE, repos = getOption("repos"))
```

#### Arguments

- `recursive`: Logical. If `TRUE`, also check dependencies. Default: `FALSE`
- `repos`: Character. The repositories to use. Default: `getOption("repos")`

#### Value

Invisible. Used for side effects (printing update information).

#### Details

This function checks if healthyverse packages are up-to-date and provides instructions for updating. It does NOT automatically install updates; instead, it prints the packages that need updating and the command to update them.

#### Examples

```r
# Check for updates
healthyverse_update()

# Output if updates available:
# The following packages are out of date:
# 
#  * healthyR (0.2.1 -> 0.2.2)
#  * healthyR.ts (0.2.9 -> 0.3.0)
# 
# Start a clean R session then run:
# install.packages(c("healthyR", "healthyR.ts"))

# Check dependencies too
healthyverse_update(recursive = TRUE)
```

#### Notes

- Always start a clean R session before updating packages
- Close all R sessions and RStudio before major updates
- Some packages may require R restart after update

---

## Conflict Management

### `healthyverse_conflicts()`

Identify conflicts between healthyverse packages and other loaded packages.

#### Usage

```r
healthyverse_conflicts()
```

#### Arguments

None.

#### Value

An object of class `healthyverse_conflicts` containing a list of conflicting functions.

#### Details

This function lists all conflicts between packages in the healthyverse and other packages you have loaded. It deliberately ignores some dplyr conflicts (`intersect`, `union`, `setequal`, `setdiff`) that make base equivalents generic without negatively affecting existing code.

#### Examples

```r
# Check conflicts
healthyverse_conflicts()

# After loading MASS
library(MASS)
healthyverse_conflicts()

# Conflicts are printed automatically on package load
library(healthyverse)
```

#### Output Format

```
── Conflicts ─────────────────────────────────── healthyverse_conflicts() ──
✖ tidyAML::check_duplicate_rows() masks TidyDensity::check_duplicate_rows()
✖ tidyAML::quantile_normalize()   masks TidyDensity::quantile_normalize()
```

#### Handling Conflicts

```r
# Use explicit package reference
result1 <- TidyDensity::check_duplicate_rows(data)
result2 <- tidyAML::check_duplicate_rows(data)

# Or use conflicted package
library(conflicted)
conflict_prefer("check_duplicate_rows", "tidyAML")
```

---

## System Information

### `healthyverse_sitrep()`

Get a situation report on the healthyverse.

#### Usage

```r
healthyverse_sitrep()
```

#### Arguments

None.

#### Value

Invisible. Prints system and package information.

#### Details

This function provides a quick overview of:
- R version
- RStudio version (if applicable)
- All healthyverse package versions
- Whether packages are up-to-date

It's primarily designed to help when debugging problems or asking for help.

#### Examples

```r
# Get system report
healthyverse_sitrep()
```

#### Sample Output

```
── R & RStudio ──────────────────────────────────────────────────────────
• RStudio: 2023.12.1.402
• R: 4.3.2

── Core packages ────────────────────────────────────────────────────────
• healthyR      (0.2.2)
• healthyR.data (1.1.1)
• healthyR.ts   (0.3.0)
• healthyR.ai   (0.1.0)
• TidyDensity   (1.5.0)
• tidyAML       (0.0.5)
• RandomWalker  (0.1.0)

── Non-core packages ────────────────────────────────────────────────────
• dplyr         (1.1.4)
• purrr         (1.0.2)
• tibble        (3.2.1)
• rlang         (1.1.4)
• magrittr      (2.0.3)
• crayon        (1.5.3)
• cli           (3.6.3)
```

#### When to Use

- Before reporting a bug
- When asking for help
- After updating packages
- For documentation purposes

---

## Utility Functions

### `%>%` (Pipe Operator)

The magrittr pipe operator for chaining operations.

#### Usage

```r
lhs %>% rhs
```

#### Details

The pipe operator forwards the left-hand side value to the first argument of the right-hand side function. This makes code more readable by avoiding nested function calls.

#### Examples

```r
library(healthyverse)

# Without pipe
result <- summarise(group_by(filter(data, x > 5), category), mean = mean(value))

# With pipe
result <- data %>%
  filter(x > 5) %>%
  group_by(category) %>%
  summarise(mean = mean(value))
```

#### Alternative

R 4.1.0+ includes a native pipe operator `|>`:

```r
# Native pipe (R >= 4.1.0)
result <- data |>
  filter(x > 5) |>
  group_by(category) |>
  summarise(mean = mean(value))
```

---

## Operators

### Tidy Evaluation Operators

The healthyverse re-exports several operators from rlang for tidy evaluation:

#### `:=` (Definition Operator)

Used for assigning names programmatically in dplyr.

```r
library(healthyverse)

var_name <- "new_column"
data %>%
  mutate({{var_name}} := value * 2)
```

#### `!!` (Bang-Bang Operator)

Unquotes an expression.

```r
library(healthyverse)

my_var <- quo(mpg)
mtcars %>%
  select(!!my_var)
```

#### `!!!` (Big Bang Operator)

Unquotes and splices a list of expressions.

```r
library(healthyverse)

group_vars <- quos(cyl, gear)
mtcars %>%
  group_by(!!!group_vars) %>%
  summarise(mean_mpg = mean(mpg))
```

#### `.data` and `.env`

Pronouns for data-variables and environment-variables.

```r
library(healthyverse)

threshold <- 20

mtcars %>%
  filter(.data$mpg > .env$threshold)
```

---

## Package Options

### Startup Options

#### `healthyverse.quiet`

Suppress package startup messages.

```r
# Before loading
options(healthyverse.quiet = TRUE)
library(healthyverse)

# No startup messages will be shown
```

Default: `FALSE`

---

## Internal Functions

The following functions are primarily for internal use but may be useful for advanced users:

### Package Attachment

- `healthyverse_attach()`: Attach core packages
- `core_unloaded()`: List unloaded core packages
- `same_library()`: Attach package from same library location

### Conflict Detection

- `confirm_conflict()`: Confirm if functions actually conflict
- `ls_env()`: List objects in an environment
- `invert()`: Invert a named list

### Utilities

- `msg()`: Print messages with color support
- `text_col()`: Get appropriate text color for RStudio theme
- `style_grey()`: Style text in grey
- `package_version()`: Get formatted package version

---

## See Also

### Related Documentation

- [Quick Start Tutorial](Quick-Start-Tutorial.md) - Getting started guide
- [Core Packages Overview](Core-Packages-Overview.md) - Details on each package
- [Troubleshooting](Troubleshooting.md) - Common issues and solutions

### External Resources

- **tidyverse**: https://www.tidyverse.org/
- **tidymodels**: https://www.tidymodels.org/
- **rlang**: https://rlang.r-lib.org/

---

## Examples

### Complete Workflow Example

```r
# Load healthyverse
library(healthyverse)

# Check system status
healthyverse_sitrep()

# List all packages
packages <- healthyverse_packages()
print(packages)

# Check for conflicts
conflicts <- healthyverse_conflicts()

# Check dependencies
deps <- healthyverse_deps()

# Filter outdated packages
library(dplyr)
outdated <- deps %>%
  filter(behind) %>%
  pull(package)

if (length(outdated) > 0) {
  message("Outdated packages: ", paste(outdated, collapse = ", "))
  healthyverse_update()
}
```

### Conflict Resolution Example

```r
library(healthyverse)
library(MASS)  # Has select() which conflicts with dplyr

# Check conflicts
healthyverse_conflicts()

# Use explicit references
result1 <- dplyr::select(data, column1, column2)
result2 <- MASS::select(data, formula)

# Or use conflicted package
library(conflicted)
conflict_prefer("select", "dplyr")
```

---

## Function Index

Quick reference of all functions:

| Function | Purpose |
|----------|---------|
| `healthyverse_packages()` | List healthyverse packages |
| `healthyverse_deps()` | List package dependencies |
| `healthyverse_update()` | Check for package updates |
| `healthyverse_conflicts()` | Show function conflicts |
| `healthyverse_sitrep()` | System and package report |
| `%>%` | Pipe operator |

---

**Last Updated**: November 2025  
**Version**: 1.1.0.9000
