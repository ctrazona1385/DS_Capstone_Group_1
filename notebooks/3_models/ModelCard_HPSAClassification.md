# Capstone - HPSA Classification Model Card
## Collaborators
* Cyron
## Agency
* Georgia State University
## Ownership
* internally-built
## Anticipated Use
### Division(s) using the model
* Modeling/Evaluation Team
### Intended application(s)
* Classification of Health Professional Shortage Areas (HPSAs) 
### Intended stakeholder(s)
* Capstone Team, Professor Berkay Aydin
## Model Information
### Current model version
* hpsa_score_classification.ipynb
### Version release date
* 2026-04-09
### Changes made since last release (if any)
* None, baseline release
## Ownership
* Owned by Capstone Team for public, free use.
## Model Architecture
### Type of Model (Classification, Regression, Object Detection, etc.)
* Classification
### Type of algorithm(s) used
* Logistic Regression, Decision Tree, Random Forest, Gradient Boosting
### Source(s) of the training data
* all_merged.csv
### Data collection/generation method
* Hand collected from varying sources
### Number of variables in the dataset
* 21
### Number of entries in the dataset
* 71507
### Percent of data chosen as the training, testing, and validation sets
* 80/20
### Metrics used to rate model performance
* Accuracy, Weighted F1-Score, ROC-AUC
### Factors that limit the model's performance (examples: limited dataset, number of nulls/NAs) (if any)?
* Limited dataset size for specific minority classes, potential "nulls/NAs" in rural reporting, and the static nature of census-based data (ACS 5-year estimates) which may not reflect real-time provider shifts.
### Inclusion of information related to individuals or human populations in the training/testing/validation datasets - Yes/No,
if "yes" include a description of demographic features
* Includes features such as population-to-provider ratios, percentage of the population below the Federal Poverty Level (FPL), and travel time to care.
### Methods used to minimize bias from human judgement
* The use of multiple algorithms (Logistic Regression to Gradient Boosting) allows for cross-validation of feature importance to ensure no single demographic variable disproportionately drives the classification without statistical merit.
### Potential biases found in the training dataset from collection methods, sample size, representation, etc.
* Data may under-represent transient or homeless populations if sourced primarily from standard census tracts. Additionally, regions with fewer resources may have less complete data, leading to a "reporting bias".
### Testing/evaluation performed to look for bias in the workflow of the model
* Model performance is evaluated using Weighted F1-Score to ensure that smaller, underserved classes (which are often the most critical in HPSA designations) are not overshadowed by majority-class performance.
### Degree of model explainability/transparency
* High. Decision Trees and Random Forests provide feature importance rankings, allowing stakeholders to see which health and economic indicators (like poverty levels or provider ratios) most influence the HPSA score.
### Model/dataset compliance with existing laws and regulations (including privacy protection regulations)
* Complies with GSU project guidelines and uses publicly available federal data (HRSA/Census). No PII (Personally Identifiable Information) is included in the merged dataset.
This model card was generated with the AI Toolkit Model Card Generator version 0.2. To learn more, visit https://ai-toolkit.xd.gov/resources/model-card-generator/tool/.