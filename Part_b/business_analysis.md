## B1. Problem Formulation
### (a) Machine Learning Formulation
The objective is to predict the number of items sold for each store under different promotion types.

Target Variable:
- items_sold (sales volume)

Input Features:
- store_id
- store_size
- location_type (urban/semi-urban/rural)
- promotion_type
- footfall
- competition_density
- customer demographics
- month, seasonality indicators (festival, weekend)

Problem Type:
- Supervised Machine Learning — Regression problem

Justification:
The target variable (items_sold) is continuous numerical data, making this a regression problem. The model learns from historical labeled data (features + items sold) to predict future outcomes.


### (b) Why items_sold instead of revenue.

Using total sales revenue can be misleading because it is influenced by price changes, discounts, and product mix.

For example, a promotion offering heavy discounts may increase items sold but reduce revenue per item.

Items sold (sales volume) is a more reliable metric because it directly reflects customer demand and promotion effectiveness.

This illustrates a broader principle in machine learning: the target variable should align.


### (c) Better Modelling Strategy.

Instead of using a single global model, a better approach is to use segmented or hierarchical models.

For example:
- Build separate models for urban, semi-urban, and rural stores
OR
- Use a model with interaction features between location_type and promotion_type

Justification:
Different stores respond differently to promotions due to varying customer behavior, competi

------------------------------------------------------------------------------------------
#B2. Data and EDA Strategy
------------------------------------------------------------------------------------------
### (a) Data Joining and Grain
The four tables (transactions, store attributes, promotion details, and calendar) would be joined using common keys such as store_id and date.

Steps:
- Join transactions with store attributes on store_id
- Join with promotion details on promotion_id or promotion_type
- Join with calendar on transaction_date

Final Dataset Grain:
- One row per store per month

Aggregations:
- Total items_sold per store per month
- Average basket size
- Total footfall
- Promotion type applied in that month
- Flags for weekend/festival

This ensures the dataset is aligned with the business decision level (monthly store-level promo

### (b) EDA Strategy

1. Promotion vs Items Sold (Bar Chart)
   - Compare average items sold across promotion types
   - Helps identify which promotions perform best

2. Time Series Plot (Monthly Sales Trend)
   - Observe seasonality and trends
   - Helps create time-based features

3. Correlation Heatmap
   - Identify relationships between numerical variables
   - Helps detect important predictors

4. Boxplots (Location Type vs Sales)
   - Compare performance across urban, semi-urban, rural stores
   - Helps justify segmentation strategy

These analyses guide feature engineering, such as creating seasonal features or interaction terms.


### (c) Handling Imbalance (80% no promotion)

This imbalance can bias the model towards predicting outcomes for non-promotion cases, reducing its ability to learn the true impact of promotions.

To address this:
- Use stratified sampling or balanced datasets
- Apply weighting to promotion cases
- Analyze promotion impact separately

This ensures the model learns meaningful patterns for promotional scenarios.

---------------------------------------------------------------------------------------------
#B3. Model Evaluation and Deployment 
---------------------------------------------------------------------------------------------

### (a) Train-Test Split and Metrics
The data should be split using a time-based approach.

Example:
- Train: First 2.5 years
- Test: Last 6 months

Random split is inappropriate because it mixes past and future data, leading to data leakage and unrealistic performance.

Evaluation Metrics:
- RMSE: Measures average prediction error (penalizes large errors)
- MAE: Measures average absolute error (easy to interpret)
- R²: Measures how well the model explains variance

Interpretation:
Lower RMSE and MAE indicate better prediction accuracy for items sold.

### (b) Explaining Model Decisions (Feature Importance)

Feature importance helps identify which variables influence predictions.

For example:
- In December, features like festival season and high footfall may make Loyalty Points more effective
- In March, lower demand or different customer behavior may favor Flat Discounts

By analyzing feature importance, we can explain that the model adapts to seasonal patterns and store conditions rather than making arbitrary decisions.

This can be communicated to the marketing team using charts showing top influencing features for each prediction.

### (c) Deployment Strategy

1. Save Model:
- Use joblib or pickle to save the trained model pipeline

2. Monthly Prediction:
- Collect new data for all stores
- Apply same preprocessing pipeline
- Generate predictions for each promotion type
- Select the promotion with highest predicted items_sold

3. Monitoring:
- Track prediction vs actual sales
- Monitor metrics like RMSE over time
- Detect performance degradation (data drift or concept drift)

4. Retraining:
- Retrain model periodically (e.g., every 3–6 months)
- Update with latest data

This ensures the model remains accurate and relevant over time.