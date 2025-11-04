# Time Series Forecasting

Comprehensive guide to time series analysis and forecasting with healthyverse.

## Table of Contents

- [Introduction](#introduction)
- [Data Preparation](#data-preparation)
- [Exploratory Analysis](#exploratory-analysis)
- [Forecasting Methods](#forecasting-methods)
- [Model Evaluation](#model-evaluation)
- [Healthcare Applications](#healthcare-applications)
- [Advanced Techniques](#advanced-techniques)
- [Best Practices](#best-practices)

---

## Introduction

The healthyR.ts package provides comprehensive time-series analysis and forecasting capabilities optimized for healthcare data patterns.

### Why Time Series for Healthcare?

- **Patient Volume Forecasting**: Predict future admissions and visits
- **Resource Planning**: Anticipate staffing and equipment needs
- **Capacity Management**: Optimize bed utilization
- **Seasonal Patterns**: Identify and account for seasonality
- **Trend Analysis**: Detect long-term changes

### Key Features

- Multiple forecasting algorithms
- Automated model selection
- Seasonal decomposition
- Trend analysis
- Ensemble methods
- Healthcare-optimized defaults

---

## Data Preparation

### Creating Time Series Objects

```r
library(healthyverse)

# From data frame with date column
daily_visits <- tibble(
  date = seq(as.Date("2023-01-01"), as.Date("2024-12-31"), by = "day"),
  visits = rpois(length(date), lambda = 50)
)

# Convert to ts object
visits_ts <- ts(daily_visits$visits, 
                frequency = 365,  # Daily data
                start = c(2023, 1))

# For monthly data
monthly_admissions <- tibble(
  month = seq(as.Date("2020-01-01"), as.Date("2024-12-01"), by = "month"),
  admissions = rpois(length(month), lambda = 500)
)

admissions_ts <- ts(monthly_admissions$admissions,
                    frequency = 12,  # Monthly data
                    start = c(2020, 1))
```

### Handling Missing Values

```r
# Check for missing values
sum(is.na(visits_ts))

# Interpolate missing values
library(zoo)
visits_clean <- na.approx(visits_ts)

# Or use moving average
visits_clean <- na.aggregate(visits_ts, FUN = mean)

# Forward fill
visits_clean <- na.locf(visits_ts)
```

### Aggregating Data

```r
# Daily to weekly
weekly_visits <- daily_visits %>%
  mutate(week = floor_date(date, "week")) %>%
  group_by(week) %>%
  summarise(visits = sum(visits))

# Daily to monthly
monthly_visits <- daily_visits %>%
  mutate(month = floor_date(date, "month")) %>%
  group_by(month) %>%
  summarise(visits = sum(visits))
```

---

## Exploratory Analysis

### Visualizing Time Series

```r
library(ggplot2)

# Basic time series plot
ggplot(daily_visits, aes(x = date, y = visits)) +
  geom_line(color = "steelblue") +
  labs(title = "Daily Patient Visits",
       x = "Date", y = "Number of Visits") +
  theme_minimal()

# With trend line
ggplot(daily_visits, aes(x = date, y = visits)) +
  geom_line(color = "steelblue", alpha = 0.6) +
  geom_smooth(method = "loess", color = "red", se = FALSE) +
  labs(title = "Daily Patient Visits with Trend",
       x = "Date", y = "Number of Visits") +
  theme_minimal()

# Multiple series
combined_data <- bind_rows(
  daily_visits %>% mutate(type = "ED Visits"),
  daily_admissions %>% mutate(type = "Admissions")
)

ggplot(combined_data, aes(x = date, y = visits, color = type)) +
  geom_line() +
  labs(title = "Patient Volume by Type",
       x = "Date", y = "Count") +
  theme_minimal()
```

### Seasonal Decomposition

```r
# Decompose time series
decomposed <- decompose(visits_ts)

# Plot components
plot(decomposed)

# Or with ggplot
decomp_df <- data.frame(
  date = time(decomposed$seasonal),
  observed = as.numeric(visits_ts),
  trend = as.numeric(decomposed$trend),
  seasonal = as.numeric(decomposed$seasonal),
  random = as.numeric(decomposed$random)
)

# Plot each component
library(tidyr)
decomp_long <- decomp_df %>%
  pivot_longer(cols = c(observed, trend, seasonal, random),
               names_to = "component",
               values_to = "value")

ggplot(decomp_long, aes(x = date, y = value)) +
  geom_line(color = "steelblue") +
  facet_wrap(~component, scales = "free_y", ncol = 1) +
  labs(title = "Time Series Decomposition",
       x = "Time", y = "Value") +
  theme_minimal()
```

### Identifying Patterns

```r
# Check for trend
library(forecast)
summary(lm(visits ~ time(visits_ts)))

# Check for seasonality
monthplot(visits_ts)

# Autocorrelation
acf(visits_ts, main = "Autocorrelation Function")
pacf(visits_ts, main = "Partial Autocorrelation Function")

# Statistical tests
Box.test(visits_ts, type = "Ljung-Box")  # Test for autocorrelation
```

---

## Forecasting Methods

### ARIMA Models

```r
library(forecast)

# Automatic ARIMA selection
arima_model <- auto.arima(visits_ts)
summary(arima_model)

# Check residuals
checkresiduals(arima_model)

# Generate forecast
arima_forecast <- forecast(arima_model, h = 30)  # 30 periods ahead

# Plot forecast
autoplot(arima_forecast) +
  labs(title = "ARIMA Forecast",
       x = "Time", y = "Visits") +
  theme_minimal()

# Access forecast values
arima_forecast$mean  # Point forecasts
arima_forecast$lower  # Lower confidence bounds
arima_forecast$upper  # Upper confidence bounds
```

### Exponential Smoothing

```r
# Holt-Winters exponential smoothing
ets_model <- ets(visits_ts)
summary(ets_model)

# Generate forecast
ets_forecast <- forecast(ets_model, h = 30)

# Plot
autoplot(ets_forecast) +
  labs(title = "Exponential Smoothing Forecast",
       x = "Time", y = "Visits") +
  theme_minimal()
```

### Prophet (Facebook's Forecasting Tool)

```r
library(prophet)

# Prepare data (requires specific column names)
prophet_df <- data.frame(
  ds = daily_visits$date,
  y = daily_visits$visits
)

# Fit model
prophet_model <- prophet(prophet_df)

# Create future dataframe
future <- make_future_dataframe(prophet_model, periods = 30)

# Generate forecast
prophet_forecast <- predict(prophet_model, future)

# Plot
plot(prophet_model, prophet_forecast)
prophet_plot_components(prophet_model, prophet_forecast)
```

### Seasonal ARIMA (SARIMA)

```r
# For data with strong seasonality
sarima_model <- auto.arima(visits_ts, seasonal = TRUE)
summary(sarima_model)

# Forecast
sarima_forecast <- forecast(sarima_model, h = 30)

# Plot
autoplot(sarima_forecast) +
  labs(title = "SARIMA Forecast",
       x = "Time", y = "Visits") +
  theme_minimal()
```

### Ensemble Methods

```r
# Combine multiple forecasts
models <- list(
  arima = auto.arima(visits_ts),
  ets = ets(visits_ts),
  stlf = stlf(visits_ts)
)

# Generate forecasts from each model
forecasts <- lapply(models, function(m) forecast(m, h = 30))

# Ensemble (simple average)
ensemble_mean <- Reduce("+", lapply(forecasts, function(f) f$mean)) / length(forecasts)

# Plot ensemble
plot(visits_ts, xlim = c(start(visits_ts)[1], end(visits_ts)[1] + 0.1),
     ylim = range(c(visits_ts, ensemble_mean)))
lines(ensemble_mean, col = "red", lwd = 2)
legend("topleft", legend = "Ensemble Forecast", col = "red", lty = 1, lwd = 2)
```

---

## Model Evaluation

### Accuracy Metrics

```r
# Split data into train and test
train_size <- floor(0.8 * length(visits_ts))
train_ts <- window(visits_ts, end = time(visits_ts)[train_size])
test_ts <- window(visits_ts, start = time(visits_ts)[train_size + 1])

# Fit model on training data
model <- auto.arima(train_ts)

# Forecast test period
forecast_test <- forecast(model, h = length(test_ts))

# Calculate accuracy metrics
accuracy(forecast_test, test_ts)

# Common metrics:
# ME   - Mean Error
# RMSE - Root Mean Squared Error
# MAE  - Mean Absolute Error
# MAPE - Mean Absolute Percentage Error
# MASE - Mean Absolute Scaled Error
```

### Cross-Validation

```r
# Time series cross-validation
cv_results <- tsCV(visits_ts, forecastfunction = function(x, h) {
  forecast(auto.arima(x), h = h)
}, h = 7)  # 7-step ahead forecast

# Calculate RMSE
sqrt(mean(cv_results^2, na.rm = TRUE))

# Plot CV errors
plot(cv_results)
```

### Residual Diagnostics

```r
# Check residuals
checkresiduals(model)

# Manual checks
residuals <- residuals(model)

# Plot residuals
plot(residuals, main = "Residuals")
abline(h = 0, col = "red")

# Histogram of residuals
hist(residuals, breaks = 30, main = "Histogram of Residuals")

# Q-Q plot
qqnorm(residuals)
qqline(residuals, col = "red")

# Ljung-Box test
Box.test(residuals, type = "Ljung-Box")
```

---

## Healthcare Applications

### Patient Volume Forecasting

```r
# Daily ED visits forecast
library(healthyverse)

# Historical data
ed_visits <- tibble(
  date = seq(as.Date("2023-01-01"), as.Date("2024-12-31"), by = "day"),
  visits = rpois(length(date), lambda = 120) + 
           20 * sin(2 * pi * as.numeric(date) / 365) +  # Seasonal
           0.05 * as.numeric(date - min(date))  # Trend
)

# Create time series
ed_ts <- ts(ed_visits$visits, frequency = 365, start = c(2023, 1))

# Fit model
model <- auto.arima(ed_ts)

# Forecast next 30 days
forecast_ed <- forecast(model, h = 30)

# Create forecast dataframe
forecast_df <- data.frame(
  date = seq(max(ed_visits$date) + 1, by = "day", length.out = 30),
  forecast = as.numeric(forecast_ed$mean),
  lower_80 = as.numeric(forecast_ed$lower[, 1]),
  upper_80 = as.numeric(forecast_ed$upper[, 1]),
  lower_95 = as.numeric(forecast_ed$lower[, 2]),
  upper_95 = as.numeric(forecast_ed$upper[, 2])
)

# Visualize
ggplot() +
  geom_line(data = ed_visits, aes(x = date, y = visits), color = "steelblue") +
  geom_line(data = forecast_df, aes(x = date, y = forecast), color = "red") +
  geom_ribbon(data = forecast_df, 
              aes(x = date, ymin = lower_95, ymax = upper_95),
              alpha = 0.2, fill = "red") +
  labs(title = "ED Visits Forecast - Next 30 Days",
       x = "Date", y = "Number of Visits") +
  theme_minimal()
```

### Staffing Optimization

```r
# Forecast to determine staffing needs
# Assume 1 nurse per 10 patients

# Generate forecast
hourly_forecast <- forecast(hourly_census_ts, h = 168)  # 1 week

# Calculate required staff
staff_requirements <- data.frame(
  hour = 1:168,
  forecast_census = as.numeric(hourly_forecast$mean),
  min_staff = ceiling(as.numeric(hourly_forecast$lower[, 2]) / 10),
  expected_staff = ceiling(as.numeric(hourly_forecast$mean) / 10),
  max_staff = ceiling(as.numeric(hourly_forecast$upper[, 2]) / 10)
)

# Visualize staffing needs
ggplot(staff_requirements, aes(x = hour)) +
  geom_ribbon(aes(ymin = min_staff, ymax = max_staff), alpha = 0.3, fill = "blue") +
  geom_line(aes(y = expected_staff), color = "blue", size = 1) +
  labs(title = "Forecasted Staffing Requirements",
       x = "Hour", y = "Number of Nurses Needed") +
  theme_minimal()
```

### Capacity Planning

```r
# Long-term capacity forecast
# Monthly admissions for next 12 months

monthly_admissions_ts <- ts(monthly_admissions$admissions, 
                            frequency = 12, 
                            start = c(2020, 1))

# Fit model
capacity_model <- auto.arima(monthly_admissions_ts)

# Forecast 12 months
capacity_forecast <- forecast(capacity_model, h = 12)

# Calculate bed requirements (assume 3-day average LOS)
avg_los <- 3
bed_requirements <- as.numeric(capacity_forecast$mean) * avg_los / 30

# Create summary
capacity_summary <- data.frame(
  month = seq(as.Date("2025-01-01"), by = "month", length.out = 12),
  forecast_admissions = as.numeric(capacity_forecast$mean),
  required_beds = ceiling(bed_requirements),
  lower_95 = as.numeric(capacity_forecast$lower[, 2]),
  upper_95 = as.numeric(capacity_forecast$upper[, 2])
)

print(capacity_summary)
```

---

## Advanced Techniques

### Handling Multiple Seasonality

```r
# For data with multiple seasonal patterns (e.g., daily + weekly)
library(forecast)

# Use msts (multiple seasonal time series)
multi_season_ts <- msts(daily_visits$visits, 
                        seasonal.periods = c(7, 365))  # Weekly and yearly

# Fit TBATS model (good for multiple seasonality)
tbats_model <- tbats(multi_season_ts)

# Forecast
tbats_forecast <- forecast(tbats_model, h = 30)

# Plot
autoplot(tbats_forecast) +
  labs(title = "TBATS Forecast (Multiple Seasonality)",
       x = "Time", y = "Visits") +
  theme_minimal()
```

### External Regressors

```r
# Include external variables (e.g., holidays, weather)
library(forecast)

# Create regressor matrix
holidays <- c(0, 0, 1, 0, 0, ...)  # 1 for holidays, 0 otherwise
temperature <- c(70, 72, 75, ...)

xreg <- cbind(holidays, temperature)

# Fit ARIMA with external regressors
model_xreg <- auto.arima(visits_ts, xreg = xreg)

# Forecast (need future values of regressors)
future_holidays <- c(1, 0, 0, ...)
future_temp <- c(80, 82, 78, ...)
future_xreg <- cbind(future_holidays, future_temp)

forecast_xreg <- forecast(model_xreg, xreg = future_xreg)
```

### Intervention Analysis

```r
# Model sudden changes (e.g., policy changes, COVID-19)

# Create intervention variable
intervention <- rep(0, length(visits_ts))
intervention[date >= "2020-03-01"] <- 1  # COVID-19 start

# Include in model
model_intervention <- auto.arima(visits_ts, xreg = intervention)

# The coefficient shows the intervention effect
coef(model_intervention)
```

---

## Best Practices

### 1. Data Quality

```r
# Always check data quality first
summary(visits_ts)
plot(visits_ts)

# Look for:
# - Outliers
# - Missing values
# - Structural breaks
# - Data entry errors
```

### 2. Model Selection

- Start simple (e.g., naive forecast)
- Compare multiple models
- Use cross-validation
- Consider domain knowledge
- Don't over-fit

### 3. Forecast Horizon

- Short-term (days/weeks): More accurate
- Medium-term (months): Moderate accuracy
- Long-term (years): Less accurate

```r
# Adjust model based on horizon
short_term <- forecast(model, h = 7)    # 1 week
medium_term <- forecast(model, h = 90)  # 3 months
long_term <- forecast(model, h = 365)   # 1 year
```

### 4. Uncertainty Communication

```r
# Always show confidence intervals
autoplot(forecast_result) +
  labs(title = "Forecast with 80% and 95% Confidence Intervals") +
  theme_minimal()

# Report uncertainty
cat("Forecast:", round(forecast_result$mean[1]), "\n")
cat("95% CI: [", round(forecast_result$lower[1, 2]), ",", 
    round(forecast_result$upper[1, 2]), "]\n")
```

### 5. Regular Updates

```r
# Update model with new data
update_forecast <- function(historical_data, new_data, horizon) {
  # Combine data
  updated_data <- c(historical_data, new_data)
  
  # Refit model
  model <- auto.arima(updated_data)
  
  # Generate new forecast
  forecast <- forecast(model, h = horizon)
  
  return(forecast)
}
```

---

## Complete Example

```r
library(healthyverse)
library(forecast)
library(ggplot2)

# 1. Load and prepare data
data <- read.csv("daily_admissions.csv") %>%
  mutate(date = as.Date(date))

# 2. Create time series
admissions_ts <- ts(data$admissions, frequency = 365, start = c(2023, 1))

# 3. Explore data
plot(admissions_ts)
decompose(admissions_ts) %>% plot()

# 4. Fit multiple models
models <- list(
  arima = auto.arima(admissions_ts),
  ets = ets(admissions_ts),
  prophet = prophet(data.frame(ds = data$date, y = data$admissions))
)

# 5. Generate forecasts
horizon <- 30
forecasts <- lapply(models[1:2], function(m) forecast(m, h = horizon))

# 6. Evaluate accuracy (using last 30 days as test)
train_data <- window(admissions_ts, end = time(admissions_ts)[length(admissions_ts) - 30])
test_data <- window(admissions_ts, start = time(admissions_ts)[length(admissions_ts) - 29])

train_models <- list(
  arima = auto.arima(train_data),
  ets = ets(train_data)
)

test_forecasts <- lapply(train_models, function(m) forecast(m, h = 30))
accuracies <- lapply(test_forecasts, function(f) accuracy(f, test_data))

# 7. Select best model and forecast
best_model <- train_models[[which.min(sapply(accuracies, function(a) a[2, "RMSE"]))]]
final_forecast <- forecast(best_model, h = horizon)

# 8. Visualize and report
autoplot(final_forecast) +
  labs(title = "Daily Admissions Forecast - Next 30 Days",
       x = "Time", y = "Admissions") +
  theme_minimal()
```

---

## Next Steps

- Explore [Healthcare Data Analysis](Healthcare-Data-Analysis) for metric calculations
- Learn [Machine Learning Workflows](Machine-Learning-Workflows) for predictive modeling
- Check [Performance Optimization](Performance-Optimization) for large time series

---

**Last Updated**: November 2025  
**Version**: 1.1.0.9000
