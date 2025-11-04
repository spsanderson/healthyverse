# Healthcare Data Analysis

Comprehensive guide to analyzing healthcare data with the healthyverse.

## Table of Contents

- [Introduction](#introduction)
- [Data Preparation](#data-preparation)
- [Common Healthcare Metrics](#common-healthcare-metrics)
- [Patient Flow Analysis](#patient-flow-analysis)
- [Readmission Analysis](#readmission-analysis)
- [Quality Metrics](#quality-metrics)
- [Resource Utilization](#resource-utilization)
- [Best Practices](#best-practices)
- [Complete Examples](#complete-examples)

---

## Introduction

The healthyverse provides specialized tools for healthcare data analysis, focusing on administrative data, clinical outcomes, and operational metrics.

### Key Packages for Healthcare Analysis

- **healthyR**: Core healthcare analytics functions
- **healthyR.data**: Sample healthcare datasets
- **healthyR.ts**: Time-series analysis for healthcare trends

### Typical Healthcare Data Workflow

```r
library(healthyverse)

# 1. Load and prepare data
# 2. Calculate healthcare metrics
# 3. Analyze trends and patterns
# 4. Visualize results
# 5. Generate reports
```

---

## Data Preparation

### Loading Healthcare Data

```r
library(healthyverse)

# From CSV
patient_data <- read.csv("patients.csv")

# From database
library(DBI)
con <- dbConnect(RSQLite::SQLite(), "hospital.db")
encounters <- dbReadTable(con, "encounters")
dbDisconnect(con)

# From healthyR.data (sample data)
data(package = "healthyR.data")
```

### Data Cleaning

```r
# Convert dates
patient_data <- patient_data %>%
  mutate(
    admit_date = as.Date(admit_date),
    discharge_date = as.Date(discharge_date)
  )

# Handle missing values
patient_data <- patient_data %>%
  filter(!is.na(discharge_date)) %>%
  mutate(
    age = ifelse(is.na(age), median(age, na.rm = TRUE), age)
  )

# Standardize categories
patient_data <- patient_data %>%
  mutate(
    gender = toupper(gender),
    department = str_trim(department)
  )
```

### Data Validation

```r
# Check for duplicates
duplicates <- patient_data %>%
  group_by(patient_id, encounter_id) %>%
  filter(n() > 1)

# Validate date ranges
invalid_dates <- patient_data %>%
  filter(discharge_date < admit_date)

# Check for outliers
summary(patient_data$age)
boxplot(patient_data$age)
```

---

## Common Healthcare Metrics

### Length of Stay (LOS)

```r
# Calculate LOS
patient_data <- patient_data %>%
  mutate(
    los = as.numeric(discharge_date - admit_date)
  )

# Summary statistics
los_summary <- patient_data %>%
  summarise(
    mean_los = mean(los),
    median_los = median(los),
    sd_los = sd(los),
    min_los = min(los),
    max_los = max(los)
  )

# LOS by department
los_by_dept <- patient_data %>%
  group_by(department) %>%
  summarise(
    avg_los = mean(los),
    total_patients = n()
  ) %>%
  arrange(desc(avg_los))

# Visualize
library(ggplot2)
ggplot(patient_data, aes(x = department, y = los)) +
  geom_boxplot() +
  coord_flip() +
  labs(title = "Length of Stay by Department",
       x = "Department", y = "Days") +
  theme_minimal()
```

### Bed Occupancy Rate

```r
# Calculate daily census
daily_census <- patient_data %>%
  mutate(date = admit_date) %>%
  group_by(date) %>%
  summarise(census = n())

# Average occupancy
avg_occupancy <- mean(daily_census$census)

# Occupancy rate (assuming 100 beds)
total_beds <- 100
occupancy_rate <- (avg_occupancy / total_beds) * 100

# Peak occupancy
peak_occupancy <- max(daily_census$census)
```

### Mortality Rate

```r
# Calculate mortality rate
mortality <- patient_data %>%
  summarise(
    total_encounters = n(),
    deaths = sum(discharge_status == "Expired"),
    mortality_rate = (deaths / total_encounters) * 100
  )

# By service line
mortality_by_service <- patient_data %>%
  group_by(service_line) %>%
  summarise(
    total = n(),
    deaths = sum(discharge_status == "Expired"),
    mortality_rate = (deaths / total) * 100
  )
```

---

## Patient Flow Analysis

### Admission Patterns

```r
# Admissions by day of week
admissions_dow <- patient_data %>%
  mutate(day_of_week = weekdays(admit_date)) %>%
  group_by(day_of_week) %>%
  summarise(admissions = n()) %>%
  mutate(day_of_week = factor(day_of_week, 
                               levels = c("Monday", "Tuesday", "Wednesday",
                                         "Thursday", "Friday", "Saturday", "Sunday")))

ggplot(admissions_dow, aes(x = day_of_week, y = admissions)) +
  geom_col(fill = "steelblue") +
  labs(title = "Admissions by Day of Week",
       x = "Day", y = "Number of Admissions") +
  theme_minimal()

# Admissions by hour
admissions_hour <- patient_data %>%
  mutate(hour = hour(admit_datetime)) %>%
  group_by(hour) %>%
  summarise(admissions = n())

ggplot(admissions_hour, aes(x = hour, y = admissions)) +
  geom_line(color = "steelblue", size = 1) +
  geom_point(color = "steelblue", size = 2) +
  labs(title = "Admissions by Hour of Day",
       x = "Hour (24h)", y = "Number of Admissions") +
  theme_minimal()
```

### Transfer Analysis

```r
# Track patient transfers
transfers <- patient_data %>%
  filter(!is.na(transfer_from) | !is.na(transfer_to)) %>%
  summarise(
    total_transfers = n(),
    icu_transfers = sum(transfer_to == "ICU"),
    transfer_rate = (total_transfers / nrow(patient_data)) * 100
  )

# Transfer patterns
transfer_matrix <- patient_data %>%
  filter(!is.na(transfer_from) & !is.na(transfer_to)) %>%
  group_by(transfer_from, transfer_to) %>%
  summarise(count = n()) %>%
  arrange(desc(count))
```

### Discharge Planning

```r
# Discharge disposition
discharge_summary <- patient_data %>%
  group_by(discharge_disposition) %>%
  summarise(
    count = n(),
    percentage = (n() / nrow(patient_data)) * 100,
    avg_los = mean(los)
  ) %>%
  arrange(desc(count))

# Visualize
ggplot(discharge_summary, aes(x = reorder(discharge_disposition, count), 
                               y = count)) +
  geom_col(fill = "steelblue") +
  coord_flip() +
  labs(title = "Discharge Disposition",
       x = "Disposition", y = "Count") +
  theme_minimal()
```

---

## Readmission Analysis

### 30-Day Readmission Rate

```r
# Identify readmissions
readmissions <- patient_data %>%
  arrange(patient_id, admit_date) %>%
  group_by(patient_id) %>%
  mutate(
    days_since_last = as.numeric(admit_date - lag(discharge_date)),
    is_readmission = !is.na(days_since_last) & days_since_last <= 30
  )

# Calculate rate
readmission_rate <- readmissions %>%
  summarise(
    total_discharges = n(),
    readmissions_30d = sum(is_readmission, na.rm = TRUE),
    rate = (readmissions_30d / total_discharges) * 100
  )

# By diagnosis
readmission_by_dx <- readmissions %>%
  group_by(primary_diagnosis) %>%
  summarise(
    total = n(),
    readmits = sum(is_readmission, na.rm = TRUE),
    rate = (readmits / total) * 100
  ) %>%
  filter(total >= 30) %>%  # Minimum sample size
  arrange(desc(rate))
```

### Readmission Risk Factors

```r
# Analyze risk factors
risk_factors <- readmissions %>%
  group_by(age_group, comorbidity_count, is_readmission) %>%
  summarise(count = n()) %>%
  spread(is_readmission, count, fill = 0) %>%
  rename(no_readmit = `FALSE`, readmit = `TRUE`) %>%
  mutate(
    total = no_readmit + readmit,
    risk = (readmit / total) * 100
  )

# Visualize
ggplot(risk_factors, aes(x = comorbidity_count, y = risk, 
                         color = age_group, group = age_group)) +
  geom_line(size = 1) +
  geom_point(size = 2) +
  labs(title = "Readmission Risk by Age and Comorbidities",
       x = "Number of Comorbidities", y = "Readmission Rate (%)") +
  theme_minimal()
```

---

## Quality Metrics

### Core Measures

```r
# Pneumonia quality metrics
pneumonia_quality <- patient_data %>%
  filter(primary_diagnosis == "Pneumonia") %>%
  summarise(
    total_cases = n(),
    abx_within_6h = sum(antibiotics_time <= 6),
    compliance_rate = (abx_within_6h / total_cases) * 100
  )

# Surgical site infection rate
ssi_rate <- patient_data %>%
  filter(procedure_type == "Surgery") %>%
  summarise(
    total_surgeries = n(),
    infections = sum(surgical_site_infection == TRUE),
    infection_rate = (infections / total_surgeries) * 100
  )
```

### Patient Satisfaction

```r
# HCAHPS scores
satisfaction <- patient_data %>%
  filter(!is.na(hcahps_score)) %>%
  summarise(
    mean_score = mean(hcahps_score),
    top_box = sum(hcahps_score >= 9) / n() * 100,
    bottom_box = sum(hcahps_score <= 6) / n() * 100
  )

# By department
satisfaction_by_dept <- patient_data %>%
  filter(!is.na(hcahps_score)) %>%
  group_by(department) %>%
  summarise(
    responses = n(),
    mean_score = mean(hcahps_score),
    top_box = sum(hcahps_score >= 9) / n() * 100
  ) %>%
  arrange(desc(mean_score))
```

---

## Resource Utilization

### Procedure Volume

```r
# Procedure counts
procedure_volume <- patient_data %>%
  filter(!is.na(procedure_code)) %>%
  group_by(procedure_code, procedure_name) %>%
  summarise(
    volume = n(),
    avg_los = mean(los)
  ) %>%
  arrange(desc(volume))

# Top 10 procedures
top_procedures <- head(procedure_volume, 10)

ggplot(top_procedures, aes(x = reorder(procedure_name, volume), y = volume)) +
  geom_col(fill = "steelblue") +
  coord_flip() +
  labs(title = "Top 10 Procedures by Volume",
       x = "Procedure", y = "Count") +
  theme_minimal()
```

### Cost Analysis

```r
# Average cost per case
cost_analysis <- patient_data %>%
  group_by(service_line) %>%
  summarise(
    cases = n(),
    total_cost = sum(total_charges),
    avg_cost = mean(total_charges),
    median_cost = median(total_charges)
  ) %>%
  arrange(desc(avg_cost))

# Cost trends over time
monthly_costs <- patient_data %>%
  mutate(month = floor_date(admit_date, "month")) %>%
  group_by(month) %>%
  summarise(
    cases = n(),
    total_cost = sum(total_charges),
    avg_cost_per_case = total_cost / cases
  )

ggplot(monthly_costs, aes(x = month, y = avg_cost_per_case)) +
  geom_line(color = "steelblue", size = 1) +
  geom_smooth(method = "loess", se = FALSE, color = "red", linetype = "dashed") +
  labs(title = "Average Cost per Case Over Time",
       x = "Month", y = "Average Cost ($)") +
  theme_minimal()
```

---

## Best Practices

### Data Quality

1. **Always validate dates**:
   ```r
   # Check for future dates
   future_dates <- filter(data, admit_date > Sys.Date())
   
   # Check date order
   invalid_order <- filter(data, discharge_date < admit_date)
   ```

2. **Handle missing data appropriately**:
   ```r
   # Document missingness
   missing_summary <- data %>%
     summarise(across(everything(), ~sum(is.na(.))))
   ```

3. **Standardize coding**:
   ```r
   # Use consistent categories
   data <- data %>%
     mutate(gender = recode(gender, 
                           "M" = "Male", "F" = "Female",
                           "m" = "Male", "f" = "Female"))
   ```

### Privacy and Security

1. **De-identify data**:
   ```r
   # Remove or hash identifiers
   data_clean <- data %>%
     select(-patient_name, -ssn, -phone) %>%
     mutate(patient_id = as.character(as.numeric(factor(patient_id))))
   ```

2. **Limit data access**:
   ```r
   # Only load needed columns
   data <- read.csv("patients.csv", 
                    select = c("age", "gender", "diagnosis", "los"))
   ```

3. **Secure data storage**:
   - Use encrypted databases
   - Implement access controls
   - Follow HIPAA guidelines

### Performance

1. **Use efficient data structures**:
   ```r
   # data.table for large datasets
   library(data.table)
   dt <- as.data.table(df)
   ```

2. **Filter early**:
   ```r
   # Good: Filter first
   recent_data <- data %>%
     filter(admit_date >= "2024-01-01") %>%
     mutate(los = discharge_date - admit_date)
   
   # Bad: Calculate on all data
   data <- data %>%
     mutate(los = discharge_date - admit_date) %>%
     filter(admit_date >= "2024-01-01")
   ```

---

## Complete Examples

### Example 1: Monthly Dashboard

```r
library(healthyverse)

# Load data
data <- read.csv("encounters.csv") %>%
  mutate(
    admit_date = as.Date(admit_date),
    discharge_date = as.Date(discharge_date),
    los = as.numeric(discharge_date - admit_date)
  )

# Calculate metrics
dashboard <- list(
  # Volume metrics
  total_encounters = nrow(data),
  total_admissions = sum(data$encounter_type == "Inpatient"),
  total_ed_visits = sum(data$encounter_type == "Emergency"),
  
  # LOS metrics
  avg_los = mean(data$los, na.rm = TRUE),
  median_los = median(data$los, na.rm = TRUE),
  
  # Readmissions
  readmission_rate = calculate_readmission_rate(data),
  
  # Quality
  mortality_rate = sum(data$discharge_status == "Expired") / nrow(data) * 100
)

print(dashboard)
```

### Example 2: Quarterly Report

```r
# Generate quarterly metrics
quarterly_report <- function(data, year, quarter) {
  # Filter data
  q_data <- data %>%
    filter(
      year(admit_date) == year,
      quarter(admit_date) == quarter
    )
  
  # Calculate metrics
  report <- list(
    period = paste(year, "Q", quarter),
    volume = nrow(q_data),
    avg_los = mean(q_data$los),
    readmission_rate = calculate_readmission_rate(q_data),
    satisfaction_score = mean(q_data$hcahps_score, na.rm = TRUE)
  )
  
  return(report)
}

# Generate reports
q1_2024 <- quarterly_report(data, 2024, 1)
q2_2024 <- quarterly_report(data, 2024, 2)
```

---

## Next Steps

- Learn [Time Series Forecasting](Time-Series-Forecasting.md) for trend analysis
- Explore [Machine Learning Workflows](Machine-Learning-Workflows.md) for predictive modeling
- Check [Performance Optimization](Performance-Optimization.md) for large datasets

---

**Last Updated**: November 2025  
**Version**: 1.1.0.9000
