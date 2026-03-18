# Exploring Power Outage Trends in the US

**By: Zachary Louie** 

## Introduction

This project analyzes a dataset from Purdue University's Laboratory for Advancing Sustainable Critical Infrastructure. It contains information on major outage events in the continental United States from January 2000 to July 2015. The dataset contains 1,534 rows where each row represents a major power event 

**Central Question: Has the frequency and severity of severe weather outages increased over time?**

As climate change becomes a more pressing issue, U.S. power grids face growing stress from extreme weather events. Understanding whether severe weather outages are becoming more frequent or impactful is imperative for helping utility companies and policymakers prioritize upgrading grid infrastructure and allocate resources effectively.

The following columns from the dataset are relevant to the central question:

| Column | Description |
|--------|-------------|
| `YEAR` | Year the outage occurred |
| `MONTH` | Month the outage occurred |
| `U.S._STATE` | State where the outage occurred |
| `CLIMATE.REGION` | U.S. climate region of the outage |
| `CAUSE.CATEGORY` | Category of the cause of the outage |
| `ANOMALY.LEVEL` | Climate anomaly level at the time of the outage |
| `CLIMATE.CATEGORY` | Climate category (normal, warm, cold) |
| `OUTAGE.DURATION` | Duration of the outage in minutes |
| `CUSTOMERS.AFFECTED` | Number of customers affected |
| `POPULATION` | Population of the state |
| `POPPCT_URBAN` | Percentage of urban population in the state |

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning 

To prepare the dataset for analysis, the following data cleaning stpes were performed:

1. **Selected relevant columns**: Only the columns relevant to the central question were kept, including `YEAR`, `MONTH`, `U.S._STATE`, `CLIMATE.REGION`, `CAUSE.CATEGORY`, `ANOMALY.LEVEL`, `CLIMATE.CATEGORY`, `OUTAGE.DURATION`, `CUSTOMERS.AFFECTED`, `POPULATION`, and `POPPCT_URBAN`.
2. **Standardized column names**: All column names were converted to lowercase with underscores replacing spaces and periods for consistency.
3. **Combined timestamps**: `OUTAGE.START.DATE` and `OUTAGE.START.TIME` were combined into a single `pd.Timestamp` column called `outage_start`. Similarly, `OUTAGE.RESTORATION.DATE` and `OUTAGE.RESTORATION.TIME` were combined into `outage_restoration`.
4. **Converted data types**: `anomaly_level`, `outage_duration`, `customers_affected`, `population`, and `poppct_urban` were all converted to numeric types.
5. **Dropped incomplete rows**: 9 rows with missing `outage_start` values were dropped, as they were also missing `month`, `anomaly_level`, and `climate_category`, making them unusable for analysis.
6. **Recomputed outage duration**: `outage_duration` was recomputed from `outage_start` and `outage_restoration` to fill in previously missing values where possible, leaving 49 rows still missing where `outage_restoration` was unavailable.

The head of the cleaned DataFrame is shown below:

|   year |   month | state     | climate_region     | cause_category     |   outage_duration |   customers_affected | outage_start        |
|-------:|--------:|:----------|:-------------------|:-------------------|------------------:|---------------------:|:--------------------|
|   2011 |       7 | Minnesota | East North Central | severe weather     |              3060 |                70000 | 2011-07-01 17:00:00 |
|   2014 |       5 | Minnesota | East North Central | intentional attack |                 1 |                  nan | 2014-05-11 18:38:00 |
|   2010 |      10 | Minnesota | East North Central | severe weather     |              3000 |                70000 | 2010-10-26 20:00:00 |
|   2012 |       6 | Minnesota | East North Central | severe weather     |              2550 |                68200 | 2012-06-19 04:30:00 |
|   2015 |       7 | Minnesota | East North Central | severe weather     |              1740 |               250000 | 2015-07-18 02:00:00 |

### Univariate Analysis

<iframe
  src="assets/uni_plt1.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

The bar chart above shows the distribution of power outages by cause category. Severe weather is by far the most common cause, accounting for nearly 750 outages — almost double the next most common cause, intentional attacks. This confirms that severe weather is the dominant driver of major power outages in the U.S. and justifies the focus on it.

<iframe
  src="assets/uni_plt2.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

The bar chart above shows the distribution of power outages by year. There is a clear upward trend from 2000 to 2011, peaking at over 250 outages, before declining. Note that 2016 appears low because the dataset only covers through July 2016.

### Bivariate Analysis

<iframe
  src="assets/bi_plt1.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

The line chart above shows the median outage duration of severe weather outages over time. Interestingly, the trend is downward — outages in the early 2000s lasted much longer on average, suggesting improvements in grid restoration speed over time despite more frequent outages.

<iframe
  src="assets/bi_plt2.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

The line chart above shows the frequency of severe weather outages per year. There is a clear upward trend from 2000 to 2011, with a peak of over 100 outages in 2011, suggesting that severe weather outages have become more frequent over time.

### Interesting Aggregates

The table below shows the number of severe weather outages by climate region, sorted in descending order.

| climate_region     |   outage_count |
|:-------------------|---------------:|
| Northeast          |            176 |
| Central            |            134 |
| Southeast          |            116 |
| South              |            112 |
| East North Central |            104 |
| West               |             70 |
| Northwest          |             29 |
| Southwest          |             10 |
| West North Central |              4 |

The Northeast and Central regions experience the most severe weather outages, reflecting their exposure to hurricanes, nor'easters, and severe thunderstorms.

The table below shows the median outage duration by climate category for severe weather outages.

| climate_category   |   median_duration_hours |
|:-------------------|------------------------:|
| cold               |                 38.6583 |
| normal             |                 41.85   |
| warm               |                 42      |


## Assessment of Missingness
### MNAR Analysis

The `customers_affected` column in the cleaned dataset may be MNAR because the likelihood of a value being missing heavily depends on the value itself. Specifically, outages that affected very few customers may fall below an internal reporting threshold of utility companies, meaning they are less likely to be documented. This creates a systemic pattern where smaller outages are underrepresented in the data. Additional data that could help explain this missingness — potentially making it MAR — would include utility company reporting guidelines or minimum thresholds for recording outage impacts.

### Missingness Dependency

Permutation tests were performed to analyze the dependency of the missingness of `customers_affected` on other columns.

**Does depend on: `cause_category`**

A permutation test was run using TVD as the test statistic. The observed TVD was 0.5576 with a p-value of 0.0000, which is below the significance level of 0.05. The null hypothesis is rejected — the missingness of `customers_affected` depends on `cause_category`. This makes sense — intentional attacks are far less likely to have customer impact data reported compared to severe weather outages.

**Does not depend on: `climate_category`**

A permutation test was run using TVD as the test statistic. The observed TVD was 0.0353 with a p-value of 0.3500, which is above the significance level of 0.05. The null hypothesis is not rejected — the missingness of `customers_affected` does not depend on `climate_category`.

The plot below shows the distribution of `cause_category` when `customers_affected` is missing vs. not missing, illustrating the dependency found above.

<iframe
  src="assets/fig_missing.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>


## Hypothesis Testing
**Null Hypothesis**: The average number of severe weather outages per year is the same in the first half (2000-2007) and the second half (2008-2016) of the dataset.

**Alternative Hypothesis**: The average number of severe weather outages per year is higher in the second half (2008-2016) than in the first half (2000-2007).

**Test Statistic**: Difference in means (second half mean minus first half mean)

**Significance Level**: 0.05

**Observed Difference**: 25.31

**P-value**: 0.0180

**Conclusion**: Since the p-value of 0.0180 is below the significance level of 0.05, the null hypothesis is rejected. The data is consistent with the idea that severe weather outages became more frequent in the second half of the dataset (2008-2016) compared to the first half (2000-2007). However, since this is a permutation test and not a randomized controlled trial, it cannot be concluded that time itself caused the increase.

<iframe
  src="assets/fig_hyp.html"
  width="800"
  height="500"
  frameborder="0"
></iframe>

## Framing a Prediction Problem
**Prediction Problem**: Predict whether a power outage was caused by severe weather or not.

**Type**: Binary classification (1 = severe weather, 0 = all other causes)

**Response Variable**: `cause_category` — chosen because it is central to the investigation of whether severe weather is a growing threat to the U.S. power grid.

**Evaluation Metric**: F1-score — chosen over accuracy because the dataset is imbalanced (severe weather accounts for roughly 50% of outages). Accuracy would be misleading since a model that always predicts severe weather would still achieve ~50% accuracy. F1-score balances precision and recall, penalizing models that are lazy or overconfident.

**Time of Prediction**: The prediction is assumed to be made at the moment an outage begins. Therefore only features known at that time are used: `year`, `month`, `climate_region`, `climate_category`, `anomaly_level`, `population`, and `poppct_urban`. `outage_duration` and `customers_affected` are excluded since those are only known after the outage is resolved.


## Baseline Model
The baseline model is a Decision Tree Classifier trained on three features:

- `month` (quantitative) — left as-is
- `climate_region` (nominal) — encoded using One Hot Encoding
- `climate_category` (nominal) — encoded using One Hot Encoding

All steps were implemented in a single sklearn Pipeline. The model achieved a **baseline F1-score of 0.6667** on the test set. While better than random, this leaves clear room for improvement, as the model only uses basic climate and temporal information without any engineered features.


## Final Model

Two new features were engineered to improve upon the baseline:

- **`season`** (derived from `month`) — severe weather outages are highly seasonal, with storms peaking in summer and winter. Grouping months into seasons captures this pattern more effectively than raw month numbers.
- **`urban_population`** (derived from `population` and `poppct_urban`) — computed as `population * poppct_urban / 100`, this captures the actual number of urban residents in a state. Urban areas tend to have more comprehensive outage reporting, which may help distinguish severe weather outages from other causes.

`anomaly_level` was also included as an additional quantitative feature, since more extreme climate anomalies are more likely to produce severe weather conditions.

The following hyperparameters were tuned using GridSearchCV with 5-fold cross validation:

- `max_depth`: [3, 5, 10, 15, None]
- `min_samples_split`: [2, 5, 10]

The best parameters were `max_depth=10` and `min_samples_split=10`. The final model achieved an **F1-score of 0.7153** on the test set, improving upon the baseline F1-score of 0.6667.


## Fairness Analysis

**Group X**: Early years (2000-2007)

**Group Y**: Later years (2008-2016)

**Evaluation Metric**: F1-score

**Null Hypothesis**: The model is fair. Its F1-score for early years and later years are roughly the same, and any differences are due to random chance.

**Alternative Hypothesis**: The model is unfair. Its F1-score for later years is lower than its F1-score for early years.

**Test Statistic**: Difference in F1-scores (late minus early)

**Significance Level**: 0.05

**Observed Difference**: -0.0938

**P-value**: 0.0000

**Conclusion**: Since the p-value of 0.0000 is below the significance level of 0.05, the null hypothesis is rejected. The model performs significantly worse on later years (2008-2016) than on early years (2000-2007). This suggests the model may not generalize as well to more recent outage patterns, potentially because the nature of severe weather outages has changed over time due to climate change.