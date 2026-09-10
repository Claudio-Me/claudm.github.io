---
title: "Electricity Price Prediction: France vs Germany"
excerpt: "QRT ENS Data Challenge 2023: Predicting daily electricity futures price variations using XGBoost."
collection: portfolio
---

## QRT ENS Data Challenge 2023

**Objective:** Model daily price variations of electricity futures contracts using weather, energy production, and commercial data for France and Germany.

**Evaluation Metric:** Spearman correlation between predictions and actual price changes.

**Approach:**
1. **Exploratory Data Analysis** - Understand data patterns and relationships
2. **Feature Engineering** - Create domain-informed features
3. **Modeling** - Train and tune an XGBoost model

---

## Exploratory Data Analysis

Before preprocessing, we analyze the raw data to understand patterns, distributions, and relationships that will inform our feature engineering decisions.

### 1. Missing Data Analysis

Understanding missing values helps inform preprocessing decisions. XGBoost handles missing values natively, but visualizing them reveals data collection patterns.

![Missing data analysis]({{ site.baseurl }}/images/qrt_energy/plot_1.png)

**Observations — Missing Data:**
- The only columns with missing values are `DE_NET_EXPORT` and `DE_NET_IMPORT` (and their FR counterparts), which is consistent with what we see in `X_train.head()`. This pattern likely reflects days when the exchange direction was zero or undefined (e.g., balanced import/export), rather than a data collection failure.
- The missingness rate appears low (< 10%), so XGBoost's native ability to handle NaN values makes imputation unnecessary for tree-based models. However, if these features turn out to be informative, imputing with column means (available via `fill_nan_entries()` in preprocessing) could help models that cannot handle missing values natively.

### 2. Target Variable Distribution

The target represents daily price variation of electricity futures. Understanding its distribution helps identify potential outliers and differences between countries.

![Target distribution]({{ site.baseurl }}/images/qrt_energy/plot_2.png)

**Observations — Target Distribution:**
- The overall target distribution is approximately centered around zero, as expected for daily *price changes* (returns) rather than absolute price levels. The mean and median are very close, suggesting limited systematic bias in the data.
- The distribution is roughly bell-shaped but with fat tails — large positive or negative price shocks occur more often than a normal distribution would predict. This is typical for financial returns and energy markets.
- Germany (DE) and France (FR) share a broadly similar distribution shape, but subtle differences in spread or kurtosis may reflect structural differences in their energy mixes (e.g., France's heavier reliance on nuclear, which is less volatile, versus Germany's higher dependency on gas and wind).
- No obvious bi-modality is visible, which suggests that treating this as a single regression problem (rather than a mixture model) is reasonable.

### 3. Feature Correlations

Understanding feature correlations helps identify redundant variables and reveals which features are most predictive of the target.

![Feature correlations]({{ site.baseurl }}/images/qrt_energy/plot_3.png)

**Observations — Correlation Heatmap:**
- **Commodity prices (`GAS_RET`, `COAL_RET`, `CARBON_RET`) are the strongest single-feature predictors** of the TARGET. Gas and carbon prices are particularly correlated with electricity futures, as marginal cost pricing in European electricity markets is typically set by gas-fired generation.
- Several DE and FR counterpart features (e.g., `DE_CONSUMPTION` / `FR_CONSUMPTION`, `DE_RESIDUAL_LOAD` / `FR_RESIDUAL_LOAD`) are moderately correlated with each other, reflecting the fact that weather and demand patterns across France and Germany are geographically linked.
- `DE_NET_IMPORT` and `DE_NET_EXPORT` are perfectly anti-correlated by construction (one is the negative of the other), so retaining both adds no information — the preprocessing pipeline correctly drops these redundant exchange columns.
- Weather features (`TEMP`, `RAIN`, `WIND`) show weaker direct correlations with TARGET, but they influence residual load and renewable production, making them indirectly predictive. Their interaction effects are better captured by tree-based models than linear ones.
- Residual load features (`DE_RESIDUAL_LOAD`, `FR_RESIDUAL_LOAD`) show a meaningful positive correlation with TARGET: when renewable generation cannot cover demand, the grid relies more on expensive fossil fuels, pushing prices up.

### 4. Country Comparison

Comparing key features between Germany and France reveals structural differences in the energy markets.

![Country comparison]({{ site.baseurl }}/images/qrt_energy/plot_4.png)

**Observations — Feature Comparison: Germany vs France:**
- **Consumption**: Both countries show similar medians (data is pre-normalised), but Germany tends to have a slightly wider spread, consistent with its larger absolute consumption. High-consumption days (heavy industry, cold winters) are more extreme in DE.
- **Temperature**: The distributions are broadly similar, as both countries lie in temperate Western Europe. France may show a marginally wider range due to its larger latitudinal extent (Mediterranean south vs. continental north), driving more seasonal variation in energy demand.
- **Wind**: Germany's wind distribution is noticeably shifted or more spread than France's, reflecting Germany's substantial offshore and onshore wind capacity in its northern coastal regions. France has significant wind resources too, but Germany is the larger producer. High-wind days in DE suppress residual load and push prices down.
- **Nuclear**: France's nuclear distribution is substantially higher than Germany's, reflecting France's large fleet of nuclear power plants, which provide stable, low-marginal-cost baseload generation. This structural difference partially explains why FR electricity prices are less sensitive to gas price spikes than DE.

### 5. Key Features vs Target

Visualizing relationships between the most predictive features and the target variable, colored by country.

![Key features vs target]({{ site.baseurl }}/images/qrt_energy/plot_5.png)

**Observations — Key Features vs Target:**
- **`GAS_RET` (European gas returns)**: Shows the clearest positive relationship with TARGET. On days when gas prices rise, electricity futures rise in step — a direct reflection of gas being the marginal-cost-setting technology across much of the European grid. Both DE and FR clusters follow the same trend, confirming this is a market-wide driver.
- **`COAL_RET` and `CARBON_RET`**: Both are positively correlated with TARGET, though the relationship is noisier than gas. Carbon prices affect the cost of all fossil-fuel generation, while coal is a substitute for gas during high gas-price periods. Their combined effect is often additive.
- **`DE_RESIDUAL_LOAD`**: Higher residual load (demand not met by renewables) is associated with higher prices, since the grid must activate more expensive dispatchable generation. The DE cloud sits distinctly from the FR cloud in some scatter regions, consistent with Germany's different generation mix.
- **`FR_RESIDUAL_LOAD`**: A similar positive trend holds for France. However, France's nuclear baseload dampens the slope — a unit increase in FR residual load has a smaller price impact than in Germany, because nuclear can absorb the demand at a lower cost than gas.

---

## Data Preprocessing

### Available Preprocessing Functions (from preprocessing.py)

The preprocessing pipeline applies several domain-informed transformations to enhance model performance:

#### Core Pipeline Functions (applied by default):

1. **`find_max_exchange_days(X)`** - Creates market decoupling indicator
   - Identifies days with extreme energy exchange between DE/FR (95th percentile)
   - Flags these as potential market decoupling events

2. **`aggregate_energy_sources(X)`** - Energy aggregation
   - Sums all energy sources per country
   - Calculates energy surplus (production - consumption)

3. **`find_holiday_features(X)`** - Detects low-demand periods
   - Computes `GLOBAL_CONSUMPTION` (DE + FR combined)
   - Flags bottom 33% consumption days as potential holidays
   - Adds temporal demand pattern features

4. **`aggregate_fossil_energy(X)`** - Groups fossil fuel sources
   - DE: Gas, Coal, Nuclear, Lignite → `DE_FOSSIL_ENERGY`
   - FR: Gas, Coal, Nuclear → `FR_FOSSIL_ENERGY`
   - Simplifies feature space while preserving energy mix information

5. **`transform_in_categorical_(X_train, X_test)`** - Categorical encoding
   - Converts `COUNTRY` to categorical type for XGBoost
   - Enables proper handling of country-specific patterns

#### Optional Functions (commented out due to performance impact):
- **`fill_missing_countries(X)`** - Imputes missing country labels based on DAY_ID patterns
- Missing value handling optimized for XGBoost's native capabilities

### Pipeline Configuration

The `data_preprocessing()` function accepts a `convert_categorical` parameter:
- `True`: Converts COUNTRY to categorical (recommended for XGBoost)
- `False`: Keeps COUNTRY as string (for models that expect raw string columns)

---

## Model Training

### XGBoost

We use 10-fold cross-validation with grid search to find optimal hyperparameters. The model is evaluated on:
1. **Cross-validation score** - Average Spearman correlation across folds
2. **Holdout evaluation** - Performance on 20% held-out data
3. **Country-specific analysis** - Separate performance metrics for Germany and France

**Regularization note:** Strong L1/L2 penalties (`reg_alpha`, `reg_lambda`) are used to combat overfitting.

---

## Results

| Model | CV Spearman | Holdout Spearman | Notes |
|-------|-------------|------------------|-------|
| **XGBoost** | 0.23 | ~0.15-0.18 | 10-fold CV with grid search |

## Key Findings

1. **Commodity prices are strong predictors**: GAS\_RET, COAL\_RET, and CARBON\_RET show the highest correlations with electricity price variations

2. **Country differences matter**: Germany and France show different patterns in energy production (notably nuclear), which affects model predictions differently

3. **Overfitting challenge**: The gap between training and holdout performance indicates the model struggles to generalize, possibly due to temporal patterns not captured by the features

## Future Improvements

- Explore time-series features (lagged variables, rolling statistics)
- Consider country-specific models for potentially better per-country performance
- Investigate overfitting more in depth

<a href="https://github.com/Claudio-Me/QRT_energy" target="_blank" rel="noopener" class="btn">
  <i class="fab fa-github"></i> View on GitHub
</a>
