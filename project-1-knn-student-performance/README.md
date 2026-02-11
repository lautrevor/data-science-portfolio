# KNN Regression: Predicting Student Final Grades

## Problem
This project applies K-Nearest Neighbors (KNN) regression to predict a student’s final grade (G3) based on study behavior, attendance, and prior academic performance. The objective is to assess whether a simple, non-parametric model can effectively capture patterns in academic outcomes using a small set of interpretable features.

## Data
Source: UCI Student Performance dataset (via `ucimlrepo`, dataset ID 320)

The dataset contains academic records for secondary school students, including:
- `studytime` (weekly study time)
- `absences` (number of school absences)
- `G1`, `G2` (grades from earlier terms)

The target variable is `G3`, the final course grade, measured on a **0–20 scale**. After selecting relevant features and removing missing values, the data was split into training and testing sets.

## Methods
- Train/test split (80/20)
- Feature scaling using `StandardScaler`
- K-Nearest Neighbors regression
- Hyperparameter tuning for the number of neighbors (`k`) using `GridSearchCV`
- Model evaluation using Root Mean Squared Error (RMSE)

A preprocessing and modeling pipeline was used to prevent data leakage and ensure consistent feature scaling during cross-validation and evaluation.

## Results
The final KNN regression model achieved an RMSE of approximately **1.38** on the test set. Given that final grades range from 0 to 20, this indicates that predictions are typically within **1–2 grade points** of the true values.

Two visualizations support this result:

1. **Predicted vs. Actual Grades**  
   The scatter plot shows a strong positive relationship between predicted and actual grades, with most points clustering near the diagonal line representing perfect prediction. This suggests that the model captures key performance patterns, particularly for mid-to-high grade ranges.

2. **Distribution of Prediction Errors**  
   Prediction errors are centered near zero, indicating no strong systematic bias. Most errors fall within a narrow range, consistent with the observed RMSE, while a small number of larger errors reflect natural variability in educational outcomes.

## Limitations
- The inclusion of prior grades (`G1`, `G2`) significantly improves accuracy but limits the model’s usefulness for early-term intervention.
- The model relies on a limited feature set and does not account for unobserved factors such as motivation, teaching quality, or external circumstances.
- KNN regression smooths predictions, which can reduce accuracy for extreme low or high grades.
- The dataset is relatively small, which may limit generalizability.

## Next Steps
- Compare KNN performance with linear regression and tree-based models.
- Evaluate model performance without prior grades to assess early predictive power.
- Explore additional features or interaction effects.
- Extend the project to a classification task (e.g., predicting pass/fail outcomes).
