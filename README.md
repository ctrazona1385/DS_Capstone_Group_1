# DPC Clinic Siting Model
### A Data-Driven Framework for Identifying Optimal Direct Primary Care Locations Across U.S. Counties

---

## Overview

Access to primary care in the United States is increasingly stratified by geography and insurance status, leaving millions in **"care deserts"** — communities with few physicians, limited facilities, and high rates of uninsured residents.

**Direct Primary Care (DPC)** clinics offer a promising alternative: low-cost, subscription-based primary care that operates independently of insurance. However, identifying where such clinics can realistically be established — and where they can remain financially viable while serving underserved populations — requires systematic, data-driven analysis that currently does not exist at scale.

This project develops a **composite location scoring model** to identify optimal sites for DPC clinic placement across U.S. counties, balancing community need with operational viability.

---

## Objectives

The model integrates economic, demographic, and healthcare infrastructure indicators to support evidence-based **go/no-go siting decisions** that maximize care impact while ensuring financial sustainability.

**Three key deliverables:**

1. A reproducible data pipeline integrating health, geographic, and economic indicators at the county level
2. A composite scoring model with documented methodology and sensitivity analysis, enabling go/no-go recommendations for DPC clinic siting
3. An interactive choropleth map dashboard visualizing opportunity scores, underlying indicators, and estimated revenue potential by county

---

## Data Sources

| Source | Data Used |
|---|---|
| **Census Bureau** (ACS 5-year estimates & SAHIE) | Uninsured rates, income levels, poverty estimates |
| **HRSA** (HPSA designations) | Physician shortage area scores and designations |
| **AHRF 2022** | MD and DO primary care provider counts, total population |
| **CDC PLACES** | Health outcomes at the census tract level (diabetes, asthma, blood pressure, etc.) |

**Key indicators used:**
- Uninsured rates
- Physician and provider counts (MDs, DOs, PCPs)
- Poverty rate and poverty population
- Median household income
- Health outcomes (diabetes, high BP, asthma, high cholesterol, preventive care gaps)
- HPSA designation and score

---

## Pipeline

### 1. Data Ingestion & Cleaning
- **`census_data_call.ipynb`** — Pulls ACS 5-year county-level estimates via Census API
- **`Copy of CDC_tract_bp&chol_imputation.ipynb`** — Imputes missing blood pressure and cholesterol prevalence at the census tract level
- **`data/v2_cleaned/Data_cleaner.ipynb`** — Cleans intermediate files, standardizes GEOIDs

### 2. Data Merging
- **`data_merging.ipynb`** — Left-joins CDC PLACES (tract-level), Census, HPSA, SAHIE, and AHRF datasets on county GEOID; handles null imputation (provider counts → 0, missing population → derived from poverty data); outputs `data/v2_cleaned/all_merged.csv` (71,507 tracts, 20 columns)

### 3. HPSA Need Classification
- **`hpsa_score_imputation.ipynb`** *(baseline regression — preserved for reference)* — Regression approach to predict continuous HPSA scores; R² near zero, pivot documented
- **`hpsa_score_classification.ipynb`** *(primary model)* — Binary classification: predicts whether a non-designated county would be **high need** (HPSA score ≥ 14, the priority placement threshold) if it were evaluated. Trains on HPSA-designated counties, imputes for non-designated. Train/test split is done by county (group split) to prevent data leakage

### 4. Health Impact Estimation
- **`Monte_Carlo_Health_Impact (1).ipynb`** — Monte Carlo simulation estimating reductions in diabetes, high BP, high cholesterol, asthma, and preventive care gaps from DPC clinic placement; outputs mean and 5th/95th percentile estimates per tract

### 5. EDA & Visualization
- **`EDA/EDA.ipynb`** — Correlation analysis, distributions, missing value assessment
- **`EDA/Histograms.ipynb`** — Feature distribution plots
- **`HPSA_Need_Category_EDA.ipynb`** — Choropleth visualization of the 4-category need classification output

### 6. Operational Viability
- **`data/v2_cleaned/Employment_and_Lease.ipynb`** — Processes GSA lease cost data by county and state; models revenue and operational costs for DPC clinic viability

---

## Models

### HPSA Need Classification (`hpsa_score_classification.ipynb`)

**Target:** `hpsa_high_need` — binary flag (1 = high need, score ≥ 14)

**Features:** uninsured rate, no-checkup rate, median household income, poverty rate, PCP per 100k, providers per poverty population, log-transformed population and PCP counts

**Train/test split:** by county (`GroupShuffleSplit`, 80/20) to prevent leakage of county-level HPSA scores into the test set

**Models trained and tracked in W&B (`hpsa-classification` project):**

| Run Name | Model | Notes |
|---|---|---|
| `logistic_regression_binary` | Logistic Regression | Baseline linear model |
| `decision_tree_binary` | Decision Tree | Interpretable baseline |
| `random_forest_binary` | Random Forest | 100 estimators |
| `gradient_boosting_binary` | Gradient Boosting | 100 estimators |
| `logistic_regression_v2_binary` | Logistic Regression | Tuned: C, penalty (l1/l2) via grid search |
| `gradient_boosting_v2_binary` | Gradient Boosting | Tuned: n_estimators, max_depth, learning_rate via grid search |
| `xgboost_baseline` | XGBoost | Default hyperparameters |
| `xgboost_v2_trials` / `xgboost_v2` | XGBoost | 100-trial Optuna search (5-fold CV); best params retrained |
| `gradient_boosting_v2_balanced` | Gradient Boosting | Class imbalance addressed via class weights + SMOTE |

**Evaluation metrics:** Accuracy, Weighted F1, ROC-AUC, per-class classification report

**Final output:** `data/v2_cleaned/hpsa_need_category_gb_v2.csv` — 71,507 tracts classified into 4 categories:

| Category | Label |
|---|---|
| 0 | `hpsa_designated_low_need` |
| 1 | `hpsa_designated_high_need` |
| 2 | `non_hpsa_low_need` |
| 3 | `non_hpsa_high_need` |

### Monte Carlo Health Impact Simulation

Probabilistic estimation of health outcome improvements from DPC clinic placement. Outputs per-tract mean reductions and 90% confidence intervals for:
- Diabetes prevalence
- High blood pressure prevalence
- High cholesterol prevalence
- Asthma prevalence
- No-checkup rate

Results saved to `data/v2_cleaned/monte_carlo_simulation_results.csv`.

---

## Methodology

### Siting Tiers

Locations are classified into three actionable categories based on both need and estimated revenue viability:

| Tier | Label | Description |
|---|---|---|
| 🟢 | **Strong Go** | High need, financially viable |
| 🟡 | **Conditional** | Moderate need or viability; requires further assessment |
| 🔴 | **Not Viable** | Insufficient need or financial sustainability |

### Operational Viability & Revenue Model

Operational viability is assessed using a revenue model benchmarked against:

- **Direct Primary Care Coalition** — published DPC cost benchmarks providing direct operational reference
- **HRSA Uniform Data System (UDS)** — standardized annual cost and utilization reporting from all federally funded health centers, used as a *conservative upper bound* on expected costs
- **Peer-reviewed literature** on DPC economics

> Because DPC clinics typically operate leaner than Federally Qualified Health Centers, UDS data serves as a conservative upper bound on expected costs, with DPC Coalition benchmarks providing a more direct operational reference.

---

## Repository Structure

```
├── data/
│   ├── raw/                        # Source data from Census, HRSA, AHRF, CDC
│   ├── v1_renamed/                 # Intermediate files with standardized column names
│   └── v2_cleaned/                 # Final production datasets
│       ├── all_merged.csv              # Main dataset (71,507 tracts, 20 columns)
│       ├── hpsa_need_category_gb_v2.csv # Final 4-category HPSA classification
│       ├── monte_carlo_simulation_results.csv  # Health impact estimates
│       ├── Lease_cost_by_county.csv    # GSA lease costs by county
│       └── Lease_cost_by_state.csv     # GSA lease costs by state
│
├── EDA/                            # Exploratory analysis and visualizations
├── PDF/                            # Supporting research papers and citations
├── wandb/                          # W&B experiment tracking logs
│
├── census_data_call.ipynb          # Census API data pull
├── data_merging.ipynb              # Dataset merging and null imputation
├── hpsa_score_imputation.ipynb     # Baseline regression (preserved for reference)
├── hpsa_score_classification.ipynb # Primary HPSA classification model
├── HPSA_Need_Category_EDA.ipynb    # EDA of classification output
├── Monte_Carlo_Health_Impact (1).ipynb  # Health impact simulation
├── ModelCard_HPSAClassification.md # Model documentation
├── ModelCard_HealthImpact.md       # Simulation documentation
└── README.md
```

---

## Experiment Tracking

All model runs are tracked with **Weights & Biases** under the project `hpsa-classification`. Logged per run:
- Accuracy, Weighted F1, ROC-AUC
- ROC curve plots
- Hyperparameter configs
- Optuna trial tables (for XGBoost v2)

---

## Impact & Applications

This project provides a replicable framework that **community health advocates, policymakers, and DPC operators** can apply to direct investment toward communities where subscription-based primary care can both serve the underserved and sustain itself financially.

---

## References

- U.S. Census Bureau — American Community Survey (ACS) 5-Year Estimates
- U.S. Census Bureau — Small Area Health Insurance Estimates (SAHIE)
- U.S. Census Bureau — Small Area Income and Poverty Estimates (SAIPE)
- HRSA — Health Professional Shortage Area (HPSA) Data
- Area Health Resources Files (AHRF) 2022 — Provider counts and population data
- CDC PLACES — Census Tract-Level Health Outcomes
- Direct Primary Care Coalition — DPC Economic Benchmarks
- HRSA Uniform Data System (UDS) — Federally Qualified Health Center Reporting
