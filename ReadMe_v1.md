
# General Description of the Repository

**This repository contains the project for Module 11 of the AI course.**

It is based on the analysis of a provided file (the original dataset comes from Kaggle with information on 3 million used cars), specifically `vehicles.csv`. This dataset contains information on 426K cars, and the goal is to understand the factors that make a car more or less expensive.

### Repository Structure:
- A **data** folder with the aforementioned file.
- An **images** folder containing generated image files.
- An **output** folder with the Excel files generated as a result of the analysis.

The notebook can be accessed via the following link: [View the notebook](Important_Feature_for_Car_Price-v3.ipynb).

The project is also available on GitHub: **https://github.com/GPSMAS/ImportantFeatureForCarPrice.git**.

### Project Overview:

The analysis was developed using the CRISP-DM framework, and consists of several sections (detailed in the file WCAC_v2), which are:

### Business Understanding

**The Business Question:** Identify key drivers of used car prices.

I need to **develop a model** to predict which features have the greatest impact on price.

**The Goal** is to apply supervised learning techniques to uncover significant patterns and relationships between the input variables (features) and the target variable (car price). Ultimately, this aims to determine which features have the greatest impact on price.

**This task involves** data understanding, data preparation, modeling, evaluation, and development.

### Data Understanding

- **Dataset Structure:** The dataset contains 426,880 rows and 18 columns. The target variable is `price`, and the other columns are features.
- **Data Types:** Columns like `price`, `year`, and `odometer` are numeric, while others are categorical.
- **Missing Data:** There is significant missing data in key columns such as `condition` (40.79%), `cylinders` (41.62%), and `size` (71.77%). Some records also have `price` and `odometer` set to zero.
- **Categorical Data Summary:** Variables like `manufacturer`, `fuel`, and `drive` have multiple unique values, some of which require cleaning due to inconsistencies or missing values. 
  - **Note:** `region` and `state` are highly correlated and `VIN` (vehicle identification number) will be excluded from the analysis.
  
- **Correlation between Categorical Features:** 
  I used Cramér's V to analyze correlations (excluding `region` and `state` to save computation time). The results can be seen in the following plots:
  - [HeatMap for Categorical Variables](images/Categorical_Variab__corr_All.jpg)
  - [HeatMap for Strong Correlations ( > 0.5)](images/Categorical_Variab_strong_corr.jpg)
  
- **Correlation Matrix for Numerical Features:**  
  You can find the correlation results in the following plot:
  - [Correlation Matrix between Numerical Features](images/numerical_Variab_corr.jpg)
  - No significant high correlations were found.

- **Duplicate Records:** No duplicate records were found.

- **Outliers and Unexpected Values:**  
  I performed a visual inspection for numerical variables through frequency distribution and box plots. You can find the plots below:
  - [Frequency Distribution](images/numerical_Variab_distribution.jpg)
  - [Box Plot](images/numerical_Variab_BoxPlot.jpg)

### Data Preparation

Based on the Data Understanding phase, I took the following steps:

1. **Handling Missing Values for Categorical Features:**
   - A strong relationship between `model` and other features was identified, so I used the `model` feature to fill missing values for `condition`, `cylinders`, `fuel`, `transmission`, `drive`, `size`, and `type`.

2. **Dropped Unnecessary Features:**
   - `VIN` and `paint_color` were dropped: **VIN** is not useful for our analysis, and **paint_color** had too much missing data and was considered less important.
   - `state` was dropped in favor of `region`.
   - `model` was dropped to avoid redundancy with other features.

3. **Handling Null and Inconsistent Values:**
   - I replaced missing values ("", " ") with `None` and dropped them.

4. **Numerical Feature Adjustments:**
   - Set limits for certain features based on visual inspection:
     - `price > 500` (Below this, prices were considered errors or irrelevant).
     - `odometer < 500,000` (Values above 500,000 were considered irrelevant).
     - `odometer > 50` (Values below 50 were considered errors).
     - Removed patterns like `123456789` in the `price` column, which indicated clear errors.

5. **Log Transformation:**
   - Since `price` and `odometer` distributions were skewed, I applied a log transformation. `year`, though non-Gaussian, did not require transformation.
   - Outliers were identified using box plots, and I applied the IQR (1.5) method to remove them. 
   - The cleaned, log-transformed dataset showed a more Gaussian distribution: 
     - [Cleaned Dataset Log-Transformed Frequency Distribution](images/log_price_odometer_distribution.jpg)
     - A strong relationship between `price` and `odometer` was observed:
     - [Log-Transformed Scatter Plot (Price vs. Odometer)](images/log_odometer_vs_log_price_scatter.jpg)

The final dataset retained about 62% of the original data, which is acceptable. A statistical check confirmed that the values were valid.

### Modeling

The following steps were performed for modeling:

- **Categorical Variables:** OneHotEncoding was used (with "first" category dropped).
- **Numerical Variables:** A scaler was applied.
- The models were trained on the cleaned dataset.

I used three different models:
- **Linear Regression (Ridge)**
- **Lasso**
- **ElasticNet**

For each model, the following were calculated:
- Best parameters
- Best cross-validation MSE
- Coefficients

### Evaluation

Based on the results, I chose the Ridge model as it produced the lowest MSE. The model was then trained with the best `alpha`, and permutation importance was applied to identify the top ten features.

- [Top 10 Features with Ridge](images/ridge_top_10_coefs_seaborn.png)

Additionally, I explored how each categorical feature impacted the price (in percentage terms) relative to the baseline in the following plots:
- [Condition Impact on Price](images/ridge_top_10_condition_changes.png)
- [Cylinders Impact on Price](images/ridge_top_10_cylinders_changes.png)
- [Drive Impact on Price](images/ridge_top_10_drive_changes.png)
- [Fuel Impact on Price](images/ridge_top_10_fuel_changes.png)
- [Manufacturer Impact on Price](images/ridge_top_10_manufacturer_changes.png)
- [Transmission Impact on Price](images/ridge_top_10_transmission_changes.png)
- [Title Status Impact on Price](images/ridge_top_10_title_status_changes.png)
- [Size Impact on Price](images/ridge_top_10_size_changes.png)
- [Region Impact on Price](images/ridge_top_10_region_changes.png)
- [Type Impact on Price](images/ridge_top_10_type_changes.png)
- [Year Impact on Price](images/ridge_top_10_year_changes.png)
- [Odometer Impact on Price](images/ridge_top_10_odometer_changes.png)

### Deployment

- We identified the features that have the greatest impact on price prediction.
- We also quantified how each feature impacts price, in percentage terms.
- The insights from this analysis could be useful for marketing, inventory management, and strategic decisions.

Finally, a report was developed for the client:
- [Report for Customer](Output/Report.docx)
