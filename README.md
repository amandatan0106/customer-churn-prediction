# Customer Churn Prediction — S-Mobile Case Study
### UC San Diego | March 2026

> Fully reproducible with synthetic data — all code runs end to end without the original dataset.

---

## Overview

S-Mobile has a ~2% monthly churn rate, but identifying which customers are at risk *before* they leave is critical to protecting revenue. This project builds a complete churn prediction and retention framework: from feature engineering through model evaluation, counterfactual analysis, RCT intervention design, and CLV-based ROI quantification.

---

## What's In This Project

**Feature Engineering**
- Log transformation of skewed continuous features for logistic regression stability
- Binary encoding of yes/no variables
- Case weighting to correct for 50% training vs. 2% real-world churn rate
- Exclusion of `retcalls` (data leakage — consequence of churn intent, not a cause)
- Iterative backwards selection to remove insignificant logistic predictors

**Two Models**

| Model | Strengths |
|---|---|
| Logistic Regression | Interpretable odds ratios; statistical significance testing; directional clarity |
| XGBoost | Captures non-linear relationships and feature interactions logistic regression misses |

Key XGBoost insight: `overage` has a U-shaped churn curve — churn peaks at zero overage, dips at low values, rises mid-range, then falls sharply above 150 units. Logistic regression assumes this away entirely.

**Counterfactual Analysis**

For each proposed intervention, the XGBoost model is used to simulate the counterfactual: modify the feature value for the target segment and measure predicted churn change. This surfaces a critical finding — device upgrade *increased* predicted churn because resetting `eqpdays` to 30 made customers look like high-risk new subscribers. Without counterfactual testing, this would have been a costly real-world mistake.

**Five Retention Interventions**

| Intervention | Method | Key Finding |
|---|---|---|
| Stabilize declining revenue trend | Direct Prediction | `changer < 0` is the clearest pre-churn signal |
| Upsell low-revenue customers | Direct Prediction | Higher revenue → lower churn; move customers up-tier |
| High credit loyalty program | RCT | OR = 2.06; competitors actively poach these customers |
| Plan upgrade for overage customers | RCT | Billing frustration risk; causal effect needs experiment |
| Network quality alert | RCT | `dropvce` confounded by high usage; RCT needed |

**CLV Framework**

Each intervention is evaluated using a 60-month discounted CLV model:
$$CLV = \sum_{t=0}^{60} \frac{\text{Margin} \times (1 - \text{Churn})^t}{(1 + r)^t}$$

Net benefit = CLV gain per customer × customers targeted − cost per customer × customers targeted

---

## Key Results

- **Plan Right-Size** (overage = 0 → 20): largest counterfactual impact — 12.8pp churn reduction across 13,736 customers
- **Early Loyalty Reward** (months < 20): 10.4pp reduction across 18,254 customers
- **Device Upgrade**: *skipped* — counterfactual showed churn *increased* by 1.7pp
- All five interventions produced positive net CLV benefit on the real dataset

---

## Repository Structure

```
customer-churn-prediction/
│
├── customer_churn_smobile.ipynb   # Main notebook
├── images/
│   ├── distr_plots.png            # EDA distribution plots
│   ├── pip_logit.png              # Logistic permutation importance
│   ├── pdp_logit.png              # Logistic partial dependence
│   ├── pip_xgb.png                # XGBoost permutation importance
│   ├── pdp_xgb.png                # XGBoost partial dependence (top 5)
│   └── xgb_cv.png                 # Cross-validation tuning plot
└── README.md
```

> **Note:** Raw data is not included (proprietary case dataset). EDA and model visualizations are embedded from the original analysis run on the real data. The notebook is fully reproducible with synthetic data calibrated to match key findings from the original.
--- 

## A Note on Data & Results

The notebook runs end to end on synthetic data generated to match the structure and key statistical properties of the original S-Mobile dataset. Churn probabilities are calibrated to reproduce directional findings from the real analysis — `highcreditr` carries ~2x churn odds, declining `changer`/`changem` strongly predicts churn, `refurb` customers churn less — but absolute model metrics (AUC, churn rates, CLV figures) will differ from the original due to the inherent signal limitations of synthetic data.

EDA distribution plots, permutation importance charts, and partial dependence plots are embedded from the original analysis run on the real dataset, as these visualizations cannot be reproduced without the proprietary data.

All methodology — feature engineering decisions, model selection, counterfactual framework, RCT designs, and CLV calculations — is identical to the original analysis.

---

## Tools & Libraries

`Python` · `Pandas` · `NumPy` · `Scikit-learn` · `XGBoost` · `Matplotlib`

---

## Author

**Amanda Tan**  
M.S. Business Analytics — UC San Diego, Rady School of Management  
[LinkedIn](http://www.linkedin.com/in/aamandatan)
