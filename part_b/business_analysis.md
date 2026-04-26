# Part B: Business Case Analysis

## B1. Problem Formulation

### (a) ML Problem Formulation

# Target Variable:
Number of items sold (sales volume) per store per month

# Candidate Input Features:
- Store attributes: location type (urban/semi-urban/rural), store size, competition density  
- Historical performance: past sales, past promotion performance  
- Promotion type: Flat Discount, BOGO, Free Gift, Category Offer, Loyalty Points  
- Temporal features: month, seasonality, festival flags, weekends  
- Customer demographics (if available)  
- Monthly footfall  

# Problem Type: 
Supervised learning — regression problem

# Justification:
We are predicting a continuous numerical outcome (items sold). The goal is to estimate expected sales volume under different promotions and choose the one that maximizes it.



### (b) Why Items Sold > Revenue

Revenue is influenced by pricing, discount levels, and product mix, making it inconsistent for evaluating promotion effectiveness.

# Items sold (sales volume):
- Direct measure of customer demand  
- Comparable across promotions  
- Not distorted by pricing changes  

# Broader Principle: 
Choose a target variable that directly reflects the business objective and minimizes external distortions.


### (c) Alternative Modelling Strategy

# Recommended Approach:
- Cluster stores (e.g., urban, semi-urban, rural) and build separate models, OR  
- Use hierarchical (multi-level) models with store-level effects  

# Justification:
Stores behave differently due to demographics and competition. Segmented or hierarchical models capture this variation while leveraging shared patterns.



## B2. Data and EDA Strategy

### (a) Data Joining and Dataset Design

# Data Sources:
- Transactions  
- Store attributes  
- Promotion details  
- Calendar  

# Join Logic:
- Join transactions with store attributes using `store_id`  
- Join promotion details using `promotion_id` or date-store mapping  
- Join calendar using `date`  

# Final Dataset Grain:
One row = one store × one month

# Aggregations:
- Total items sold (target)  
- Total revenue  
- Number of transactions  
- Average basket size  
- Promotion type  
- Monthly footfall  
- Festival/weekend indicators  

## Data Quality & Leakage Checks:
- Handle missing joins (e.g., missing promotion/store data)
- Ensure no future data leakage(only use past data)
- Validate consistency across tables (IDs, dates)


### (b) EDA Strategy

# 1. Promotion vs Sales (Bar Plot)**  
Compare average items sold across promotion types to identify strong performers.

# 2. Time Series Trends (Line Chart)**  
Analyze monthly sales trends to detect seasonality and trends.

# 3. Store Segmentation (Clustering / Scatter Plot)**  
Group similar stores to inform segmented modelling.

# 4. Correlation Heatmap:
Identify relationships between features and the target variable.

# 5. Promotion × Store Type Analysis: 
Examine interaction effects to guide feature engineering.

# 6. Outlier Detection(Box Plot/ IQR):
Identify unusually high/low sales months
-> Decide on capping or investigation

# 7. Distribution of Target(Histogram):
Check skewness of sales
-> Apply transformation if needed


### (c) Handling Promotion Imbalance

# Problem:
80% of transactions have no promotion → model bias

# Impact:
- Model may favor "no promotion"  
- Poor learning of promotion effects  

# Solutions:
- Oversample promotion cases or undersample non-promotion cases  
- Apply sample weights  
- Use balanced evaluation strategies  
- Consider uplift modelling  


## B3. Model Evaluation and Deployment

### (a) Train-Test Split and Metrics

# Split Strategy:
- Train: first ~2.5 years  
- Test: last ~6 months (time-based split)  

# Why not random split?
- Causes data leakage  
- Breaks temporal dependencies  

# Evaluation Metrics:
- **RMSE:** penalizes large errors  
- **MAE:** interpretable average error  
- **MAPE:** compares performance across stores  


### (b) Explaining Recommendations

Use feature importance and interpretability tools (e.g., SHAP values).

# Example:
- December ->Loyalty Points Bonus (due to festive demand) 
  - High importance: festival flag, seasonal demand 
- March -> Flat Discount (to boost lower demand)  
  - High importance: Low baseline demand, price sensitivity

# Communication Approach:
- Use visual explanations  
- Translate technical outputs into business insights  


### (c) Deployment Process

# Pipeline Overview:
# Steps:
1. Save model (pickle/joblib)  
2. Prepare new monthly data  
3. Generate predictions for all promotion types  
4. Select best promotion per store  
5. Output recommendations  

# Monitoring:
- Track prediction vs actual performance  
- Monitor feature drift  
- Track error metrics over time  

# Retraining Triggers:
- Performance degradation  
- Changes in customer behavior  
- Introduction of new promotions  

# Production Consideration:
- Automate pipeline with scheduled monthly jobs
- Maintain model and data versioning
- Log predictions and inputs for auditability
