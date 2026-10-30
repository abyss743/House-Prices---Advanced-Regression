# House Prices - Advanced Regression Techniques

My solution for the classic Ames Housing dataset from Kaggle.

**Final Validation RMSE: 0.1183**

---

## Project Overview

This project predicts house sale prices using the Ames Housing dataset (1,460 houses with 79 explanatory features).

### Key Steps Taken:
- Exploratory Data Analysis
- Missing value treatment
- Feature Engineering
- Outlier removal
- Ordinal Encoding
- Hyperparameter tuning
- Data leakage detection & fixing

---

## Final Model Performance

| Stage                              | Model     | RMSE    |
|------------------------------------|-----------|---------|
| Baseline                           | Linear Regression | 0.13206 |
| After Feature Engineering          | XGBoost   | 0.13801 |
| After Outlier Removal              | XGBoost   | 0.12226 |
| After Hyperparameter Tuning        | XGBoost   | 0.1204  |
| **After Ordinal Encoding (Final)** | **XGBoost** | **0.1183** |

---

## Key Techniques Used

### 1. Feature Engineering
- `TotalSF` = TotalBsmtSF + 1stFlrSF + 2ndFlrSF
- `TotalBath`
- `HouseAge`
- `RemodeledAge`
- `IsNew`

### 2. Outlier Removal
Removed 2 clear outliers with very large `GrLivArea` but low `SalePrice`.

### 3. Ordinal Encoding
Applied ordinal encoding to quality-related features (`ExterQual`, `KitchenQual`, `BsmtQual`, etc.).

### 4. Data Leakage Detection
During experimentation, a leaked column (`SalePrice_log`) caused an unrealistic RMSE of 0.00000.  
The issue was identified through correlation analysis and fixed.

---

## Tech Stack

- Python
- pandas, numpy
- matplotlib, seaborn
- scikit-learn
- XGBoost

---

## How to Run

1. Open the notebook in Google Colab or Jupyter.
2. Run all cells in order.

---

## Author

Abyss Reverie
