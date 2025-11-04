# Integration with Other Packages

Guide to using healthyverse with other popular R packages and ecosystems.

## Table of Contents

- [Tidyverse Integration](#tidyverse-integration)
- [Tidymodels Integration](#tidymodels-integration)
- [Shiny Applications](#shiny-applications)
- [R Markdown and Quarto](#r-markdown-and-quarto)
- [Database Integration](#database-integration)
- [Visualization Packages](#visualization-packages)
- [Other Ecosystems](#other-ecosystems)

---

## Tidyverse Integration

The healthyverse is designed to work seamlessly with tidyverse packages.

### Loading Together

```r
# Load both ecosystems
library(tidyverse)
library(healthyverse)

# They work together naturally
data <- read_csv("patients.csv") %>%
  filter(!is.na(discharge_date)) %>%
  mutate(los = discharge_date - admit_date) %>%
  group_by(department) %>%
  summarise(avg_los = mean(los))
```

### Data Manipulation with dplyr

```r
library(healthyverse)

# healthyverse packages return tibbles
# which work perfectly with dplyr
result <- patient_data %>%
  filter(age >= 18) %>%
  select(patient_id, age, diagnosis, los) %>%
  arrange(desc(los)) %>%
  mutate(
    age_group = cut(age, breaks = c(0, 18, 65, Inf)),
    los_category = case_when(
      los < 3 ~ "Short",
      los < 7 ~ "Medium",
      TRUE ~ "Long"
    )
  )
```

### String Operations with stringr

```r
library(healthyverse)
library(stringr)

# Clean diagnosis codes
data <- data %>%
  mutate(
    diagnosis_clean = str_trim(diagnosis),
    diagnosis_upper = str_to_upper(diagnosis),
    has_diabetes = str_detect(diagnosis, regex("diabetes", ignore_case = TRUE))
  )
```

### Date Operations with lubridate

```r
library(healthyverse)
library(lubridate)

# Extract date components
data <- data %>%
  mutate(
    admit_year = year(admit_date),
    admit_month = month(admit_date, label = TRUE),
    admit_weekday = wday(admit_date, label = TRUE),
    admit_hour = hour(admit_datetime),
    days_in_hospital = interval(admit_date, discharge_date) / days(1)
  )
```

### Reshaping with tidyr

```r
library(healthyverse)
library(tidyr)

# Pivot wider
wide_data <- long_data %>%
  pivot_wider(
    names_from = measure_type,
    values_from = measure_value
  )

# Pivot longer
long_data <- wide_data %>%
  pivot_longer(
    cols = starts_with("lab_"),
    names_to = "lab_test",
    values_to = "result"
  )
```

---

## Tidymodels Integration

Healthyverse packages (especially tidyAML and healthyR.ai) are built on tidymodels.

### Complete ML Workflow

```r
library(healthyverse)
library(tidymodels)

# 1. Split data
set.seed(123)
data_split <- initial_split(patient_data, prop = 0.8, strata = outcome)
train_data <- training(data_split)
test_data <- testing(data_split)

# 2. Create recipe (preprocessing)
data_recipe <- recipe(outcome ~ ., data = train_data) %>%
  step_impute_median(all_numeric()) %>%
  step_dummy(all_nominal(), -all_outcomes()) %>%
  step_normalize(all_numeric(), -all_outcomes()) %>%
  step_zv(all_predictors())

# 3. Define model
rf_spec <- rand_forest(
  mtry = tune(),
  trees = 1000,
  min_n = tune()
) %>%
  set_engine("ranger") %>%
  set_mode("classification")

# 4. Create workflow
rf_workflow <- workflow() %>%
  add_recipe(data_recipe) %>%
  add_model(rf_spec)

# 5. Tune hyperparameters
cv_folds <- vfold_cv(train_data, v = 5)

tune_results <- tune_grid(
  rf_workflow,
  resamples = cv_folds,
  grid = 10
)

# 6. Finalize and fit
best_params <- select_best(tune_results, metric = "roc_auc")
final_workflow <- finalize_workflow(rf_workflow, best_params)
final_fit <- fit(final_workflow, data = train_data)

# 7. Evaluate
predictions <- predict(final_fit, test_data, type = "prob") %>%
  bind_cols(test_data %>% select(outcome))

roc_auc(predictions, truth = outcome, .pred_Yes)
```

### Using with tidyAML

```r
library(healthyverse)

# tidyAML automates much of this
results <- fast_classification(
  .data = train_data,
  .rec_obj = data_recipe,
  .split_type = "initial_split",
  .split_args = list(prop = 0.8, strata = "outcome")
)

# Extract best model
best_model <- select_best_model(results, metric = "roc_auc")
```

---

## Shiny Applications

Build interactive healthcare dashboards with Shiny.

### Basic Shiny App

```r
library(shiny)
library(healthyverse)

ui <- fluidPage(
  titlePanel("Healthcare Analytics Dashboard"),
  
  sidebarLayout(
    sidebarPanel(
      selectInput("department", "Select Department:",
                  choices = c("All", unique(patient_data$department))),
      dateRangeInput("dates", "Date Range:",
                     start = min(patient_data$admit_date),
                     end = max(patient_data$admit_date))
    ),
    
    mainPanel(
      plotOutput("los_plot"),
      tableOutput("summary_table")
    )
  )
)

server <- function(input, output, session) {
  
  # Reactive data
  filtered_data <- reactive({
    data <- patient_data %>%
      filter(admit_date >= input$dates[1],
             admit_date <= input$dates[2])
    
    if (input$department != "All") {
      data <- data %>% filter(department == input$department)
    }
    
    data
  })
  
  # Length of stay plot
  output$los_plot <- renderPlot({
    ggplot(filtered_data(), aes(x = department, y = los)) +
      geom_boxplot() +
      labs(title = "Length of Stay by Department",
           x = "Department", y = "Days") +
      theme_minimal()
  })
  
  # Summary table
  output$summary_table <- renderTable({
    filtered_data() %>%
      group_by(department) %>%
      summarise(
        Patients = n(),
        `Avg LOS` = round(mean(los), 1),
        `Readmit Rate` = paste0(round(mean(readmitted) * 100, 1), "%")
      )
  })
}

shinyApp(ui, server)
```

### Real-time Forecasting Dashboard

```r
library(shiny)
library(healthyverse)
library(forecast)

ui <- fluidPage(
  titlePanel("Patient Volume Forecasting"),
  
  sidebarLayout(
    sidebarPanel(
      sliderInput("forecast_days", "Forecast Horizon (days):",
                  min = 7, max = 90, value = 30),
      selectInput("method", "Forecasting Method:",
                  choices = c("ARIMA", "ETS", "Prophet")),
      actionButton("update", "Update Forecast", class = "btn-primary")
    ),
    
    mainPanel(
      plotOutput("forecast_plot"),
      verbatimTextOutput("accuracy_metrics")
    )
  )
)

server <- function(input, output, session) {
  
  forecast_results <- eventReactive(input$update, {
    # Create time series
    ts_data <- ts(visits_data$visits, frequency = 365)
    
    # Generate forecast based on method
    if (input$method == "ARIMA") {
      model <- auto.arima(ts_data)
    } else if (input$method == "ETS") {
      model <- ets(ts_data)
    }
    
    forecast(model, h = input$forecast_days)
  })
  
  output$forecast_plot <- renderPlot({
    autoplot(forecast_results()) +
      labs(title = paste(input$method, "Forecast"),
           x = "Time", y = "Patient Visits") +
      theme_minimal()
  })
  
  output$accuracy_metrics <- renderPrint({
    accuracy(forecast_results())
  })
}

shinyApp(ui, server)
```

---

## R Markdown and Quarto

Create reproducible healthcare reports.

### R Markdown Healthcare Report

````markdown
---
title: "Monthly Healthcare Analytics Report"
author: "Healthcare Analytics Team"
date: "`r Sys.Date()`"
output:
  html_document:
    toc: true
    toc_float: true
    theme: flatly
---

```{r setup, include=FALSE}
knitr::opts_chunk$set(echo = FALSE, message = FALSE, warning = FALSE)
library(healthyverse)
library(gt)
```

## Executive Summary

```{r summary}
# Calculate key metrics
summary_stats <- patient_data %>%
  summarise(
    total_patients = n(),
    avg_los = round(mean(los), 1),
    readmission_rate = paste0(round(mean(readmitted) * 100, 1), "%")
  )

gt(summary_stats) %>%
  tab_header(title = "Key Metrics")
```

## Patient Volume Trends

```{r volume-trend}
patient_data %>%
  mutate(month = floor_date(admit_date, "month")) %>%
  count(month) %>%
  ggplot(aes(x = month, y = n)) +
  geom_line(color = "steelblue", size = 1) +
  geom_point(color = "steelblue", size = 2) +
  labs(title = "Monthly Patient Volume",
       x = "Month", y = "Number of Patients") +
  theme_minimal()
```

## Length of Stay Analysis

```{r los-analysis}
patient_data %>%
  group_by(department) %>%
  summarise(
    patients = n(),
    avg_los = round(mean(los), 1),
    median_los = median(los)
  ) %>%
  arrange(desc(avg_los)) %>%
  gt() %>%
  tab_header(title = "Length of Stay by Department") %>%
  cols_label(
    department = "Department",
    patients = "Patients",
    avg_los = "Avg LOS",
    median_los = "Median LOS"
  )
```
````

### Quarto Document

````markdown
---
title: "Healthyverse Analysis"
format:
  html:
    code-fold: true
    toc: true
execute:
  echo: true
  warning: false
---

## Setup

```{r}
library(healthyverse)
data <- read_csv("patients.csv")
```

## Analysis

```{r}
#| label: fig-los
#| fig-cap: "Length of Stay Distribution"

ggplot(data, aes(x = los)) +
  geom_histogram(bins = 30, fill = "steelblue") +
  theme_minimal()
```
````

---

## Database Integration

Work with healthcare databases efficiently.

### DBI and dbplyr

```r
library(healthyverse)
library(DBI)
library(dbplyr)

# Connect to database
con <- dbConnect(
  RPostgres::Postgres(),
  dbname = "hospital_db",
  host = "localhost",
  port = 5432,
  user = Sys.getenv("DB_USER"),
  password = Sys.getenv("DB_PASS")
)

# Create lazy tbl
patients <- tbl(con, "patients")
encounters <- tbl(con, "encounters")

# Use dplyr on database
result <- patients %>%
  inner_join(encounters, by = "patient_id") %>%
  filter(admit_date >= "2024-01-01") %>%
  group_by(department) %>%
  summarise(
    count = n(),
    avg_los = mean(los, na.rm = TRUE)
  ) %>%
  arrange(desc(count)) %>%
  collect()  # Bring to R

# Always disconnect
dbDisconnect(con)
```

### Database Queries with SQL

```r
library(DBI)

con <- dbConnect(...)

# Execute SQL query
query <- "
  SELECT department, 
         COUNT(*) as patient_count,
         AVG(los) as avg_los
  FROM encounters
  WHERE admit_date >= '2024-01-01'
  GROUP BY department
  ORDER BY patient_count DESC
"

result <- dbGetQuery(con, query)

# Use with healthyverse functions
analysis <- analyze_encounters(result)

dbDisconnect(con)
```

---

## Visualization Packages

### ggplot2 Extensions

```r
library(healthyverse)
library(ggplot2)
library(ggthemes)
library(patchwork)

# Create multiple plots
p1 <- ggplot(data, aes(x = department, y = los)) +
  geom_boxplot() +
  theme_economist()

p2 <- ggplot(data, aes(x = los)) +
  geom_histogram(bins = 30) +
  theme_economist()

# Combine with patchwork
p1 + p2 + plot_annotation(title = "Healthcare Analytics")
```

### Interactive Plots with plotly

```r
library(healthyverse)
library(plotly)

# Create ggplot
p <- ggplot(data, aes(x = admit_date, y = visits, color = department)) +
  geom_line() +
  theme_minimal()

# Make interactive
ggplotly(p)
```

### Dashboard with flexdashboard

````markdown
---
title: "Healthcare Dashboard"
output: flexdashboard::flex_dashboard
runtime: shiny
---

```{r setup}
library(healthyverse)
library(flexdashboard)
```

Sidebar {.sidebar}
-------------------------------------

```{r}
selectInput("dept", "Department:", choices = unique(data$department))
```

Column
-------------------------------------

### Patient Volume

```{r}
renderPlot({
  data %>%
    filter(department == input$dept) %>%
    ggplot(aes(x = date, y = visits)) +
    geom_line() +
    theme_minimal()
})
```
````

---

## Other Ecosystems

### targets for Workflow Management

```r
# _targets.R
library(targets)
library(healthyverse)

tar_option_set(packages = c("healthyverse", "tidyverse"))

list(
  tar_target(raw_data, read_csv("data/patients.csv")),
  tar_target(clean_data, clean_patient_data(raw_data)),
  tar_target(analysis_results, analyze_data(clean_data)),
  tar_target(report, render_report(analysis_results))
)
```

### drake (predecessor to targets)

```r
library(drake)
library(healthyverse)

plan <- drake_plan(
  raw_data = read_csv("data/patients.csv"),
  clean_data = clean_patient_data(raw_data),
  model = train_model(clean_data),
  report = rmarkdown::render("report.Rmd")
)

make(plan)
```

### reticulate for Python Integration

```r
library(healthyverse)
library(reticulate)

# Use Python's scikit-learn
sklearn <- import("sklearn.ensemble")

# Prepare data in R
data_prepared <- patient_data %>%
  select(where(is.numeric)) %>%
  na.omit()

# Train model in Python
model <- sklearn$RandomForestClassifier()
model$fit(data_prepared[, -1], data_prepared[, 1])

# Use predictions in R
predictions <- model$predict(new_data)
```

---

## Best Practices

### 1. Namespace Management

```r
# Use explicit namespaces to avoid conflicts
result <- dplyr::select(data, column1, column2)
forecast <- forecast::forecast(model, h = 30)
```

### 2. Consistent Data Types

```r
# Ensure compatibility between packages
# Convert as needed
data_tibble <- as_tibble(data_frame)
data_dt <- as.data.table(data_tibble)
```

### 3. Error Handling

```r
# Graceful handling of package differences
tryCatch({
  # Try tidyverse approach
  result <- tidyverse_function(data)
}, error = function(e) {
  # Fall back to base R
  result <- base_function(data)
})
```

---

## Resources

- **Tidyverse**: https://www.tidyverse.org/
- **Tidymodels**: https://www.tidymodels.org/
- **Shiny**: https://shiny.rstudio.com/
- **R Markdown**: https://rmarkdown.rstudio.com/
- **Quarto**: https://quarto.org/

---

**Last Updated**: November 2025  
**Version**: 1.1.0.9000
