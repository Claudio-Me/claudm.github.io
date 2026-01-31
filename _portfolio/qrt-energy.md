---
title: "Electricity Price Prediction: France vs Germany"
excerpt: "QRT ENS Data Challenge 2023: Predicting daily electricity futures price variations using XGBoost and TabPFN."
collection: portfolio
---

## QRT ENS Data Challenge 2023

**Objective:** Model daily price variations of electricity futures contracts using weather, energy production, and commercial data for France and Germany.

**Evaluation Metric:** Spearman correlation between predictions and actual price changes.

**Approach:**
1. **Exploratory Data Analysis** -- Understand data patterns and relationships
2. **Feature Engineering** -- Create domain-informed features
3. **Modeling** -- Compare XGBoost and TabPFN performance

---

## Exploratory Data Analysis

### 1. Missing Data Analysis

Understanding missing values helps inform preprocessing decisions. XGBoost handles missing values natively, but visualizing them reveals data collection patterns.

![Missing data analysis]({{ site.baseurl }}/images/qrt_energy/plot_1.png)

### 2. Target Variable Distribution

The target represents daily price variation of electricity futures. Understanding its distribution helps identify potential outliers and differences between countries.

![Target distribution]({{ site.baseurl }}/images/qrt_energy/plot_2.png)

### 3. Feature Correlations

Understanding feature correlations helps identify redundant variables and reveals which features are most predictive of the target.

![Feature correlations]({{ site.baseurl }}/images/qrt_energy/plot_3.png)

### 4. Country Comparison

Comparing key features between Germany and France reveals structural differences in the energy markets.

![Country comparison]({{ site.baseurl }}/images/qrt_energy/plot_4.png)

### 5. Key Features vs Target

Visualizing relationships between the most predictive features and the target variable, colored by country.

![Key features vs target]({{ site.baseurl }}/images/qrt_energy/plot_5.png)

---

## Data Preprocessing

The preprocessing pipeline applies several domain-informed transformations:

1. **Market decoupling indicator** -- Identifies days with extreme energy exchange between DE/FR (95th percentile)
2. **Energy aggregation** -- Sums all energy sources per country and calculates energy surplus (production - consumption)
3. **Holiday detection** -- Flags bottom 33% consumption days as potential holidays
4. **Fossil fuel grouping** -- Aggregates fossil fuel sources to simplify the feature space
5. **Categorical encoding** -- Converts country to categorical type for XGBoost

---

## Model Training

### XGBoost

10-fold cross-validation with grid search to find optimal hyperparameters. Strong L1/L2 regularization penalties are used to combat overfitting.

### TabPFN

[TabPFN](https://github.com/automl/TabPFN) is a transformer-based model pre-trained on synthetic tabular data. It offers strong out-of-the-box performance without hyperparameter tuning.

---

## Results

| Model | CV Spearman | Holdout Spearman | Notes |
|-------|-------------|------------------|-------|
| **XGBoost** | 0.23 | ~0.15-0.18 | 10-fold CV with grid search |
| **TabPFN** | -- | ~0.20-0.27 | No hyperparameter tuning required |

### Key Findings

1. **Commodity prices are strong predictors**: GAS\_RET, COAL\_RET, and CARBON\_RET show the highest correlations with electricity price variations.
2. **Country differences matter**: Germany and France show different patterns in energy production (notably nuclear).
3. **TabPFN outperforms XGBoost** on this dataset without requiring hyperparameter tuning.
4. **Overfitting challenge**: The gap between training and holdout performance indicates difficulty generalizing, possibly due to temporal patterns not captured by the features.

<a href="https://github.com/Claudio-Me/QRT_energy" target="_blank" rel="noopener" class="btn">
  <i class="fab fa-github"></i> View on GitHub
</a>
