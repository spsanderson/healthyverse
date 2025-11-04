# Core Packages Overview

The healthyverse consists of seven core packages, each designed for specific aspects of healthcare data analysis and statistical modeling.

## Table of Contents

- [Package Summary](#package-summary)
- [healthyR](#healthyr)
- [healthyR.data](#healthyrdata)
- [healthyR.ts](#healthyrts)
- [healthyR.ai](#healthyrai)
- [TidyDensity](#tidydensity)
- [tidyAML](#tidyaml)
- [RandomWalker](#randomwalker)
- [Package Relationships](#package-relationships)

---

## Package Summary

| Package | Version | Purpose | Key Features |
|---------|---------|---------|--------------|
| **healthyR** | 0.2.2+ | Healthcare data analysis | LOS, readmissions, metrics |
| **healthyR.data** | 1.1.1+ | Sample datasets | Educational data, examples |
| **healthyR.ts** | 0.3.0+ | Time series analysis | Forecasting, decomposition |
| **healthyR.ai** | 0.1.0+ | AI/ML for healthcare | Model building, evaluation |
| **TidyDensity** | 1.5.0+ | Statistical distributions | Distribution generation, viz |
| **tidyAML** | 0.0.5+ | Automated ML | AutoML, model comparison |
| **RandomWalker** | 0.1.0+ | Random walk simulations | Stochastic processes |

---

## healthyR

**Healthcare data analysis tools focused on administrative data**

### Website
https://www.spsanderson.com/healthyR/

### Description
healthyR provides functions for analyzing common data problems in healthcare administrative data. It's designed to help healthcare analysts and data scientists quickly calculate important metrics and identify patterns in patient data.

### Key Features

- **Length of Stay (LOS) Analysis**: Calculate and visualize patient length of stay
- **Readmission Rates**: Track and analyze hospital readmissions
- **Healthcare Metrics**: Calculate common healthcare performance indicators
- **Data Quality**: Tools for identifying and handling missing data
- **Visualization**: Healthcare-specific plotting functions

### Common Functions

```r
# Example usage (specific functions depend on package version)
library(healthyR)

# Calculate length of stay
# los_summary(patient_data)

# Analyze readmissions
# readmission_rate(patient_data)

# Generate healthcare reports
# healthcare_metrics(patient_data)
```

### Use Cases

- Hospital performance analysis
- Quality improvement initiatives
- Patient flow optimization
- Resource utilization studies
- Administrative reporting

### Dependencies
Works seamlessly with dplyr, ggplot2, and other tidyverse packages.

---

## healthyR.data

**Simulated healthcare datasets for testing and learning**

### Website
https://www.spsanderson.com/healthyR.data/

### Description
healthyR.data provides simulated healthcare datasets that are perfect for:
- Learning healthcare analytics
- Testing new analysis techniques
- Developing proof-of-concept analyses
- Training and education
- Creating reproducible examples

### Key Features

- **Realistic Data**: Simulated datasets that mirror real healthcare data structures
- **Privacy-Safe**: Completely synthetic data with no real patient information
- **Variety**: Multiple dataset types covering different healthcare scenarios
- **Ready-to-Use**: Pre-cleaned and formatted for immediate analysis
- **Educational**: Includes examples and documentation

### Available Datasets

```r
library(healthyR.data)

# List all available datasets
data(package = "healthyR.data")

# Load a dataset
# data(dataset_name)
```

### Common Dataset Types

- Patient admission records
- Emergency department visits
- Surgical procedures
- Laboratory results
- Billing and financial data

### Use Cases

- Healthcare analytics training
- Algorithm development
- Statistical method testing
- Documentation examples
- Classroom instruction

---

## healthyR.ts

**Time series analysis and forecasting for healthcare data**

### Website
https://www.spsanderson.com/healthyR.ts/

### Description
healthyR.ts provides comprehensive time-series analysis and forecasting functions optimized for healthcare data patterns. It handles the unique characteristics of healthcare time series, including:
- Irregular patterns
- Multiple seasonality
- Trend changes
- Holiday effects

### Key Features

- **Automated Forecasting**: Multiple models with automatic selection
- **Seasonal Decomposition**: Separate trend, seasonal, and irregular components
- **Visualization**: Time series-specific plotting functions
- **Model Comparison**: Compare multiple forecasting approaches
- **Healthcare-Optimized**: Handles healthcare data patterns

### Common Workflows

```r
library(healthyR.ts)

# Time series forecasting workflow
# 1. Create time series object
# ts_data <- ts_auto_tbl(data)

# 2. Visualize patterns
# ts_plot(ts_data)

# 3. Generate forecasts
# forecasts <- ts_auto_forecast(ts_data)

# 4. Evaluate models
# ts_model_comparison(forecasts)
```

### Forecasting Methods

- ARIMA models
- Exponential smoothing
- Prophet
- Seasonal decomposition
- Ensemble methods

### Use Cases

- Patient volume forecasting
- Resource planning
- Capacity management
- Demand prediction
- Seasonal pattern analysis

---

## healthyR.ai

**AI and machine learning utilities for healthcare applications**

### Website
https://www.spsanderson.com/healthyR.ai/

### Description
healthyR.ai provides AI and machine learning utilities specifically designed for healthcare applications. It features automated model building, evaluation, and prediction workflows tailored to healthcare data challenges.

### Key Features

- **Automated Workflows**: Streamlined ML pipelines
- **Healthcare Focus**: Functions designed for healthcare use cases
- **Model Building**: Easy model creation and training
- **Evaluation Tools**: Comprehensive model assessment
- **Prediction Functions**: Make predictions on new data

### Common Applications

```r
library(healthyR.ai)

# Machine learning workflow
# 1. Prepare data
# data_split <- hai_split_data(patient_data)

# 2. Build model
# model <- hai_auto_model(data_split)

# 3. Evaluate performance
# metrics <- hai_evaluate_model(model)

# 4. Make predictions
# predictions <- hai_predict(model, new_data)
```

### Supported Models

- Logistic regression
- Random forests
- Gradient boosting
- Neural networks
- Support vector machines

### Use Cases

- Readmission prediction
- Risk stratification
- Diagnosis assistance
- Treatment recommendation
- Patient outcome prediction

---

## TidyDensity

**Generate and visualize probability distributions in tidy format**

### Website
https://www.spsanderson.com/TidyDensity/

### Description
TidyDensity makes it easy to work with, compare, and plot various statistical distributions. All distributions are generated in a tidy format, making them compatible with dplyr, ggplot2, and other tidyverse tools.

### Key Features

- **Multiple Distributions**: 20+ probability distributions
- **Tidy Format**: All output as tidy data frames
- **Visualization**: Built-in plotting functions
- **Comparison**: Easy comparison of distributions
- **Simulation**: Generate random samples

### Available Distributions

- Normal, Log-Normal
- Uniform, Beta, Gamma
- Exponential, Weibull
- Poisson, Negative Binomial
- Binomial, Bernoulli
- And many more...

### Common Usage

```r
library(TidyDensity)

# Generate normal distribution
normal_data <- tidy_normal(.n = 1000, .mean = 100, .sd = 15)

# Generate multiple simulations
multi_sim <- tidy_normal(.n = 1000, .mean = 100, .sd = 15, .num_sims = 5)

# Visualize
library(ggplot2)
ggplot(normal_data, aes(x = y)) +
  geom_density() +
  theme_minimal()

# Compare distributions
combined <- bind_rows(
  tidy_normal(.n = 1000) %>% mutate(dist = "Normal"),
  tidy_gamma(.n = 1000) %>% mutate(dist = "Gamma")
)

ggplot(combined, aes(x = y, color = dist)) +
  geom_density() +
  theme_minimal()
```

### Use Cases

- Statistical modeling
- Monte Carlo simulations
- Probability calculations
- Distribution comparison
- Educational demonstrations

---

## tidyAML

**Automated machine learning framework built on tidymodels**

### Website
https://www.spsanderson.com/tidyAML/

### Description
tidyAML streamlines the process of training, tuning, and comparing multiple models simultaneously. Built on top of tidymodels, it provides a high-level interface for automated machine learning workflows.

### Key Features

- **AutoML**: Automatically train multiple models
- **Model Comparison**: Compare model performance easily
- **Hyperparameter Tuning**: Automated parameter optimization
- **Tidymodels Integration**: Full tidymodels compatibility
- **Workflow Management**: Organized ML workflows

### Workflow Example

```r
library(tidyAML)

# AutoML workflow
# 1. Prepare data
data_split <- initial_split(data, prop = 0.8)

# 2. Train multiple models automatically
models <- fast_regression(
  .data = training(data_split),
  .rec_obj = recipe(outcome ~ ., data = training(data_split))
)

# 3. Compare models
model_comparison <- extract_model_comparison(models)

# 4. Select best model
best_model <- select_best_model(models, metric = "rmse")
```

### Supported Algorithms

- Linear/Logistic Regression
- Random Forest
- XGBoost
- Support Vector Machines
- K-Nearest Neighbors
- Neural Networks
- Elastic Net

### Use Cases

- Rapid model prototyping
- Model selection
- Baseline model creation
- Comparative analysis
- Production model development

---

## RandomWalker

**Simulate and analyze random walk processes**

### Website
https://www.spsanderson.com/RandomWalker/

### Description
RandomWalker provides tools for simulating and analyzing random walk processes. It's useful for modeling stochastic processes and understanding random behavior in systems.

### Key Features

- **Multiple Walk Types**: Various random walk implementations
- **Visualization**: Built-in plotting functions
- **Analysis Tools**: Statistical analysis of walks
- **Simulation**: Generate multiple walk instances
- **Tidy Output**: All results in tidy format

### Random Walk Types

- Simple random walk
- Brownian motion
- Geometric random walk
- Random walk with drift
- Biased random walks

### Common Usage

```r
library(RandomWalker)

# Generate random walk
walk_data <- rw_tibble(
  .num_walks = 10,
  .steps = 100,
  .initial_value = 100,
  .sd = 1
)

# Visualize
library(ggplot2)
ggplot(walk_data, aes(x = step, y = value, color = factor(walk_number))) +
  geom_line() +
  theme_minimal() +
  labs(title = "Random Walk Simulations")

# Analyze
walk_stats <- walk_data %>%
  group_by(walk_number) %>%
  summarise(
    final_value = last(value),
    max_value = max(value),
    min_value = min(value),
    volatility = sd(value)
  )
```

### Use Cases

- Financial modeling
- Population dynamics
- Stock price simulation
- Monte Carlo methods
- Teaching probability concepts

---

## Package Relationships

### Data Flow

```
healthyR.data → healthyR → healthyR.ts
                     ↓
              healthyR.ai
                     ↓
                 tidyAML

TidyDensity ←→ RandomWalker
```

### Integration Points

**healthyR + healthyR.data**
- Use sample data for testing healthyR functions
- Develop analysis pipelines with realistic data

**healthyR.ts + healthyR**
- Time series analysis of healthcare metrics
- Forecast healthcare indicators

**healthyR.ai + tidyAML**
- Combine automated ML with healthcare-specific functions
- Build and deploy healthcare prediction models

**TidyDensity + RandomWalker**
- Generate distributions for random walk parameters
- Analyze distributional properties of walks

### Common Workflows

**Complete Healthcare Analysis**
```r
library(healthyverse)

# 1. Load data (healthyR.data)
# 2. Calculate metrics (healthyR)
# 3. Analyze trends (healthyR.ts)
# 4. Build predictions (healthyR.ai/tidyAML)
```

**Statistical Modeling**
```r
library(healthyverse)

# 1. Generate distributions (TidyDensity)
# 2. Simulate processes (RandomWalker)
# 3. Build models (tidyAML)
```

---

## Version Compatibility

All packages are designed to work together. However:

- **Minimum R**: 3.4.0
- **Recommended R**: 4.3.0+
- **Native Pipe**: 4.1.0+ for `|>` support

Check compatibility:
```r
healthyverse_sitrep()
```

---

## Next Steps

- Explore [API Reference](API-Reference) for detailed function documentation
- Try [Healthcare Data Analysis](Healthcare-Data-Analysis) examples
- Learn [Time Series Forecasting](Time-Series-Forecasting) techniques
- Build models with [Machine Learning Workflows](Machine-Learning-Workflows)

---

**Last Updated**: November 2025  
**Version**: 1.1.0.9000
