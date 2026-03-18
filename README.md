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

Here is the head of the cleaned dataset:
|   year |   month | state     | climate_region     | cause_category     |   anomaly_level | climate_category   |   outage_duration |   customers_affected |   population |   poppct_urban | outage_start        | outage_restoration   |   is_severe_weather |
|-------:|--------:|:----------|:-------------------|:-------------------|----------------:|:-------------------|------------------:|---------------------:|-------------:|---------------:|:--------------------|:---------------------|--------------------:|
|   2011 |       7 | Minnesota | East North Central | severe weather     |            -0.3 | normal             |              3060 |                70000 |  5.34812e+06 |          73.27 | 2011-07-01 17:00:00 | 2011-07-03 20:00:00  |                   1 |
|   2014 |       5 | Minnesota | East North Central | intentional attack |            -0.1 | normal             |                 1 |                  nan |  5.45712e+06 |          73.27 | 2014-05-11 18:38:00 | 2014-05-11 18:39:00  |                   0 |
|   2010 |      10 | Minnesota | East North Central | severe weather     |            -1.5 | cold               |              3000 |                70000 |  5.3109e+06  |          73.27 | 2010-10-26 20:00:00 | 2010-10-28 22:00:00  |                   1 |
|   2012 |       6 | Minnesota | East North Central | severe weather     |            -0.1 | normal             |              2550 |                68200 |  5.38044e+06 |          73.27 | 2012-06-19 04:30:00 | 2012-06-20 23:00:00  |                   1 |
|   2015 |       7 | Minnesota | East North Central | severe weather     |             1.2 | warm               |              1740 |               250000 |  5.48959e+06 |          73.27 | 2015-07-18 02:00:00 | 2015-07-19 07:00:00  |                   1 |

## Data Cleaning and Exploratory Data Analysis

### Data Cleaning 

To prepare the dataset for analysis, the following data cleaning stpes were performed:

1. **Selected relevant columns**: Only the columns relevant to the central question were kept, including `YEAR`, `MONTH`, `U.S._STATE`, `CLIMATE.REGION`, `CAUSE.CATEGORY`, `ANOMALY.LEVEL`, `CLIMATE.CATEGORY`, `OUTAGE.DURATION`, `CUSTOMERS.AFFECTED`, `POPULATION`, and `POPPCT_URBAN`.
2. **Standardized column names**: All column names were converted to lowercase with underscores replacing spaces and periods for consistency.
3. **Combined timestamps**: `OUTAGE.START.DATE` and `OUTAGE.START.TIME` were combined into a single `pd.Timestamp` column called `outage_start`. Similarly, `OUTAGE.RESTORATION.DATE` and `OUTAGE.RESTORATION.TIME` were combined into `outage_restoration`.
4. **Converted data types**: `anomaly_level`, `outage_duration`, `customers_affected`, `population`, and `poppct_urban` were all converted to numeric types.
5. **Dropped incomplete rows**: 9 rows with missing `outage_start` values were dropped, as they were also missing `month`, `anomaly_level`, and `climate_category`, making them unusable for analysis.
6. **Recomputed outage duration**: `outage_duration` was recomputed from `outage_start` and `outage_restoration` to fill in previously missing values where possible, leaving 49 rows still missing where `outage_restoration` was unavailable.


## Assessment of Missingness

## Hypothesis Testing

## Framing a Prediction Problem

## Baseline Model

## Final Model

## Fairness Analysis

