## House-Prices---Advanced-Regression
My attempt on the famous Ames Housing dataset
## 1. Data Loading
- Loaded the Ames Housing dataset (1,460 rows × 81 columns)
- Source: Kaggle House Prices - Advanced Regression Techniques
## 2. Dataset Overview
- The dataset contains 1,460 houses with 81 features\columns.
- Mix of numerical and categorical variables.
- Target variable: 'SalePrice'
## 3. Target Variable Analysis
- `SalePrice` is right-skewed.
- Most houses are priced between $100,000 – $250,000.
- Log transformation was later applied to make the distribution more normal.
## 4. Missing Values
- Several columns had missing values (e.g. PoolQC, MiscFeature, Alley, Fence, FireplaceQu, LotFrontage, etc.).
- Missing values in many categorical columns actually meant the feature was absent (e.g. no pool, no alley).
- These were handled later during preprocessing.
## 5. Correlation Analysis
- Strongest features correlated with `SalePrice`:
  - OverallQual (0.79)
  - GrLivArea (0.71)
  - GarageCars (0.64)
  - GarageArea (0.62)
  - TotalBsmtSF (0.61)
- These features were prioritized during modeling.
## 6. Correlation Heatmap
- Visualized the relationships between the top correlated features.
- Confirmed strong positive correlations between OverallQual, GrLivArea, GarageCars, and SalePrice.
## 7. Feature Relationships
- `OverallQual` shows a clear positive relationship with SalePrice (higher quality → higher price).
- `GrLivArea` also shows a strong positive relationship, with a few outliers visible.
## 8. Handling Missing Values
- Categorical columns where NaN meant “None” (e.g. PoolQC, Alley, Fence) were filled with `'None'`.
- `LotFrontage` was filled with the median.
- Other numerical columns were filled with 0.
- Remaining categorical columns were filled with the mode.
- Final result: 0 missing values.
## 9. Log Transformation
- `SalePrice` was right-skewed.
- Applied `np.log1p()` transformation to make the distribution more normal.
- This helped improve model performance.
## 10. Data Preparation
- Dropped `Id` and the original `SalePrice`.
- Used the log-transformed target (`SalePrice_log` / `LogSalePrice`).
- Prepared baseline features before applying feature engineering.
## 11. Categorical Encoding
- Applied One-Hot Encoding using `pd.get_dummies()`.
- Number of features increased from 79 to 301 after encoding.
## 12. Train-Validation Split
- Split the data into 80% training and 20% validation sets.
- Used `random_state=42` for reproducibility.
## 13. Baseline Model
- Trained a Linear Regression model as the baseline.
- Validation RMSE: **0.13206**
## 14. Random Forest
- Trained a Random Forest model with 100 trees.
- Validation RMSE: **0.14571**
## 15. XGBoost (Initial)
- Trained an initial XGBoost model.
- Validation RMSE: **0.13839**
## 16. Feature Engineering
Created the following new features:
- `TotalSF` = TotalBsmtSF + 1stFlrSF + 2ndFlrSF
- `TotalBath` = FullBath + HalfBath + BsmtFullBath + BsmtHalfBath
- `HouseAge` = YrSold - YearBuilt
- `RemodeledAge` = YrSold - YearRemodAdd
- `IsNew` = whether the house was built in the year it was sold
## 17. Data Preparation After Feature Engineering
- Recreated features and target after adding engineered features.
- Applied One-Hot Encoding again.
- Split the data into training and validation sets.
## 18. Data Leakage Detection
- After feature engineering, Linear Regression produced an unrealistic RMSE of 0.00000.
- This was caused by a leaked column (`SalePrice_log`) that had a perfect correlation with the target.
- The issue was investigated and fixed in the following steps.
## 19. Data Leakage Investigation
- Checked whether `SalePrice` or `LogSalePrice` were still present in the features.
- Both were correctly removed.
- Further investigation (correlation analysis) revealed a leaked column named `SalePrice_log`.
## 20. Confirming the Leakage
- Compared the first 10 predictions with the actual values.
- Predictions matched the target exactly → confirmed data leakage.
## 21. Root Cause of Data Leakage
- Correlation analysis showed that the column `SalePrice_log` had a perfect correlation (1.0) with the target.
- This column was accidentally left in the features and caused the unrealistic performance.
## 22. Fixing Data Leakage
- Removed the leaking column `SalePrice_log`.
- Re-split the data after removing the problematic feature.
- Models were retrained after the fix.
## 23. Results After Fixing Leakage
After removing the leaking column, the models returned realistic scores again:

| Model              | RMSE    |
|--------------------|---------|
| Linear Regression  | 0.13187 |
| Random Forest      | 0.14502 |
| XGBoost            | 0.13801 |
## 24. Outlier Detection
- Plotted `GrLivArea` vs `SalePrice`.
- Identified two clear outliers with very large living area but relatively low sale price.
## 25. Outlier Removal
- Removed 2 clear outliers where `GrLivArea > 4000` and `SalePrice < 300000`.
- Dataset size reduced from 1460 to 1458 rows.
## 26. Data Preparation After Outlier Removal
- Recreated the features and target after removing outliers.
- Applied One-Hot Encoding again.
- Ensured the leaking column was removed.
- Split the data into training and validation sets.
## 27. Results After Outlier Removal

| Model              | RMSE    |
|--------------------|---------|
| Linear Regression  | 0.13692 |
| Random Forest      | 0.14722 |
| **XGBoost**        | **0.12226** |

Removing the outliers significantly improved the XGBoost performance.
## 28. Hyperparameter Tuning
Tuned the XGBoost model with the following parameters:
- `n_estimators=1000`
- `learning_rate=0.03`
- `max_depth=4`
- `subsample=0.8`
- `colsample_bytree=0.8`
- `reg_alpha=0.1`
- `reg_lambda=1.0`

**Result:** Tuned XGBoost RMSE = **0.1204**
## 29. Ordinal Encoding
Applied ordinal encoding to quality-related features that have a natural order:

- ExterQual, ExterCond
- BsmtQual, BsmtCond
- HeatingQC, KitchenQual
- FireplaceQu, GarageQual, GarageCond, PoolQC

Mapped values from poor → excellent into numerical scores (e.g. Po=1, Fa=2, TA=3, Gd=4, Ex=5).
## 30. Data Preparation After Ordinal Encoding
- Applied ordinal encoding to quality features.
- One-hot encoded the remaining categorical columns.
- Final feature set size: 265 columns.
## 31. Final Model Performance

**Best Model:** XGBoost + Feature Engineering + Outlier Removal + Hyperparameter Tuning + Ordinal Encoding

**Final Validation RMSE: 0.1183**
