# Care Optimize
### A Data-Driven DPC Clinic Siting Model

**DSCI 4350 – Data Science Capstone | Spring 2026**

| Team Member | GitHub |
|---|---|
| Cyron Trazona | ctrazona1385 |
| Liam Finn | lfinn582 |
| Aidan Moore | aidanrmoore2 |
| Matthew McGee | mmcgee18 |

---

## Project Overview

Access to primary care in the United States is increasingly stratified by geography and insurance status, leaving millions in **"care deserts"** — communities with few physicians, limited facilities, and high rates of uninsured residents.

**Direct Primary Care (DPC)** clinics offer a promising alternative: low-cost, subscription-based primary care that operates independently of insurance. However, identifying where such clinics can realistically be established and where they can remain financially viable while serving underserved populations, requires systematic, data-driven analysis that currently does not exist at scale.

**Care Optimize** develops a **composite location scoring model** to identify optimal sites for DPC clinic placement across U.S. counties, balancing community need with operational viability.

---

## Objectives

1. A reproducible data pipeline integrating health, geographic, and economic indicators at the census-tract level
2. A machine learning classification model predicting healthcare need for non-HPSA-designated areas
3. A probabilistic health impact simulation quantifying expected outcome improvements from clinic placement
4. A financial viability model estimating revenue potential and operating costs by county
5. A machine learning classification model that determines optimal placements for highest impact based on objectives 2 trough 4
6. An interactive dashboard showing results of all models, filterable to individual model classifications
---

## Repository Navigation

```
notebooks/
├── 1_data_pipeline/          ← Start here: data collection and cleaning
├── 2_eda/                    ← Exploratory data analysis and visualizations
└── 3_models/                 ← ML models, simulations, model cards
    └── archive/              ← Draft/prior versions (kept for reference)

data/
├── raw/                      ← Original source files (do not modify)
├── v1_renamed/               ← Intermediate standardized files
└── v2_cleaned/               ← Final production datasets (outputs land here)

EDA/                          ← Static visualization outputs (PNG)
PDF/                          ← Supporting research papers and citations
wandb/                        ← Weights & Biases experiment tracking logs
```

**Recommended reading order:** follow the numbered notebooks in `notebooks/1_data_pipeline/` → `2_eda/` → `3_models/`.

---

## Pipeline

### Stage 1 — Data Ingestion & Cleaning (`notebooks/1_data_pipeline/`)

| Notebook | Description |
|---|---|
| `census_data_call.ipynb` | Pulls ACS 5-year county-level estimates via Census API |
| `Copy of CDC_tract_bp&chol_imputation.ipynb` | Imputes missing blood pressure and cholesterol prevalence at the census tract level |
| `Data_cleaner.ipynb` | Cleans intermediate files, standardizes GEOIDs |
| `data_merging.ipynb` | Left-joins CDC PLACES, Census, HPSA, SAHIE, and AHRF on county GEOID; handles null imputation; outputs `all_merged.csv` (71,507 tracts, 20 columns) |
| `Employment_and_Lease.ipynb` | Processes GSA lease cost data; models revenue and operating costs per county |

### Stage 2 — Exploratory Data Analysis (`notebooks/2_eda/`)

| Notebook | Description |
|---|---|
| `EDA.ipynb` | Correlation analysis, distributions, missing value assessment |
| `Histograms.ipynb` | Feature distribution plots |
| `EDA_Financial_Data.ipynb` | Financial indicator EDA |
| `HPSA_Need_Category_EDA.ipynb` | Choropleth visualization of the 4-category need classification output |

### Stage 3 — Models (`notebooks/3_models/`)

| Notebook | Description |
|---|---|
| `hpsa_score_imputation.ipynb` | Baseline regression to predict continuous HPSA scores; R² near zero, pivot documented |
| `hpsa_score_classification.ipynb` | **Primary v2 model** — Binary classification of HPSA need (score ≥ 14 threshold). Includes LR, Decision Tree, Random Forest, Gradient Boosting, XGBoost with grid search and Optuna tuning |
| `hpsa_score_classification_v3.ipynb` | **v3 model** — Systematic class imbalance handling: GridSearchCV (LR, RF), Optuna (GB, XGBoost), SMOTE variants for all 4 models |
| `Monte_Carlo_Health_Impact (1).ipynb` | Monte Carlo simulation of health outcome improvements from DPC placement (10,000 simulations per tract) |
| `FinancialModel.ipynb` | Revenue and operational viability model for DPC clinic siting |
| `GO_NOGO_SVM.ipynb` | SVM-based go/no-go siting recommendation model |

Model documentation lives alongside the notebooks:
`ModelCard_HPSAClassification.md` · `ModelCard_HealthImpact.md` · `ModelCard_Financial.md` · `ModelCard_GONOGO.md`

---

## Key Output Files (`data/v2_cleaned/`)

| File | Description |
|---|---|
| `all_merged.csv` | Main dataset — 71,507 census tracts, 20 columns |
| `hpsa_need_category_gb_v2.csv` | v2 classification output — 4-category HPSA need labels |
| `hpsa_need_category_v3.csv` | v3 classification output — best model across 8 runs |
| `monte_carlo_simulation_results.csv` | Per-tract health impact estimates (mean + 90% CI) |
| `Lease_cost_by_county.csv` | GSA lease costs by county |
| `Lease_cost_by_state.csv` | GSA lease costs by state |

---

## Models

### HPSA Need Classification

**Problem:** Predict whether a non-HPSA-designated census tract would qualify as *high need* (HPSA score ≥ 14) if evaluated, enabling the model to generalize shortage-area analysis to all U.S. tracts.

**Target:** `hpsa_high_need` — binary (1 = high need)

**Features:** uninsured rate, no-checkup rate, median household income, poverty rate, PCP per 100k, providers per poverty population, log-transformed total population, log-transformed PCP count

**Train/test split:** `GroupShuffleSplit` by county (80/20) — prevents county-level HPSA score leakage into the test set

**Class imbalance:** training set is 88.5% high-need (ratio ≈ 1:7.7); addressed via `class_weight='balanced'`, `scale_pos_weight`, `sample_weight`, and SMOTE

**W&B project:** `hpsa-classification`

| Run | Model | Tuning | Imbalance Handling |
|---|---|---|---|
| `logistic_regression_binary` | Logistic Regression | Baseline | — |
| `decision_tree_binary` | Decision Tree | Baseline | — |
| `random_forest_binary` | Random Forest (100 est.) | Baseline | — |
| `gradient_boosting_binary` | Gradient Boosting (100 est.) | Baseline | — |
| `logistic_regression_v2_binary` | Logistic Regression | Grid search: C, penalty | — |
| `gradient_boosting_v2_binary` | Gradient Boosting | Grid search: n_est, depth, lr | — |
| `xgboost_baseline` | XGBoost | Baseline | `scale_pos_weight` |
| `xgboost_v2_trials` / `xgboost_v2` | XGBoost | 100-trial Optuna (5-fold CV) | `scale_pos_weight` |
| `gradient_boosting_v2_balanced` | Gradient Boosting | v2 params | Class weights + SMOTE |
| `logistic_regression_v3_gridsearch` | Logistic Regression | GridSearchCV | `class_weight='balanced'` |
| `random_forest_v3_gridsearch` | Random Forest | GridSearchCV (216 combos) | `class_weight='balanced'` |
| `gradient_boosting_v3_optuna` | Gradient Boosting | Optuna 50 trials | `sample_weight` per fold |
| `xgboost_v3_optuna` | XGBoost | Optuna 50 trials | `scale_pos_weight` |
| `*_v3_smote` (×4) | All 4 models | Best params from above | SMOTE via `ImbPipeline` |

**4-category output:**

| Category | Label | Description |
|---|---|---|
| 0 | `hpsa_designated_low_need` | Designated HPSA, score < 14 |
| 1 | `hpsa_designated_high_need` | Designated HPSA, score ≥ 14 |
| 2 | `non_hpsa_low_need` | Non-designated, predicted low need |
| 3 | `non_hpsa_high_need` | Non-designated, predicted high need |

### Monte Carlo Health Impact Simulation

Probabilistic estimation of health outcome improvements from DPC clinic placement. 10,000 simulations per tract; outputs mean reductions and 90% confidence intervals for:

- Diabetes prevalence · High blood pressure · High cholesterol · Asthma prevalence · No-checkup rate

### Financial Viability Model

Revenue model benchmarked against Direct Primary Care Coalition cost benchmarks and HRSA Uniform Data System (UDS) data as a conservative upper bound. 

### Go/No Go Classifier Model

Model enabling go/no go recommendations based on inputs from previous 3 models.

| Tier | Label | Criteria | (TBD)
|---|---|---|
| Strong Go | High need + financially viable | HPSA high-need + positive revenue projection |
| Conditional | Moderate need or viability | Requires further local assessment |
| Not Viable | Low need or unsustainable | Insufficient demand or negative margin |

---

## Data Sources

| Source | Data Used |
|---|---|
| **Census Bureau** (ACS 5-year & SAHIE) | Uninsured rates, income, poverty estimates |
| **HRSA HPSA** | Physician shortage area scores and designations |
| **AHRF 2022** | MD/DO primary care provider counts, total population |
| **CDC PLACES** | Census tract-level health outcomes (diabetes, BP, cholesterol, asthma) |
| **GSA Lease/Rent Data** | Commercial lease costs by county and state |

---

## Experiment Tracking

All model runs are logged to **Weights & Biases** (`hpsa-classification` project). Each run records:
- Accuracy, Weighted F1, ROC-AUC
- Per-class classification report
- ROC curve plots
- Hyperparameter configurations
- Optuna trial tables (v2/v3 XGBoost and GB)

Local run files are stored in `wandb/`.

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
