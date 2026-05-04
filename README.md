# European Urban Centres — Data Analysis and Predictive Modelling (ANDOC)

A complete data mining pipeline applied to a dataset of **1,165 European urban centres**, following the **CRISP-DM methodology**. The project was developed for the Data Analysis and Knowledge Management course (ANDOC), Master's in Industrial Engineering and Management at ISEP.

The dataset was provided by the **Joint Research Centre (JRC) of the European Commission**, as part of the GHS Urban Centre Database (GHS-UCDB R2024A).

---

## Objectives

The project addresses two predictive tasks in support of European Commission policy-making:

- **Regression:** predict the average GDP of each urban centre in 2020 (`SC_GDP_AVG_2020`)
- **Classification:** categorise the Human Development Index (HDI) into four discrete levels — Low, Medium, High, Very High (`SC_SEC_HDI_2020`)

---

## Dataset

- **Source:** Joint Research Centre (JRC), European Commission — GHS-UCDB R2024A
- **File:** `europeCitiesData.xlsx`
- **Observations:** 1,165 European urban centres
- **Variables:** 33 (30 numerical, 3 categorical)

Variable types include continuous numerical (GDP, area, population density), discrete numerical (number of inhabitants), ordinal categorical (HDI classification) and nominal categorical (country, region).

> The dataset file is not included in this repository due to licensing. Contact the JRC or your institution for access.

---

## Methodology — CRISP-DM

The project follows all six phases of the CRISP-DM framework:

**1. Business Understanding**
Understanding socioeconomic and territorial patterns across European cities to support EC policies on territorial cohesion and sustainable urban development.

**2. Data Understanding**
- 30 numerical and 3 categorical variables identified
- GDP mean ~72M USD (std ~51M) — strong positive skew; outliers linked to major capitals (Paris, London, Madrid)
- HDI mean ~0.871 (range 0.64–0.98) — concentrated distribution consistent with European context
- Population mean ~259K (max ~14.3M); area mean ~61 km² (max ~1,649 km²)
- Variables `SC_SEC_HDI_2020`, `SC_SEC_LET_2020`, `SC_SEC_SYT_2020` — 2 missing values each
- Pharmaceutical variables (`HL_FCL_PHA_2020`, `HL_FDE_PHA_2020`, `HL_FPC_PHA_2020`) — ~40% missing
- Outliers retained as contextually valid (heterogeneity between small cities and major metropolises)

**3. Data Preparation**
- Removal of highly correlated variables (|r| > 0.95): `SC_GDP_AVG_2000`, `SC_GDP_AVG_2005`, `SC_GDP_AVG_2010`, `SC_GDP_AVG_2015`, `IN_ROA_LEN_2020`
- Simple median imputation for variables with less than 0.2% missing values
- Conditional imputation by country for pharmaceutical (~40% missing) and hospital (~5% missing) variables
- Ordinal encoding of `GC_DEV_WIG_2020`: Lower Middle=1, Upper Middle=2, High Income=3
- Log transformation (`log1p`) applied to: `GC_POP_TOT_2020`, `GC_UCA_KM2_2020`, `SC_GDP_AVG_1990`, `SC_GDP_AVG_1995`, `SC_GDP_AVG_2020`
- Z-score standardisation (StandardScaler) applied to all continuous numerical variables, excluding targets and ordinal variables

**4. Modelling**

For **GDP Regression:**
- One-hot encoding of country variable; city name excluded
- 70/30 train/test split with `random_state=42`
- Models: KNN Regressor, Decision Tree Regressor, Random Forest Regressor (GridSearchCV)

For **HDI Classification:**
- HDI discretised into 4 classes using quartile-based binning (0=Low, 1=Medium, 2=High, 3=Very High)
- All HDI-related columns excluded from features to prevent data leakage
- Stratified 70/30 train/test split
- Models: KNN Classifier, Decision Tree Classifier, Random Forest Classifier (1,000 trees)

**5. Evaluation**

GDP Regression results:

| Model | MSE | RMSE | MAE | R² |
|---|---|---|---|---|
| KNN | 0.0190 | 0.1408 | 0.1002 | 0.9533 |
| Decision Tree (depth=6) | 0.0159 | 0.1262 | 0.0895 | 0.9625 |
| **Random Forest** | **0.0106** | **0.1029** | **0.0755** | **0.9751** |

HDI Classification results:

| Model | Optimised Parameters | Final Accuracy |
|---|---|---|
| KNN | k = 8 | 75.71% |
| Decision Tree | max_depth = 9 | 88.29% |
| **Random Forest** | **1,000 trees** | **90.29%** |

Random Forest was selected as the best model in both tasks, offering superior generalisation, robustness to overfitting, and interpretable feature importance scores.

**6. Deployment**
Not implemented in this academic context. In a real-world scenario, the Random Forest models would be integrated into an operational system with continuous monitoring and model updates.

---

## Key Findings

- GDP and HDI are strongly positively correlated — wealthier cities consistently show higher human development
- High Income group cities show higher GDP variability, driven by major metropolises
- HDI values cluster between 0.80 and 0.95 for most European cities, with a multimodal distribution reflecting North/South differences
- GDP grew consistently from 1990 to 2020, with a slight slowdown between 2015 and 2020
- Random Forest outperformed KNN and Decision Tree in both tasks

---

## Requirements

```
pandas
numpy
matplotlib
seaborn
scikit-learn
scipy
openpyxl
joblib
```

Install all dependencies:

```bash
pip install pandas numpy matplotlib seaborn scikit-learn scipy openpyxl joblib
```

---

## How to Run

1. Clone this repository
2. Place `europeCitiesData.xlsx` in the project root folder
3. Install the required dependencies
4. Open and run the notebook sequentially:

```bash
jupyter notebook European_Cities_ML_Analysis.ipynb
```

---

## Project Structure

```
├── European_Cities_ML_Analysis.ipynb   # Main notebook
├── europeCitiesData.xlsx               # Dataset (not included)
└── README.md                           # This file
```

---

## Author

**Filipa Sousa Neves** — 1211249   

Master's in Industrial Engineering and Management  
Instituto Superior de Engenharia do Porto (ISEP)  
[LinkedIn — Filipa Neves](https://www.linkedin.com/in/filipa-neves-15987a226)

---

## References

- Chapman et al. (2000). *CRISP-DM 1.0: Step-by-step data mining guide.*
- Breiman, L. (2001). *Random Forests. Machine Learning, 45*(1), 5–32.
- Hastie, T., Tibshirani, R., & Friedman, J. (2009). *The Elements of Statistical Learning* (2nd ed.). Springer.
- James, G., Witten, D., Hastie, T., & Tibshirani, R. (2021). *An Introduction to Statistical Learning* (2nd ed.).
- Pedregosa, F. (2011). *Scikit-learn: Machine Learning in Python.*
