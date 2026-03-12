# Air Quality Analysis and Regression Modeling

## Overview

This project performs a complete exploratory and predictive analysis on the **Air Quality dataset** downloaded from Kaggle. It includes data acquisition, cleaning, feature engineering, missing-value analysis, exploratory statistical analysis, correlation analysis, preprocessing, and the training and evaluation of several regression models to predict **benzene concentration (`C6H6(GT)`)**.

The workflow is designed to be **reproducible**, since the dataset is downloaded automatically, copied into the current working directory, and loaded dynamically without depending on hardcoded local paths.

---

## Main Objectives

The code aims to:

- Download and load the dataset automatically.
- Clean and preprocess the data.
- Transform temporal variables into interpretable categorical features.
- Analyze the statistical properties of pollutant variables.
- Study missing values and variable relationships.
- Build predictive models for **`C6H6(GT)`**.
- Compare different regression approaches:
  - Simple Linear Regression
  - Multiple Linear Regression
  - Decision Tree Regression

---

## Dataset

The dataset used is:

- **Kaggle identifier:** `fedesoriano/air-quality-data-set`

It contains air quality measurements, sensor values, and meteorological variables such as:

- `CO(GT)`
- `NOx(GT)`
- `NO2(GT)`
- `C6H6(GT)`
- `PT08` sensor measurements
- `T`, `RH`, `AH`
- `Date`, `Time`

---

## Workflow Description

## 1. Dataset Download and Loading

The code:

- Downloads the latest version of the dataset from Kaggle.
- Copies its contents to the current directory.
- Detects the CSV file automatically.
- Loads it into a Pandas DataFrame.

This ensures that the workflow can be executed on different machines without manually configuring file paths.

---

## 2. Initial Inspection

The dataset is inspected using:

- `df.info()`
- `len(df)`
- `df.describe()`

This allows checking:

- Number of rows and columns
- Data types
- Missing values
- Basic descriptive statistics

---

## 3. Data Cleaning

Several cleaning steps are applied:

- Duplicate rows are removed.
- The value `-200` is replaced with `NaN`, since it represents missing values in the original dataset.
- Rows with all values missing are dropped.
- Empty columns such as `Unnamed: 15` and `Unnamed: 16` are removed.

After this process, the dataset contains only meaningful and non-redundant records.

---

## 4. Temporal Feature Engineering

The original `Date` and `Time` columns are converted into proper Python date/time objects and then combined into a `Datetime` variable.

From this timestamp, new categorical temporal features are created:

- `Mes_cat` → month
- `Dia_semana` → day of the week
- `Fin_de_semana` → weekend indicator
- `Franja_horaria` → time range of the day
- `Estacion` → season of the year

These variables are later used to study temporal patterns in air pollution.

---

## 5. Missing Value Analysis

The code calculates:

- The number of missing values per column
- The percentage of missing values per column

This helps identify problematic variables, especially:

- `NMHC(GT)` with a very high proportion of missing values
- Other pollutant variables with moderate missingness
- Temporal derived variables with no missing values

---

## 6. Exploratory Statistical Analysis

The code studies the statistical behavior of pollutant variables such as:

- `CO(GT)`
- `NOx(GT)`
- `NO2(GT)`

Using:

- Descriptive statistics
- Histograms
- KDE curves
- Boxplots

This allows identifying:

- Right-skewed distributions
- Heavy tails
- Outliers
- Non-normal behavior

Additionally, goodness-of-fit tests are performed to compare the empirical distributions with theoretical ones such as:

- Normal
- Skew-Normal
- Lognormal
- Gamma
- Weibull

The results suggest that these variables do not follow simple parametric distributions well.

---

## 7. Correlation Analysis

The code analyzes relationships among variables using:

- **Spearman correlation** for monotonic relationships
- **Pearson correlation** for linear relationships
- Scatterplot matrices for visual exploration

It also evaluates the association between categorical time-derived variables and numerical variables using the **correlation ratio (`η`)**.

This helps identify:

- Strong relations between pollutant concentrations and sensor signals
- Temporal effects linked to season, month, and time of day
- The strongest predictors for the target variable `C6H6(GT)`

---

## 8. Target Variable Selection

The project chooses **`C6H6(GT)`** (benzene concentration) as the regression target because:

- It is a relevant pollutant from a public health perspective.
- It shows strong relationships with other sensor variables.
- It is suitable for supervised regression modeling.

From the exploratory analysis, **`PT08.S2(NMHC)`** emerges as the strongest single predictor.

---

## 9. Train/Test Split

Before applying any preprocessing, the data is split into:

- **Training set**
- **Test set**

This prevents **data leakage**, ensuring that imputations, scaling, and encoding are learned only from the training set and then applied to test data.

---

## 10. Missing Value Imputation

The code uses **MICE** via `IterativeImputer` to fill missing numerical values.

Key points:

- The imputer is fitted only on training data.
- The same fitted imputer is applied to the test set.
- The variable `NMHC(GT)` is excluded due to its extremely high missing-value percentage.

The distribution of variables before and after imputation is then compared using:

- Histograms
- KDE
- Boxplots
- KS tests

This verifies that the imputation preserves the overall distribution reasonably well.

---

## 11. Scaling and Encoding

### Numerical variables
The code applies **RobustScaler**, which:

- Centers by the median
- Scales by the interquartile range (IQR)

This is more robust to outliers than standard normalization methods.

### Categorical variables
The categorical temporal features are transformed using **One-Hot Encoding**.

Again, fitting is done only on the training set to avoid leakage.

---

## 12. Regression Models

Three predictive models are trained and compared.

### A. Simple Linear Regression
Uses only one predictor:

- `PT08.S2(NMHC)`

This model is intended to test how well benzene concentration can be explained by the strongest single variable.

### B. Multiple Linear Regression
Uses all processed predictors after:

- imputation
- scaling
- one-hot encoding

This model captures linear effects from multiple variables simultaneously.

### C. Decision Tree Regressor
A tree-based regression model is trained with specified hyperparameters such as:

- `criterion="poisson"`
- `max_depth=10`
- `min_samples_split=10`
- `min_samples_leaf=2`

This model is more flexible and can capture nonlinear relationships.

---

## 13. Model Evaluation

The models are evaluated using:

- **MSE** → Mean Squared Error
- **RMSE** → Root Mean Squared Error
- **MAE** → Mean Absolute Error
- **R²** → Coefficient of determination
- **RMSLE** for the decision tree model

Residual analysis is also performed through:

- Residuals vs predictions
- Residuals vs true values
- Residual histograms
- Q–Q plots

These diagnostics help determine:

- goodness of fit
- normality of errors
- presence of systematic structure
- potential nonlinearities not captured by linear models

---

## Main Findings

The code is designed to support conclusions such as:

- Air pollutant variables are not normally distributed.
- Several variables exhibit skewness, heavy tails, and outliers.
- Missing values must be treated carefully.
- `PT08.S2(NMHC)` is the most informative single predictor for `C6H6(GT)`.
- Multiple linear regression improves over simple linear regression.
- Decision tree regression can outperform linear models when nonlinear patterns are present.

---

## Libraries Used

The code relies mainly on:

- `os`
- `shutil`
- `pathlib`
- `kagglehub`
- `pandas`
- `numpy`
- `matplotlib`
- `scipy`
- `scikit-learn`

---

## How to Run

1. Install the required dependencies.
2. Make sure Kaggle access is configured if needed by `kagglehub`.
3. Run the notebook or script step by step.
4. The dataset will be downloaded automatically.
5. The code will perform:
   - cleaning
   - analysis
   - preprocessing
   - training
   - evaluation

---

## Project Structure

The code follows this logical order:

1. Import libraries  
2. Download and load dataset  
3. Inspect data  
4. Clean and preprocess  
5. Feature engineering  
6. Missing value analysis  
7. Exploratory analysis  
8. Correlation study  
9. Train/test split  
10. Imputation  
11. Scaling and encoding  
12. Model training  
13. Evaluation and diagnostics  

---

## Notes

- The workflow is especially focused on **reproducibility** and **good preprocessing practice**.
- It avoids data leakage by fitting transformations only on the training set.
- It combines **descriptive statistics**, **visual analysis**, and **machine learning** in a single pipeline.

---

## Author

**Toni Esteve Gene**
