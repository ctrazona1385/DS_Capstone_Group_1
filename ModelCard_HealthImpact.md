# Capstone - Health Impact Monte Carlo Model Card
## Collaborators
* Aidan
## Agency
* Georgia State University
## Ownership
* internally-built
## Anticipated Use
### Division(s) using the model
* Modeling/Evaluation Team
### Intended application(s)
* Probablistic Simulation 
### Intended stakeholder(s)
* Capstone Team, Professor Berkay Aydin
## Model Information
### Current model version
* Monte_Carlo_Health_Impact.ipynb
### Version release date
* 2026-04-09
### Changes made since last release (if any)
* None, baseline release
## Ownership
* Owned by Capstone Team for public, free use.
## Model Architecture
### Type of Model (Classification, Regression, Object Detection, etc.)
* Stochastic/Probabilistic Simulation
### Type of algorithm(s) used
* Monte Carlo Simulation
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
* avg_asthma_reduction, avg_diabetes_reduction, avg_high_bp_reduction, avg_high_cholesterol_reduction, avg_no_checkup_reduction
### Factors that limit the model's performance (examples: limited dataset, number of nulls/NAs) (if any)?
* The model's accuracy is primarily limited by the quality and age of the input data (e.g., outdated cholesterol or asthma rates) and the computational intensity required to run a high number of iterations for statistical significance.
### Inclusion of information related to individuals or human populations in the training/testing/validation datasets - Yes/No,
if "yes" include a description of demographic features
* The training dataset (all_merged.csv) includes demographic features such as race/ethnicity, age groups, and geographic location (state/county) to assess health disparities across populations.
### Methods used to minimize bias from human judgement
* The model utilizes probabilistic quantitative bias analysis to adjust estimates for potential systematic errors and employs randomized sampling to ensure that human judgment in parameter selection does not skew the simulation results.
### Potential biases found in the training dataset from collection methods, sample size, representation, etc.
* Potential selection bias may exist due to the "hand-collected" nature of the data from varying sources, which may over-represent certain regions or demographics with better reporting systems.
### Testing/evaluation performed to look for bias in the workflow of the model
* The team performed sensitivity analysis by varying input distributions to observe how different demographic weights impact the model's health reduction metrics.
### Degree of model explainability/transparency
* High. The simulation logic is documented within the Monte_Carlo_Health_Impact.ipynb notebook, and outcomes are visualized via histograms and 95% simulation intervals to show the range of probable outcomes.
### Model/dataset compliance with existing laws and regulations (including privacy protection regulations)
*  The model complies with GSU Institutional Review Board (IRB) standards for secondary data analysis and adheres to general data privacy guidelines by using aggregated, non-personally identifiable information (non-PII).
This model card was generated with the AI Toolkit Model Card Generator version 0.2. To learn more, visit https://ai-toolkit.xd.gov/resources/model-card-generator/tool/.