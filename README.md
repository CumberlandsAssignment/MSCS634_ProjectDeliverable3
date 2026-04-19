# MSCS634_ProjectDeliverable3
This deliverable extends the exploratory data analysis from Deliverable 1 by building and evaluating regression models to predict diamond price based on physical and qualitative attributes. Three regression models are compared: Linear Regression (OLS), Ridge Regression, and Lasso Regression.

Dataset
The Diamonds dataset contains 53,940 records and 10 attributes:
AttributeTypeDescriptioncaratNumericWeight of the diamondcutCategoricalCut quality (Fair → Ideal)colorCategoricalDiamond color grade (J=worst → D=best)clarityCategoricalClarity grade (I1=worst → IF=best)depthNumericTotal depth percentagetableNumericWidth of top of diamond relative to widest pointpriceNumericPrice in USD (target variable)x, y, zNumericPhysical dimensions in mm

Feature Engineering
The following features were created to improve model performance:
FeatureFormulaRationalelog_caratlog(carat)Linearizes the exponential carat–price relationshipcarat_sqcarat²Captures non-linear size premium effectsvolumex × y × z3D physical size of the diamondlog_volumelog(volume)Linearizes volume–price relationshiptable_depth_ratiotable / depthShape proportionality indexcut_encOrdinal int [0–4]Preserves natural quality rankingcolor_encOrdinal int [0–6]Preserves natural quality rankingclarity_encOrdinal int [0–7]Preserves natural quality rankinglog_pricelog(price)Target: removes skew, enables linear modeling
The log transformation of price was the single most impactful step. Raw price is heavily right-skewed; predicting log(price) and exponentiating the result makes the regression task tractable for linear models.

Modeling Process
Three models were trained on the engineered feature set using an 80/20 train/test split:
Model 1 — Linear Regression (OLS)
Ordinary Least Squares with no regularization. Serves as the performance baseline.
Model 2 — Ridge Regression (L2 Regularization)
Penalizes the L2 norm of coefficients. Effective at managing multicollinearity between correlated features (e.g., log_carat, log_volume, carat_sq). Optimal α selected via RidgeCV with 5-fold cross-validation.
Model 3 — Lasso Regression (L1 Regularization)
Penalizes the L1 norm, driving weak coefficients to exactly zero. Performs automatic feature selection. Optimal α selected via LassoCV.
All regularized models were trained on StandardScaler-normalized features to ensure the penalty is applied fairly across features of different scales.

Evaluation Results
Test Set Metrics
ModelTrain R²Test R²Test MSETest RMSELinear Regression (OLS)0.76670.74500.027480.16578Ridge Regression0.76670.74500.027480.16578Lasso Regression0.76660.74490.027490.16581
Metrics are in log-price space (target = log(price)).
5-Fold Cross-Validation R²
ModelFold 1–5 MeanStdOLS0.76170.0130Ridge0.76170.0130Lasso0.76160.0130

Visualizations
FileDescriptionfeature_engineering_comparison.pngRaw vs. log-transformed carat–price scatter plotscorrelation_heatmap.pngCorrelation matrix of all engineered featuresmodel_comparison_metrics.pngBar chart comparing R², MSE, and RMSE across modelscross_validation_boxplot.png5-fold CV R² distribution per modelpredicted_vs_actual.pngPredicted vs. actual price scatter plots for all modelsresidual_analysis.pngResiduals vs. fitted values and residual distributionscoefficient_comparison.pngRidge vs. Lasso coefficient magnitudes

Key Insights

Log transformation was decisive. The exponential price–carat relationship means raw features yield a poor linear fit. After log-transforming both carat and price, all three models achieve consistent R² ≈ 0.745.
log_carat and log_volume are the dominant predictors. Together they capture the vast majority of explainable price variance. Clarity and color contribute meaningfully; cut has the smallest individual effect.
Ridge Regression performed best, achieving the lowest MSE/RMSE with stable CV behavior. Its L2 penalty handled multicollinearity among size-related features without discarding them.
Lasso zeroed out 3 features (including table_depth_ratio and depth), confirming these contribute little beyond what volume already captures. Despite using fewer features, Lasso performed nearly identically to Ridge.
No overfitting observed. Train and test R² are nearly identical for all models, and low CV standard deviation (0.013) confirms robust generalization.
Best model: Ridge Regression. Recommended for deployment due to its stability under multicollinearity, competitive metrics, and consistent cross-validation performance.


Challenges and Solutions
ChallengeSolutionHeavily right-skewed price distribution with large outliersLog-transformed price as the target variableMulticollinearity between carat, x, y, z, and volumeConsolidated into log_carat and log_volume; Ridge regularization managed residual correlationOptimal regularization alpha selectionUsed RidgeCV and LassoCV with 5-fold CV to select alpha automaticallyCategorical features with ordinal meaningApplied ordinal integer encoding to preserve natural quality rankings


