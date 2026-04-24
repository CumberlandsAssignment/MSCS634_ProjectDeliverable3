MSCS634 Project – Deliverable 3: Classification, Clustering, and Pattern Mining
Author: Reza Shrestha
Course: MSCS634 – Advanced Data Mining
Dataset: Diamonds Dataset (53,940 records, 10 attributes)


Overview
This deliverable builds on the EDA and regression work from Deliverables 1 and 2. Here we apply classification, clustering, and association rule mining to the Diamonds dataset to discover patterns and predictive structure beyond price regression.

Dataset
The Diamonds dataset contains 53,940 records across 10 attributes: carat, cut, color, clarity, depth, table, price, x, y, z. A derived price_tier column (Low / Medium / High) serves as the classification target.

Tasks Completed
1. Classification
Target: price_tier — Low (≤$1,000), Medium ($1,001–$5,000), High (>$5,000)
Model 1 — Decision Tree Classifier

Default: max_depth=8, min_samples_leaf=20
Accuracy: 98.9% | F1 (weighted): 98.9%

Model 2 — k-Nearest Neighbors (k=7)

Euclidean distance, StandardScaler applied
Accuracy: 98.6% | F1 (weighted): 98.5%

Hyperparameter Tuning (Decision Tree via GridSearchCV)

Grid: max_depth ∈ {4,6,8,10,12}, min_samples_leaf ∈ {10,20,50}, criterion ∈ {gini, entropy}
Best params: max_depth=4, min_samples_leaf=10, criterion=gini
Best CV F1: 98.9% | Test F1: 98.9%
Insight: shallower tree (depth=4) generalizes equally well — default depth-8 was unnecessary

Evaluation includes: confusion matrices, ROC curves (one-vs-rest with AUC), accuracy/F1 bar chart.

2. Clustering
K-Means Clustering (k=4)

Features: log_carat, log_volume, cut_enc, color_enc, clarity_enc
k selected via elbow method (WCSS drops steeply to k=4, then plateaus)
PCA used for 2D visualization

3. Association Rule Mining (FP-Growth)
FP-Growth was applied to a one-hot encoded transaction matrix of cut, color, clarity, and price_tier.

min_support = 0.05 | min_confidence = 0.60
Rules ranked by lift (values >1 = non-random association)

Key findings:

High-clarity (VVS1, VVS2, IF) paired with top color grades (D, E) reliably associate with the High price tier
Lower clarity (SI1, SI2) with mid-range color strongly associates with the Low or Medium tier
Cut quality alone is a weaker predictor than clarity+color combinations