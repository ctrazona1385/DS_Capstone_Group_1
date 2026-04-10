# Capstone - GO/NOGO Final Model Card
## Collaborators
* Matthew
## Agency
* Georgia State University
## Ownership
* internally-built
## Anticipated Use
### Division(s) using the model
* Modeling/Evaluation Team
### Intended application(s)
* Final predictor to identify optimal locations for health clinic investment based on HPSA need and simulation outcomes. 
### Intended stakeholder(s)
* Capstone Team, Professor Berkay Aydin
## Model Information
### Current model version
* GO_NOGO_SVM.ipynb
### Version release date
* 2026-04-09
### Changes made since last release (if any)
* None, baseline release
## Ownership
* none
## Model Architecture
### Type of Model (Classification, Regression, Object Detection, etc.)
* Classification
### Type of algorithm(s) used
* Support Vector Machine (SVM)
### Source(s) of the training data
* A merging of monte_carlo_simulation_results.csv and hpsa_need_category_gb_v2.csv on the geoid
### Data collection/generation method
* Hybrid: Hand-collected socioeconomic data merged with Monte Carlo simulation outputs.
### Number of variables in the dataset
* 5 (Total features after removing the 3 leakage-prone variables).
### Number of entries in the dataset
* 18,384,324 (Reflected from recent baseline report)
### Percent of data chosen as the training, testing, and validation sets
* 80/20 (Training/Test), with 5-Fold Stratified Cross-Validation.
### Metrics used to rate model performance
* Accuracy, Precision, Recall, F-1 Score
### Factors that limit the model's performance (examples: limited dataset, number of nulls/NAs) (if any)?
* Linearity Constraint: By using LinearSVC for speed, the model cannot capture high-dimensional non-linear relationships unless kernel approximation (e.g., Nystroem) is added.
* Scale Complexity: The massive row count (18M+) limits the ability to perform exhaustive Grid Search across all hyperparameters without significant downsampling.
### Inclusion of information related to individuals or human populations in the training/testing/validation datasets - Yes/No,
if "yes" include a description of demographic features
* The model utilizes HPSA (Health Professional Shortage Area) designations which are intrinsically tied to population demographics and geographic health disparities.
### Methods used to minimize bias from human judgement
* Used StratifiedKFold to ensure class balances are maintained across folds during evaluation.
### Potential biases found in the training dataset from collection methods, sample size, representation, etc.
* TBD
### Testing/evaluation performed to look for bias in the workflow of the model
* TBD
### Degree of model explainability/transparency
* High; utilized LinearSVC coefficients to rank feature importance and PCA for 2D decision boundary visualization.
### Model/dataset compliance with existing laws and regulations (including privacy protection regulations)
* Complies with GSU project guidelines and uses publicly available federal data (HRSA/Census). No PII (Personally Identifiable Information) is included in the merged dataset.
This model card was generated with the AI Toolkit Model Card Generator version 0.2. To learn more, visit https://ai-toolkit.xd.gov/resources/model-card-generator/tool/.