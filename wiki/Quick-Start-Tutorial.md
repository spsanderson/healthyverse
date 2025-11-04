# Quick Start Tutorial

This tutorial will get you up and running with the healthyverse in just a few minutes.

## Table of Contents

- [Your First Steps](#your-first-steps)
- [Loading the healthyverse](#loading-the-healthyverse)
- [Understanding the Output](#understanding-the-output)
- [Managing Conflicts](#managing-conflicts)
- [Working with Healthcare Data](#working-with-healthcare-data)
- [Time Series Analysis](#time-series-analysis)
- [Machine Learning Basics](#machine-learning-basics)
- [Next Steps](#next-steps)

---

## Your First Steps

### 1. Install healthyverse

If you haven't already, install the package:

```r
install.packages("healthyverse")
```

See the [Installation Guide](Installation-Guide.md) for more details.

### 2. Load the Package

```r
library(healthyverse)
```

You should see output like this:

```
── Attaching packages ───────────────────────────── healthyverse 1.1.0.9000 ──
✔ healthyR      0.2.2          ✔ TidyDensity   1.5.0     
✔ healthyR.data 1.1.1          ✔ tidyAML       0.0.5
✔ healthyR.ts   0.3.0          ✔ RandomWalker  0.1.0     
✔ healthyR.ai   0.1.0
── Conflicts ─────────────────────────────────── healthyverse_conflicts() ──
✖ tidyAML::check_duplicate_rows() masks TidyDensity::check_duplicate_rows()
✖ tidyAML::quantile_normalize()   masks TidyDensity::quantile_normalize()
```

🎉 Congratulations! All seven core packages are now loaded and ready to use.

---

## Loading the healthyverse

When you load `library(healthyverse)`, it automatically loads these packages:

| Package | Purpose |
|---------|---------|
| **healthyR** | Healthcare administrative data analysis |
| **healthyR.data** | Sample healthcare datasets |
| **healthyR.ts** | Time series analysis and forecasting |
| **healthyR.ai** | AI and machine learning for healthcare |
| **TidyDensity** | Statistical distributions and visualization |
| **tidyAML** | Automated machine learning (AutoML) |
| **RandomWalker** | Random walk simulations |

---

## Understanding the Output

### Package Versions

The output shows which version of each package was loaded:

```
✔ healthyR      0.2.2
```

- ✔ Green checkmark = successfully loaded
- Version number helps with reproducibility
- Red numbers indicate development versions

### Conflicts Warning

```
✖ tidyAML::check_duplicate_rows() masks TidyDensity::check_duplicate_rows()
```

This tells you when multiple packages have functions with the same name. The package loaded last "wins" and its function will be used by default.

---

## Managing Conflicts

### Checking for Conflicts

You can check for conflicts anytime:

```r
healthyverse_conflicts()
```

### Resolving Conflicts

If you need a specific version of a function:

```r
# Use explicit package reference
result1 <- TidyDensity::check_duplicate_rows(data)
result2 <- tidyAML::check_duplicate_rows(data)
```

### Avoiding Conflict Messages

If you don't want to see conflict messages at startup:

```r
# Suppress messages
options(healthyverse.quiet = TRUE)
library(healthyverse)
```

---

## Working with Healthcare Data

### Example 1: Load Sample Data

```r
# healthyR.data comes with sample datasets
library(healthyverse)

# List available datasets
data(package = "healthyR.data")

# Load a sample dataset (example)
# Note: Actual dataset names may vary based on healthyR.data version
```

### Example 2: Calculate Length of Stay

```r
# Simulated patient data
patient_data <- data.frame(
  patient_id = 1:5,
  admit_date = as.Date(c("2024-01-01", "2024-01-02", "2024-01-03", 
                         "2024-01-04", "2024-01-05")),
  discharge_date = as.Date(c("2024-01-05", "2024-01-08", "2024-01-10", 
                             "2024-01-06", "2024-01-12"))
)

# Calculate length of stay
patient_data <- patient_data %>%
  mutate(los = as.numeric(discharge_date - admit_date))

print(patient_data)
```

Output:
```
  patient_id admit_date discharge_date los
1          1 2024-01-01     2024-01-05   4
2          2 2024-01-02     2024-01-08   6
3          3 2024-01-03     2024-01-10   7
4          4 2024-01-04     2024-01-06   2
5          5 2024-01-05     2024-01-12   7
```

---

## Time Series Analysis

### Example: Basic Time Series

```r
# Create a simple time series
dates <- seq(as.Date("2024-01-01"), as.Date("2024-12-31"), by = "day")
patient_visits <- tibble(
  date = dates,
  visits = rpois(length(dates), lambda = 50) + 
           10 * sin(seq(0, 4*pi, length.out = length(dates)))
)

# View the data
head(patient_visits)

# Basic visualization
library(ggplot2)
ggplot(patient_visits, aes(x = date, y = visits)) +
  geom_line(color = "steelblue") +
  theme_minimal() +
  labs(title = "Daily Patient Visits",
       x = "Date", 
       y = "Number of Visits")
```

---

## Machine Learning Basics

### Example: Statistical Distributions

```r
# Generate random normal distributions
library(TidyDensity)

# Create distributions
dist_data <- tidy_normal(
  .n = 1000,
  .mean = 100,
  .sd = 15,
  .num_sims = 3
)

# Visualize
library(ggplot2)
ggplot(dist_data, aes(x = y, fill = factor(sim_number))) +
  geom_density(alpha = 0.5) +
  theme_minimal() +
  labs(title = "Normal Distribution Samples",
       x = "Value", 
       y = "Density",
       fill = "Simulation")
```

### Example: Random Walk

```r
# Simulate a random walk
library(RandomWalker)

# Create random walk simulation
rw_data <- RandomWalker::rw_tibble(
  .num_walks = 5,
  .steps = 100,
  .initial_value = 100
)

# Visualize
ggplot(rw_data, aes(x = step, y = value, color = factor(walk_number))) +
  geom_line() +
  theme_minimal() +
  labs(title = "Random Walk Simulations",
       x = "Step", 
       y = "Value",
       color = "Walk #")
```

---

## Checking Your System

### Get a System Report

```r
healthyverse_sitrep()
```

This provides:
- Your R version
- RStudio version (if using RStudio)
- All healthyverse package versions
- Packages that need updating

### Check for Updates

```r
healthyverse_update()
```

This will:
- Check CRAN for newer versions
- List packages that need updating
- Provide update instructions

---

## Common Patterns

### Pattern 1: Data Pipeline

```r
library(healthyverse)

# Typical workflow
result <- patient_data %>%
  filter(!is.na(discharge_date)) %>%
  mutate(los = discharge_date - admit_date) %>%
  group_by(department) %>%
  summarise(
    avg_los = mean(los),
    total_patients = n()
  )
```

### Pattern 2: Iterative Analysis

```r
# Using purrr for iteration
library(healthyverse)

results <- list(model1_data, model2_data, model3_data) %>%
  map(~ fit_model(.x)) %>%
  map_dfr(~ extract_metrics(.x), .id = "model")
```

### Pattern 3: Safe Operations

```r
# Using safely() for error handling
library(healthyverse)

safe_process <- safely(process_patient_data)

results <- patient_files %>%
  map(safe_process) %>%
  transpose()

# Check for errors
errors <- results$error %>% 
  compact()
```

---

## Tips for Success

### 1. Start Simple
Begin with basic operations before moving to complex workflows.

### 2. Use the Pipe
The pipe operator (`%>%` or `|>`) makes code more readable:

```r
# Instead of this:
result <- summarise(group_by(filter(data, x > 5), category), mean = mean(value))

# Write this:
result <- data %>%
  filter(x > 5) %>%
  group_by(category) %>%
  summarise(mean = mean(value))
```

### 3. Check Package Versions
Run `healthyverse_sitrep()` regularly to ensure packages are up-to-date.

### 4. Use Explicit References
When in doubt about which function to use:

```r
# Explicit package reference
result <- dplyr::filter(data, condition)
```

### 5. Read the Documentation
Each package has detailed documentation:

```r
# Get help for a package
?healthyR

# Get help for a function
?healthyverse_update

# Browse vignettes
browseVignettes("healthyverse")
```

---

## Common Gotchas

### Issue 1: Function Conflicts

**Problem**: Wrong function is called due to name conflicts.

**Solution**: Use `package::function()` syntax:
```r
TidyDensity::check_duplicate_rows(data)
```

### Issue 2: Package Not Loading

**Problem**: Error when loading healthyverse.

**Solution**: Check if all dependencies are installed:
```r
healthyverse_deps()
```

### Issue 3: Old Package Versions

**Problem**: Functions behave unexpectedly.

**Solution**: Update packages:
```r
healthyverse_update()
```

---

## Next Steps

Now that you're familiar with the basics, explore:

1. **[Core Packages Overview](Core-Packages-Overview.md)** - Deep dive into each package
2. **[Healthcare Data Analysis](Healthcare-Data-Analysis.md)** - Advanced healthcare workflows
3. **[Time Series Forecasting](Time-Series-Forecasting.md)** - Forecasting techniques
4. **[Machine Learning Workflows](Machine-Learning-Workflows.md)** - AutoML and modeling
5. **[API Reference](API-Reference.md)** - Complete function reference

---

## Practice Exercises

### Exercise 1: Basic Data Manipulation

Create a dataset, calculate summary statistics, and visualize results.

```r
library(healthyverse)

# Your code here
```

### Exercise 2: Time Series

Generate a time series with trend and seasonality, then visualize it.

```r
library(healthyverse)

# Your code here
```

### Exercise 3: Distributions

Generate multiple distributions and compare them visually.

```r
library(healthyverse)

# Your code here
```

---

## Getting Help

If you get stuck:

1. **Check the documentation**: `?function_name`
2. **Browse vignettes**: `browseVignettes("package_name")`
3. **Search the FAQ**: [FAQ](FAQ.md)
4. **Ask the community**: [GitHub Discussions](https://github.com/spsanderson/healthyverse/discussions)
5. **Report bugs**: [GitHub Issues](https://github.com/spsanderson/healthyverse/issues)

---

**Last Updated**: November 2025  
**Version**: 1.1.0.9000
