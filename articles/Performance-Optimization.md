# Performance Optimization

Guide to optimizing healthyverse performance for large datasets and complex analyses.

## Table of Contents

- [Performance Basics](#performance-basics)
- [Data Management](#data-management)
- [Parallel Processing](#parallel-processing)
- [Memory Optimization](#memory-optimization)
- [Code Optimization](#code-optimization)
- [Benchmarking](#benchmarking)
- [Best Practices](#best-practices)

---

## Performance Basics

### Understanding Performance

Performance bottlenecks typically occur in:
1. **Data loading** - Reading large files
2. **Data transformation** - Processing operations
3. **Computation** - Complex calculations
4. **Memory** - Insufficient RAM
5. **I/O** - Disk read/write operations

### Profiling Your Code

```r
# Profile with profvis
library(profvis)

profvis({
  # Your code here
  data <- read.csv("large_file.csv")
  result <- analyze_data(data)
})

# This opens an interactive visualization showing:
# - Time spent in each function
# - Memory usage
# - Call stack
```

### Quick Wins

```r
# 1. Load only needed columns
data <- read.csv("data.csv", 
                 colClasses = c("numeric", "factor", "Date", rep("NULL", 10)))

# 2. Use readr for faster reading
library(readr)
data <- read_csv("data.csv")

# 3. Filter early
data <- read.csv("data.csv") %>%
  filter(date >= "2024-01-01")  # Early filtering

# 4. Use data.table for large data
library(data.table)
dt <- fread("large_file.csv")
```

---

## Data Management

### Efficient Data Loading

#### Use readr for Speed

```r
library(readr)

# Faster than base R
data <- read_csv("large_file.csv",
                 col_types = cols(
                   date = col_date(),
                   value = col_double(),
                   category = col_factor()
                 ))

# Read specific columns only
data <- read_csv("large_file.csv",
                 col_select = c(patient_id, date, diagnosis))
```

#### Use data.table for Very Large Data

```r
library(data.table)

# Extremely fast for large files
dt <- fread("huge_file.csv")

# data.table operations are very fast
result <- dt[date >= "2024-01-01", 
             .(avg_value = mean(value)), 
             by = category]

# Convert between data.table and tibble
library(dtplyr)
lazy_dt <- lazy_dt(dt)  # Lazy evaluation
result <- lazy_dt %>%
  filter(value > 100) %>%
  group_by(category) %>%
  summarise(mean = mean(value)) %>%
  as_tibble()  # Collect results
```

### Database Connections

For truly large datasets, use databases:

```r
library(DBI)
library(dplyr)

# Connect to database
con <- dbConnect(RSQLite::SQLite(), "hospital.db")

# Use dplyr with database
patients <- tbl(con, "patients")

# Operations are lazy (executed on database)
result <- patients %>%
  filter(date >= "2024-01-01") %>%
  group_by(department) %>%
  summarise(count = n()) %>%
  collect()  # Bring results into R

# Always disconnect
dbDisconnect(con)
```

### Chunked Processing

```r
# Process large file in chunks
library(readr)

process_chunk <- function(chunk) {
  # Process each chunk
  chunk %>%
    filter(!is.na(value)) %>%
    summarise(mean = mean(value))
}

# Read and process in chunks
results <- read_csv_chunked(
  "huge_file.csv",
  callback = DataFrameCallback$new(process_chunk),
  chunk_size = 10000
)
```

---

## Parallel Processing

### Using future and furrr

```r
library(future)
library(furrr)

# Set up parallel processing
plan(multisession, workers = 4)

# Parallel map
results <- future_map(data_list, ~ {
  analyze(.x)
}, .options = furrr_options(seed = TRUE))

# Reset to sequential
plan(sequential)
```

### Parallel Machine Learning

```r
library(doParallel)
library(foreach)

# Setup
cl <- makeCluster(detectCores() - 1)
registerDoParallel(cl)

# Parallel cross-validation
library(caret)
model <- train(
  outcome ~ .,
  data = training_data,
  method = "rf",
  trControl = trainControl(
    method = "cv",
    number = 10,
    allowParallel = TRUE
  )
)

# Cleanup
stopCluster(cl)
```

### Parallel tidymodels

```r
library(tidymodels)
library(doParallel)

# Setup parallel backend
cl <- makeCluster(4)
registerDoParallel(cl)

# Tune with parallel processing
tune_results <- tune_grid(
  workflow,
  resamples = cv_folds,
  grid = param_grid,
  control = control_grid(
    verbose = TRUE,
    allow_par = TRUE,
    parallel_over = "everything"
  )
)

stopCluster(cl)
```

---

## Memory Optimization

### Monitor Memory Usage

```r
library(pryr)

# Current memory usage
mem_used()

# Object sizes
object_size(large_data)

# Memory change during operation
mem_change({
  result <- expensive_operation(data)
})
```

### Reduce Memory Footprint

```r
# 1. Remove unused objects
rm(temp_data, old_results)
gc()  # Garbage collection

# 2. Use appropriate data types
data <- data %>%
  mutate(
    id = as.integer(id),  # Use integer instead of numeric
    category = factor(category),  # Use factor for categories
    date = as.Date(date)  # Use Date not POSIXct if time not needed
  )

# 3. Avoid copies
# Bad: Creates multiple copies
result <- data %>%
  mutate(x = x + 1) %>%
  mutate(y = y + 1) %>%
  mutate(z = z + 1)

# Good: Single mutation
result <- data %>%
  mutate(
    x = x + 1,
    y = y + 1,
    z = z + 1
  )
```

### Memory-Efficient Patterns

```r
# Process in batches instead of all at once
process_batches <- function(data, batch_size = 1000) {
  n <- nrow(data)
  results <- list()
  
  for (i in seq(1, n, batch_size)) {
    end <- min(i + batch_size - 1, n)
    batch <- data[i:end, ]
    results[[length(results) + 1]] <- process(batch)
    
    # Clear batch from memory
    rm(batch)
    gc()
  }
  
  bind_rows(results)
}
```

---

## Code Optimization

### Vectorization

```r
# Slow: Loop
result <- numeric(nrow(data))
for (i in 1:nrow(data)) {
  result[i] <- data$x[i] * data$y[i]
}

# Fast: Vectorized
result <- data$x * data$y

# Or with dplyr
result <- data %>%
  mutate(result = x * y)
```

### Avoid Growing Objects

```r
# Slow: Growing a vector
result <- c()
for (i in 1:10000) {
  result <- c(result, i^2)
}

# Fast: Pre-allocate
result <- numeric(10000)
for (i in 1:10000) {
  result[i] <- i^2
}

# Even better: Vectorize
result <- (1:10000)^2
```

### Efficient dplyr

```r
# Combine operations
# Less efficient
data <- data %>% filter(x > 5)
data <- data %>% filter(y < 10)
data <- data %>% select(a, b, c)

# More efficient
data <- data %>%
  filter(x > 5, y < 10) %>%
  select(a, b, c)

# Use .by instead of group_by() when possible (dplyr 1.1.0+)
# Less efficient
result <- data %>%
  group_by(category) %>%
  summarise(mean = mean(value)) %>%
  ungroup()

# More efficient
result <- data %>%
  summarise(mean = mean(value), .by = category)
```

### String Operations

```r
library(stringr)

# Vectorized string operations
# Slow
result <- sapply(strings, function(x) str_detect(x, "pattern"))

# Fast
result <- str_detect(strings, "pattern")

# Compile regex for repeated use
pattern <- regex("complex.*pattern", ignore_case = TRUE)
result <- str_detect(strings, pattern)
```

---

## Benchmarking

### Using microbenchmark

```r
library(microbenchmark)

# Compare different approaches
results <- microbenchmark(
  base_r = apply(data, 1, sum),
  dplyr = data %>% rowwise() %>% mutate(sum = sum(c_across())),
  rowSums = rowSums(data),
  times = 100
)

print(results)
autoplot(results)
```

### Using bench

```r
library(bench)

# More detailed benchmarking
results <- mark(
  base_r = apply(data, 1, sum),
  dplyr = data %>% rowwise() %>% mutate(sum = sum(c_across())),
  rowSums = rowSums(data),
  check = FALSE,
  iterations = 100
)

print(results)
plot(results)
```

### Real-world Benchmarking

```r
# Benchmark complete workflow
library(tictoc)

tic("Complete Analysis")

tic("Data Loading")
data <- read_csv("large_file.csv")
toc()

tic("Data Cleaning")
clean_data <- clean_data(data)
toc()

tic("Analysis")
results <- analyze_data(clean_data)
toc()

toc()  # Complete Analysis
```

---

## Best Practices

### 1. Profile Before Optimizing

```r
# Always profile first
profvis({
  # Your analysis
})

# Identify bottlenecks
# Optimize only the slow parts
```

### 2. Choose Right Data Structure

```r
# For large data: data.table
# For moderate data: tibble
# For databases: database connection
# For repeated operations: matrix (if numeric)

# Example: Matrix operations are faster
matrix_data <- as.matrix(numeric_data)
result <- matrix_data %*% t(matrix_data)  # Very fast
```

### 3. Cache Expensive Computations

```r
library(memoise)

# Cache function results
expensive_function <- memoise(function(x) {
  # Expensive computation
  Sys.sleep(2)
  x^2
})

# First call: slow
result1 <- expensive_function(5)

# Second call: instant (cached)
result2 <- expensive_function(5)
```

### 4. Use Compiled Code for Critical Loops

```r
library(Rcpp)

# C++ function (in separate file or inline)
cppFunction('
  NumericVector cpp_sum(NumericVector x, NumericVector y) {
    int n = x.size();
    NumericVector result(n);
    for(int i = 0; i < n; i++) {
      result[i] = x[i] + y[i];
    }
    return result;
  }
')

# Much faster than R loop
result <- cpp_sum(x, y)
```

### 5. Monitor Resources

```r
# Monitor memory during long operations
library(pryr)

monitor_memory <- function(expr) {
  mem_before <- mem_used()
  result <- force(expr)
  mem_after <- mem_used()
  
  cat("Memory used:", format(mem_after - mem_before), "\n")
  result
}

result <- monitor_memory({
  analyze_large_dataset(data)
})
```

---

## Healthcare-Specific Optimizations

### Time Series Forecasting

```r
# For many time series, use parallel processing
library(furrr)

plan(multisession, workers = 4)

forecasts <- patient_groups %>%
  group_split(department) %>%
  future_map(~ {
    ts_data <- ts(.x$visits, frequency = 365)
    forecast(auto.arima(ts_data), h = 30)
  })
```

### Machine Learning

```r
# Sample data for initial exploration
sample_data <- slice_sample(large_data, prop = 0.1)

# Use on sample first
model <- train_model(sample_data)

# Then on full data if needed
if (model_good_enough(model)) {
  final_model <- train_model(large_data)
}
```

### Large Healthcare Databases

```r
# Use database for filtering, R for analysis
library(dbplyr)

# Connect to database
con <- dbConnect(...)

# Filter in database
filtered <- tbl(con, "encounters") %>%
  filter(
    date >= "2024-01-01",
    department == "Emergency"
  ) %>%
  collect()  # Bring to R

# Analyze in R
analysis_results <- analyze_encounters(filtered)
```

---

## Troubleshooting Performance Issues

### Common Issues

1. **Loading too much data**: Use database or sampling
2. **Inefficient operations**: Profile and vectorize
3. **Memory constraints**: Process in chunks
4. **Slow functions**: Use parallel processing or Rcpp
5. **I/O bottlenecks**: Use faster formats (parquet, feather)

### Performance Checklist

- [ ] Profiled code to identify bottlenecks
- [ ] Using appropriate data structures
- [ ] Vectorized operations where possible
- [ ] Loading only needed data
- [ ] Using efficient file formats
- [ ] Parallel processing for independent operations
- [ ] Caching expensive computations
- [ ] Monitoring memory usage
- [ ] Using database for very large data

---

## Additional Resources

- **Advanced R**: https://adv-r.hadley.nz/perf-measure.html
- **Efficient R Programming**: https://csgillespie.github.io/efficientR/
- **data.table**: https://rdatatable.gitlab.io/data.table/
- **future package**: https://future.futureverse.org/

---

**Last Updated**: November 2025  
**Version**: 1.1.0.9000
