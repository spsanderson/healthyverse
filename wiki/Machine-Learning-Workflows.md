# Machine Learning Workflows

Comprehensive guide to machine learning with healthyverse packages.

## Table of Contents

- [Introduction](#introduction)
- [Getting Started](#getting-started)
- [Automated Machine Learning (tidyAML)](#automated-machine-learning-tidyaml)
- [Healthcare AI (healthyR.ai)](#healthcare-ai-healthyrai)
- [Model Building](#model-building)
- [Model Evaluation](#model-evaluation)
- [Deployment](#deployment)
- [Best Practices](#best-practices)

---

## Introduction

The healthyverse provides powerful tools for machine learning, with a focus on healthcare applications and automated workflows.

### Key Packages

- **tidyAML**: Automated machine learning built on tidymodels
- **healthyR.ai**: Healthcare-specific AI and ML utilities
- **TidyDensity**: Statistical distributions for modeling

### Common ML Tasks in Healthcare

- **Classification**: Diagnosis prediction, readmission risk
- **Regression**: Length of stay prediction, cost estimation
- **Clustering**: Patient segmentation, disease grouping
- **Time Series**: Patient volume forecasting

---

## Getting Started

### Data Preparation

```r
library(healthyverse)

# Load data
patient_data <- read.csv("patients.csv")

# Data cleaning
clean_data <- patient_data %>%
  # Remove missing values
  drop_na(outcome) %>%
  # Encode categorical variables
  mutate(
    gender = factor(gender),
    race = factor(race),
    admission_type = factor(admission_type)
  ) %>%
  # Scale numeric variables if needed
  mutate(across(where(is.numeric), scale))

# Check for class imbalance
table(clean_data$outcome)
```

### Train-Test Split

```r
library(rsample)

# Simple split
set.seed(123)
data_split <- initial_split(clean_data, prop = 0.8, strata = outcome)
train_data <- training(data_split)
test_data <- testing(data_split)

# For time series (no random sampling)
time_split <- initial_time_split(clean_data, prop = 0.8)

# Cross-validation folds
cv_folds <- vfold_cv(train_data, v = 10, strata = outcome)
```

### Feature Engineering

```r
library(recipes)

# Create recipe
data_recipe <- recipe(outcome ~ ., data = train_data) %>%
  # Handle missing values
  step_impute_median(all_numeric()) %>%
  step_impute_mode(all_nominal(), -all_outcomes()) %>%
  # Create dummy variables
  step_dummy(all_nominal(), -all_outcomes()) %>%
  # Normalize numeric predictors
  step_normalize(all_numeric(), -all_outcomes()) %>%
  # Remove zero variance predictors
  step_zv(all_predictors()) %>%
  # Remove highly correlated predictors
  step_corr(all_numeric(), threshold = 0.9)

# Prep and bake
prepped_recipe <- prep(data_recipe, training = train_data)
train_processed <- bake(prepped_recipe, new_data = train_data)
test_processed <- bake(prepped_recipe, new_data = test_data)
```

---

## Automated Machine Learning (tidyAML)

### Quick AutoML

```r
library(tidyAML)

# Fast classification
results <- fast_classification(
  .data = train_data,
  .rec_obj = data_recipe,
  .split_type = "initial_split",
  .split_args = list(prop = 0.8, strata = "outcome")
)

# Fast regression
results <- fast_regression(
  .data = train_data,
  .rec_obj = data_recipe,
  .split_type = "initial_split",
  .split_args = list(prop = 0.8)
)
```

### Model Comparison

```r
# Extract model performance
model_comparison <- extract_model_comparison(results)

# View metrics
print(model_comparison)

# Visualize performance
library(ggplot2)
ggplot(model_comparison, aes(x = model, y = accuracy, fill = model)) +
  geom_col() +
  coord_flip() +
  labs(title = "Model Comparison - Accuracy",
       x = "Model", y = "Accuracy") +
  theme_minimal()
```

### Select Best Model

```r
# Select based on metric
best_model <- select_best_model(results, metric = "roc_auc")

# Extract workflow
best_workflow <- extract_workflow(best_model)

# Make predictions
predictions <- predict(best_workflow, new_data = test_data)

# Get probabilities (for classification)
probabilities <- predict(best_workflow, new_data = test_data, type = "prob")
```

---

## Healthcare AI (healthyR.ai)

### Building Healthcare-Specific Models

```r
library(healthyR.ai)

# Example: Readmission prediction
# Prepare data
readmit_data <- patient_data %>%
  mutate(
    readmitted_30d = factor(readmitted_30d, levels = c("No", "Yes"))
  )

# Split data
split <- hai_split_data(readmit_data, prop = 0.8)

# Build model
model <- hai_auto_model(
  .data = split$train,
  .target = readmitted_30d,
  .model_type = "classification"
)

# Evaluate
evaluation <- hai_evaluate_model(model, split$test)
print(evaluation)

# Make predictions
new_predictions <- hai_predict(model, new_data = new_patients)
```

### Feature Importance

```r
# Extract feature importance
importance <- hai_feature_importance(model)

# Visualize
ggplot(importance, aes(x = reorder(feature, importance), y = importance)) +
  geom_col(fill = "steelblue") +
  coord_flip() +
  labs(title = "Feature Importance",
       x = "Feature", y = "Importance") +
  theme_minimal()
```

---

## Model Building

### Classification Models

#### Logistic Regression

```r
library(parsnip)

# Define model
logistic_spec <- logistic_reg() %>%
  set_engine("glm") %>%
  set_mode("classification")

# Create workflow
logistic_wf <- workflow() %>%
  add_recipe(data_recipe) %>%
  add_model(logistic_spec)

# Fit model
logistic_fit <- fit(logistic_wf, data = train_data)

# Predictions
logistic_pred <- predict(logistic_fit, new_data = test_data, type = "prob")
```

#### Random Forest

```r
# Define model
rf_spec <- rand_forest(
  mtry = tune(),
  trees = 1000,
  min_n = tune()
) %>%
  set_engine("ranger", importance = "impurity") %>%
  set_mode("classification")

# Create workflow
rf_wf <- workflow() %>%
  add_recipe(data_recipe) %>%
  add_model(rf_spec)

# Hyperparameter tuning
rf_grid <- grid_regular(
  mtry(range = c(2, 10)),
  min_n(range = c(2, 20)),
  levels = 5
)

# Tune model
rf_tune <- tune_grid(
  rf_wf,
  resamples = cv_folds,
  grid = rf_grid,
  metrics = metric_set(roc_auc, accuracy)
)

# Select best parameters
best_rf <- select_best(rf_tune, metric = "roc_auc")

# Finalize workflow
final_rf_wf <- finalize_workflow(rf_wf, best_rf)

# Fit final model
final_rf_fit <- fit(final_rf_wf, data = train_data)
```

#### XGBoost

```r
# Define model
xgb_spec <- boost_tree(
  trees = 1000,
  tree_depth = tune(),
  min_n = tune(),
  loss_reduction = tune(),
  sample_size = tune(),
  mtry = tune(),
  learn_rate = tune()
) %>%
  set_engine("xgboost") %>%
  set_mode("classification")

# Create workflow
xgb_wf <- workflow() %>%
  add_recipe(data_recipe) %>%
  add_model(xgb_spec)

# Define grid
xgb_grid <- grid_latin_hypercube(
  tree_depth(),
  min_n(),
  loss_reduction(),
  sample_size = sample_prop(),
  finalize(mtry(), train_data),
  learn_rate(),
  size = 30
)

# Tune with parallel processing
library(doParallel)
cl <- makeCluster(detectCores() - 1)
registerDoParallel(cl)

xgb_tune <- tune_grid(
  xgb_wf,
  resamples = cv_folds,
  grid = xgb_grid,
  control = control_grid(save_pred = TRUE)
)

stopCluster(cl)

# Select best
best_xgb <- select_best(xgb_tune, metric = "roc_auc")
final_xgb_fit <- finalize_workflow(xgb_wf, best_xgb) %>%
  fit(data = train_data)
```

### Regression Models

#### Linear Regression

```r
# Define model
lm_spec <- linear_reg() %>%
  set_engine("lm") %>%
  set_mode("regression")

# Fit
lm_wf <- workflow() %>%
  add_recipe(data_recipe) %>%
  add_model(lm_spec)

lm_fit <- fit(lm_wf, data = train_data)

# Predictions
lm_pred <- predict(lm_fit, new_data = test_data)
```

#### Elastic Net

```r
# Define model
enet_spec <- linear_reg(
  penalty = tune(),
  mixture = tune()
) %>%
  set_engine("glmnet") %>%
  set_mode("regression")

# Create workflow
enet_wf <- workflow() %>%
  add_recipe(data_recipe) %>%
  add_model(enet_spec)

# Define grid
enet_grid <- grid_regular(
  penalty(),
  mixture(),
  levels = 10
)

# Tune
enet_tune <- tune_grid(
  enet_wf,
  resamples = cv_folds,
  grid = enet_grid
)

# Finalize
best_enet <- select_best(enet_tune, metric = "rmse")
final_enet_fit <- finalize_workflow(enet_wf, best_enet) %>%
  fit(data = train_data)
```

---

## Model Evaluation

### Classification Metrics

```r
library(yardstick)

# Get predictions
test_pred <- predict(final_model, new_data = test_data, type = "prob") %>%
  bind_cols(predict(final_model, new_data = test_data)) %>%
  bind_cols(select(test_data, outcome))

# Confusion matrix
conf_mat(test_pred, truth = outcome, estimate = .pred_class)

# ROC-AUC
roc_auc(test_pred, truth = outcome, .pred_Yes)

# Accuracy
accuracy(test_pred, truth = outcome, estimate = .pred_class)

# Sensitivity and Specificity
sens(test_pred, truth = outcome, estimate = .pred_class)
spec(test_pred, truth = outcome, estimate = .pred_class)

# F1 Score
f_meas(test_pred, truth = outcome, estimate = .pred_class)

# ROC Curve
roc_curve(test_pred, truth = outcome, .pred_Yes) %>%
  autoplot()
```

### Regression Metrics

```r
# Get predictions
test_pred <- predict(final_model, new_data = test_data) %>%
  bind_cols(select(test_data, los))

# RMSE
rmse(test_pred, truth = los, estimate = .pred)

# MAE
mae(test_pred, truth = los, estimate = .pred)

# R-squared
rsq(test_pred, truth = los, estimate = .pred)

# Predicted vs Actual plot
ggplot(test_pred, aes(x = los, y = .pred)) +
  geom_point(alpha = 0.5) +
  geom_abline(slope = 1, intercept = 0, color = "red", linetype = "dashed") +
  labs(title = "Predicted vs Actual",
       x = "Actual", y = "Predicted") +
  theme_minimal()
```

### Cross-Validation

```r
# Fit with cross-validation
cv_results <- fit_resamples(
  workflow,
  resamples = cv_folds,
  metrics = metric_set(roc_auc, accuracy, sens, spec),
  control = control_resamples(save_pred = TRUE)
)

# Collect metrics
collect_metrics(cv_results)

# Show individual fold performance
collect_metrics(cv_results, summarize = FALSE)

# Visualize
autoplot(cv_results)
```

---

## Deployment

### Save Model

```r
# Save trained model
saveRDS(final_model, "models/readmission_model.rds")

# Save recipe
saveRDS(prepped_recipe, "models/data_recipe.rds")

# Save as bundle (better for deployment)
library(bundle)
model_bundle <- bundle(final_model)
saveRDS(model_bundle, "models/model_bundle.rds")
```

### Load and Use Model

```r
# Load model
loaded_model <- readRDS("models/readmission_model.rds")

# Or load bundle
model_bundle <- readRDS("models/model_bundle.rds")
loaded_model <- unbundle(model_bundle)

# Make predictions
new_predictions <- predict(loaded_model, new_data = new_patients)
```

### API Deployment with Plumber

```r
# api.R
library(plumber)
library(healthyverse)

# Load model
model <- readRDS("models/readmission_model.rds")

#* @apiTitle Readmission Prediction API
#* @apiDescription Predict 30-day readmission risk

#* Predict readmission risk
#* @param age:numeric Patient age
#* @param gender:string Patient gender
#* @param los:numeric Length of stay
#* @post /predict
function(age, gender, los) {
  # Create data frame
  new_data <- data.frame(
    age = as.numeric(age),
    gender = as.character(gender),
    los = as.numeric(los)
  )
  
  # Predict
  prediction <- predict(model, new_data, type = "prob")
  
  # Return
  list(
    risk_probability = prediction$.pred_Yes,
    risk_category = ifelse(prediction$.pred_Yes > 0.5, "High", "Low")
  )
}

# Run with: plumber::pr("api.R") %>% pr_run(port = 8000)
```

---

## Best Practices

### 1. Data Quality

```r
# Always check data quality
summary(data)
skimr::skim(data)

# Check for:
# - Missing values
# - Outliers
# - Class imbalance
# - Data leakage
```

### 2. Feature Engineering

```r
# Domain knowledge is crucial
# Create meaningful features:
data <- data %>%
  mutate(
    # Interaction terms
    age_los_interaction = age * los,
    
    # Polynomial features
    age_squared = age^2,
    
    # Binning
    age_group = cut(age, breaks = c(0, 18, 65, 100)),
    
    # Date features
    admit_day_of_week = weekdays(admit_date),
    admit_month = month(admit_date),
    
    # Ratios
    lab_ratio = lab1 / lab2
  )
```

### 3. Model Selection

- Start simple (logistic regression, linear regression)
- Try ensemble methods (random forest, XGBoost)
- Use cross-validation for all comparisons
- Consider interpretability vs performance tradeoff

### 4. Handling Imbalanced Data

```r
# Upsampling minority class
library(themis)

balanced_recipe <- recipe(outcome ~ ., data = train_data) %>%
  step_upsample(outcome, over_ratio = 1) %>%
  # ... other steps

# Downsampling majority class
balanced_recipe <- recipe(outcome ~ ., data = train_data) %>%
  step_downsample(outcome, under_ratio = 1) %>%
  # ... other steps

# SMOTE
balanced_recipe <- recipe(outcome ~ ., data = train_data) %>%
  step_smote(outcome) %>%
  # ... other steps
```

### 5. Model Interpretability

```r
# SHAP values (for tree-based models)
library(shapr)

# LIME (Local Interpretable Model-agnostic Explanations)
library(lime)

explainer <- lime(train_data, final_model)
explanation <- explain(test_data[1:5, ], explainer, n_features = 5)
plot_features(explanation)

# Variable importance
library(vip)
vip(final_model)
```

### 6. Monitoring

```r
# Track model performance over time
performance_log <- data.frame(
  date = Sys.Date(),
  accuracy = test_accuracy,
  auc = test_auc,
  data_size = nrow(train_data)
)

# Alert if performance degrades
if (test_accuracy < 0.8) {
  warning("Model performance below threshold. Consider retraining.")
}
```

---

## Complete Example

```r
library(healthyverse)
library(tidymodels)

# 1. Load data
data <- read.csv("readmissions.csv") %>%
  mutate(readmitted = factor(readmitted, levels = c("No", "Yes")))

# 2. Split data
set.seed(123)
data_split <- initial_split(data, prop = 0.8, strata = readmitted)
train_data <- training(data_split)
test_data <- testing(data_split)

# 3. Create recipe
data_recipe <- recipe(readmitted ~ ., data = train_data) %>%
  step_impute_median(all_numeric()) %>%
  step_dummy(all_nominal(), -all_outcomes()) %>%
  step_normalize(all_numeric()) %>%
  step_corr(all_numeric(), threshold = 0.9)

# 4. Define models
models <- list(
  logistic = logistic_reg() %>% set_engine("glm"),
  rf = rand_forest() %>% set_engine("ranger") %>% set_mode("classification"),
  xgb = boost_tree() %>% set_engine("xgboost") %>% set_mode("classification")
)

# 5. Create workflows
workflows <- map(models, ~workflow() %>% add_recipe(data_recipe) %>% add_model(.x))

# 6. Fit models
fits <- map(workflows, ~fit(.x, data = train_data))

# 7. Evaluate
cv_folds <- vfold_cv(train_data, v = 5)
cv_results <- map(workflows, ~fit_resamples(.x, resamples = cv_folds))
map(cv_results, collect_metrics)

# 8. Select best and finalize
best_model <- fits$xgb

# 9. Final evaluation
test_pred <- predict(best_model, test_data, type = "prob") %>%
  bind_cols(predict(best_model, test_data)) %>%
  bind_cols(select(test_data, readmitted))

# Metrics
conf_mat(test_pred, truth = readmitted, estimate = .pred_class)
roc_auc(test_pred, truth = readmitted, .pred_Yes)

# 10. Save model
saveRDS(best_model, "readmission_model.rds")
```

---

## Next Steps

- Review [Healthcare Data Analysis](Healthcare-Data-Analysis.md) for feature engineering ideas
- Learn [Performance Optimization](Performance-Optimization.md) for large datasets
- Check [API Reference](API-Reference.md) for function details

---

**Last Updated**: November 2025  
**Version**: 1.1.0.9000
