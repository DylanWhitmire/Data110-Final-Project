# Housing Price Prediction (Multiple Linear Regression + Hypothesis Tests)

## Overview
This project models **home sale prices (SalePrice)** using a **multiple linear regression** approach and supports the modeling decisions with **hypothesis testing** and **multicollinearity checks**. The goal is to identify which housing features are most strongly associated with sale price and to understand when “simple correlations” can be misleading in a multivariable setting.

## Key Questions
1. Which features are the strongest drivers of **SalePrice** in a multivariable model?
2. Why can a feature (like **FullBath**) look positively related to SalePrice in a scatterplot, but get a negative regression coefficient?
3. Does **CentralAir** relate to higher SalePrice?
4. Does **remodeling** (remodeled vs. not remodeled) relate to higher SalePrice?

## Data & Features
We work with a cleaned housing dataset containing **SalePrice** and common housing attributes.  
Our final regression predictors focus on clear, interpretable drivers of value:

- **Size:** `1stFlrSF`, `2ndFlrSF`  
- **Amenities:** `FullBath`, `Fireplaces`, `GarageCars`, `OpenPorchSF`  
- **Age:** `Age`

> Note: predictors are **z-score normalized** so coefficients are comparable across features.

## Methods
### 1) Multiple Linear Regression (Normalized Predictors)
- Split: **90% train / 10% test**
- Normalize predictors with z-scores
- Fit `sklearn.linear_model.LinearRegression`
- Evaluate with **R²** on train and test

### 2) Multicollinearity Check (VIF)
We computed **Variance Inflation Factors (VIF)** to flag overlapping predictors.
High VIF values suggest coefficient instability and help explain why some coefficients (e.g., **FullBath**) behave unexpectedly.

### 3) Hypothesis Test #1: CentralAir (A/B Test)
We tested whether the **mean SalePrice** differs between homes **with** and **without** central air using a **two-sample t-test**.

### 4) Hypothesis Test #2: Remodeled vs. Not Remodeled (Simulation)
Because our dataset includes `YearBuilt` and `YearRemodAdd` (timing information), we also directly tested whether **being remodeled** is associated with higher SalePrice using a **randomization simulation**:
- Shuffle labels many times
- Recompute the difference in mean SalePrice each time
- Compare to the observed difference to estimate a p-value

## Results (Main Takeaways)
### Regression performance
- **R² (train): ~0.71**
- **R² (test): ~0.74**
Train and test R² are close, which suggests the model generalizes reasonably well for a simple linear baseline.

### Normalized coefficients (interpretation)
Because predictors are normalized, each coefficient represents the expected **$ change in SalePrice for a 1 standard deviation increase** in that feature (holding others constant).
- Largest positive effects: **`1stFlrSF`, `2ndFlrSF`**
- Strong negative effect: **`Age`**
- Amenities (garage, fireplaces) add value but are typically smaller than size/age signals.

### FullBath “weird” negative coefficient
Although `FullBath` shows a positive pattern in scatterplots, its coefficient can turn negative in multiple regression due to **suppression / multicollinearity**—it overlaps heavily with size-related predictors, making individual coefficients harder to interpret.

### VIF summary
VIF indicated the strongest overlap comes from:
- `1stFlrSF`, `FullBath`, `GarageCars` (highest VIFs)
Other predictors had much lower VIFs. We treat this as a flag for interpretation and a direction for follow-up work (e.g., Ridge/Lasso).

### Hypothesis Test Conclusions
**CentralAir (t-test):**
- Statistically significant difference in mean SalePrice between groups  
- Homes with central air sold for about **$80,923 more on average** in this dataset  
- Even with significance, CentralAir may be smaller in importance than core drivers like size/age in the full model

**Remodeled vs. Not Remodeled (simulation):**
- Simulated **p-value ≈ 0.7989**
- We fail to reject the null; the observed difference is very plausible under “no real effect” (in the tested direction)

## Real Estate Recommendation (Practical Takeaway)
As a real estate consultant, we recommend prioritizing **usable living space and high-impact structural features** (size, age/condition, garage capacity) and treating remodeling decisions **case-by-case** based on costs and local market context, rather than assuming remodeling will reliably increase sale price.

## Repo Structure (example)
