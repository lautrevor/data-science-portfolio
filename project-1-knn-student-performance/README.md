# KNN Regression: Predicting Student Final Grades

## Problem
This project applies K-Nearest Neighbors (KNN) regression to predict a student’s final grade (G3) based on study behavior, attendance, and prior academic performance. The goal is to evaluate whether a simple, non-parametric model can meaningfully capture patterns in student outcomes using a small set of academic features.

## Data
Source: UCI Student Performance dataset (via ucimlrepo, dataset id 320)
The dataset contains student academic records, including:
- studytime (weekly study time)
- absences (number of school absences)
- G1, G2 (grades from earlier terms)

The target variable is G3, the final course grade, measured on a 0–20 scale. After selecting relevant features and removing missing values, the data was split into training and testing sets.

## Methods
- Train/test split (80/20)
- Feature scaling using StandardScaler
- K-Nearest Neighbors regression
- Hyperparameter tuning for the number of neighbors (k) using GridSearchCV
- Model evaluation using Root Mean Squared Error (RMSE)

A preprocessing and modeling pipeline was used to prevent data leakage and ensure consistent scaling during cross-validation.

## Results
The final KNN regression model achieved an RMSE of approximately **1.38** on the test set. Given that final grades range from 0 to 20, this indicates that predictions are, on average, within **1–2 grade points** of the true values.

Two visualizations support this result:

1. **Predicted vs. Actual Grades**  
   This scatter plot shows a strong positive relationship between predicted and actual final grades, with most points clustering near the diagonal line representing perfect prediction. This suggests that the model captures key patterns in student performance, particularly for mid-to-high grade ranges.

2. **Distribution of Prediction Errors**  
   The error distribution is centered near zero, indicating no strong systematic over- or under-prediction. Most prediction errors fall within a narrow range, consistent with the observed RMSE, while a small number of larger errors reflect natural variability in educational outcomes.

## Limitations
- The model relies on a limited number of features and does not account for unobserved factors such as motivation, course difficulty, or external circumstances.
- KNN regression tends to smooth predictions, which can reduce accuracy for extreme low or high grades.
- The dataset size is relatively small, which may limit generalizability.

## Next Steps
- Compare KNN performance with linear regression or tree-based models.
- Explore additional features or interaction effects.
- Extend the project to a classification task (e.g., predicting pass/fail outcomes).
- Evaluate performance using additional metrics and repeated cross-validation.
