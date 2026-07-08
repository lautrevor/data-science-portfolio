## **Data Science Portfolio**

Hi, I’m **Trevor Lau** — a UBC undergraduate specializing in Data Science. I’m interested in using data to analyze real-world systems, evaluate claims critically, and support better decision-making through disciplined analysis.

This repository contains selected projects that demonstrate my growing skills in:

- **Data cleaning and preprocessing**
- **Exploratory data analysis (EDA)**
- **Statistical reasoning and inference**
- **Data visualization for insight and communication**
- **Applying machine learning models responsibly**

Each project emphasizes clear problem formulation, transparent methods, and honest interpretation of results, with a focus on separating signal from noise.

---

## **Projects**

### **Project 1: KNN Regression — Student Performance**
Predicted student final grades using K-Nearest Neighbors regression with feature scaling and hyperparameter tuning. The project evaluates model performance using out-of-sample error metrics and discusses limitations of distance-based models in noisy, real-world data.

**Skills:** Python, pandas, scikit-learn, feature scaling, RMSE, model evaluation

---

### **Project 2: Signal vs Noise in Technical Indicators (VFV)**
Evaluated whether commonly used return-based technical indicators provide meaningful out-of-sample predictive value for next-day returns of VFV (Vanguard S&P 500 Index ETF). Using time-aware train/test splits and baseline comparisons, the analysis finds no meaningful improvement over naive predictors, highlighting the difficulty of short-horizon return prediction.

**Skills:** Time-series analysis, feature engineering, linear regression, baseline modeling, RMSE/MAE, financial data analysis

---

### **Project 3: Collision Risk Factor Analysis** (Status: In Progress)
Analysis of police-reported traffic collision microdata from Transport Canada's National Collision Database (2019–2020, ~471K records) to identify which factors are most associated with injury severity — the kind of risk analysis conducted by insurance and road-safety analytics teams. The project combines SQL-based exploratory querying (crash timing, weather and road conditions, crash type vs. severity, year-over-year trends including the COVID-era drop) with a multivariate logistic regression that isolates the independent effects of driver age, safety device use, weather, and year on injury probability, reported as interpretable odds ratios.

Data is loaded directly from this repository via a reproducible pipeline (no manual downloads required to rerun the analysis), with a documented data dictionary and an explicit data-cleaning step resolving a formatting inconsistency between source files.

**Skills:** SQL (SQLite), R, dplyr, ggplot2, logistic regression (glm/broom), data cleaning, reproducible data pipelines, statistical inference

---

## **Tools & Technologies**

- **Python**
- **R**
- **SQL (SQLite)**
- **pandas / dplyr**
- **scikit-learn**
- **Statistical inference**
- **Data visualization (Altair, matplotlib, ggplot2)**
