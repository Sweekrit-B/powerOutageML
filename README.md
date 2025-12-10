# Analyzing the Impact of Climate and Regional Information on Power Outages
Final project for DSC 80 @ UCSD by Sweekrit Bhatnagar

## Introduction

### Choice of dataset

In this project, I analyze major outage events witnessed in the continental US from January 2000 to July 2016, where a "major outage" refers to an outage that impacted at least 50,000 customers or caused a firm load loss of 300 MW. In addition to the outages' characteristics, the dataset also contains information regarding regional climate information, land-use, electricity consumption patterns, and economic characteristics. 

I chose this dataset because it yields itself to interesting analysis on variables that predict outage, especially considering the diversity and amount of geographic, climate, and economic information available. Furthermore, the dataset itself is important due to the **real world consequences** of power outages - these events have major societal impact and affect key infrastructure, including hospitals, transport systems, communication networks, etc. Working with this dataset allows us to do important data analysis on the causes of these events, and inform policymaking and mitigation strategies in order to product cities and homes from major power outages.

### Questions brainstorm and selection

While looking at the dataset, I thought of the following questions that I could analyze:
1. What is the background regional and climate information (i.e. the environment) of major power outages of varying severity and cause?
2. How do socioeconomic conditions of the region affect the severity of outages as measured by the duration and number of people affected?
3. Is there a distinct geographic association with coastal regions and power outage cause and/or severity? What background characteristics play into the distinction?

However, I ultimately chose to go with the first question: 
**What is the background regional and climate information (i.e. the environment) of major power outages of varying severity and cause?**

Specifically, I plan to look into how various features that can affect regional climate (such as the geographic region, the month the outage occurred, the presence of a hurricane, etc.) play into the impact characteristics of outages, focusing on outage duration and the cause category.

Reasons why this question is important:
1. Different climate patterns can strongly influence storm severity, equipment stress, grid load, etc. and can be key preidctors about whether an area is affected by a major power outage.
2. Climate change sees an increased variance of climate events - being able to predict power outages quickly before they happened can be important to save infrastucture in times of climate disaster (ex. hurricanes or major storms).

### Description of key columns and overall shape of dataset

There are **1534 rows** in the dataset, indicating the presence of 1534 major outage events that occurred in the time period of the study, of which we find the following features relevant:

<table>
  <thead>
    <tr>
      <th>Category</th>
      <th>Variable</th>
      <th>Description</th>
    </tr>
  </thead>
  <tbody>
    <tr><td rowspan="5">General Information</td><td>YEAR</td><td>Year when the outage event occurred</td></tr>
    <tr><td>MONTH</td><td>Month when the outage event occurred</td></tr>
    <tr><td>U.S._STATE</td><td>U.S. state where the outage occurred</td></tr>
    <tr><td>POSTAL.CODE</td><td>Postal code of the U.S. state</td></tr>
    <tr><td>NERC.REGION</td><td>NERC region involved in the outage</td></tr>
    <tr><td rowspan="3">Regional Climate Information</td><td>CLIMATE.REGION</td><td>U.S. climate region as defined by the National Centers for Environmental Information (9 regions total)</td></tr>
    <tr><td>ANOMALY.LEVEL</td><td>Oceanic Niño/La Niña index (ONI), 3-month running mean of SST anomalies</td></tr>
    <tr><td>CLIMATE.CATEGORY</td><td>Climate category (“Warm,” “Cold,” or “Normal”) based on ONI index ±0.5°C</td></tr>
    <tr><td rowspan="10">Outage Event Information</td><td>OUTAGE.START.DATE</td><td>Calendar day when the outage started</td></tr>
    <tr><td>OUTAGE.START.TIME</td><td>Time of day when the outage started</td></tr>
    <tr><td>OUTAGE.RESTORATION.DATE</td><td>Calendar day when power was fully restored</td></tr>
    <tr><td>OUTAGE.RESTORATION.TIME</td><td>Time of day when power was fully restored</td></tr>
    <tr><td>CAUSE.CATEGORY</td><td>High-level category describing the cause of the outage</td></tr>
    <tr><td>CAUSE.CATEGORY.DETAIL</td><td>Detailed description of the event cause</td></tr>
    <tr><td>HURRICANE.NAMES</td><td>Hurricane name if the outage was caused by a hurricane</td></tr>
    <tr><td>OUTAGE.DURATION</td><td>Duration of the outage in minutes</td></tr>
    <tr><td>DEMAND.LOSS.MW</td><td>Peak demand lost during the outage (megawatts)</td></tr>
    <tr><td>CUSTOMERS.AFFECTED</td><td>Number of customers impacted by the outage</td></tr>
    <tr><td rowspan="11">Regional Land-Use Characteristics</td><td>POPULATION</td><td>Population of the U.S. state in the given year</td></tr>
    <tr><td>POPPCT_URBAN</td><td>Percentage of the population living in urban areas</td></tr>
    <tr><td>POPPCT_UC</td><td>Percentage of the population living in urban clusters</td></tr>
    <tr><td>POPDEN_URBAN</td><td>Population density of urban areas (persons/sq. mile)</td></tr>
    <tr><td>POPDEN_UC</td><td>Population density of urban clusters (persons/sq. mile)</td></tr>
    <tr><td>POPDEN_RURAL</td><td>Population density of rural areas (persons/sq. mile)</td></tr>
    <tr><td>AREAPCT_URBAN</td><td>Percentage of the state’s land area classified as urban</td></tr>
    <tr><td>AREAPCT_UC</td><td>Percentage of the state’s land area classified as urban clusters</td></tr>
    <tr><td>PCT_LAND</td><td>Percentage of total U.S. land area represented by the state</td></tr>
    <tr><td>PCT_WATER_TOT</td><td>Percentage of total U.S. water area represented by the state</td></tr>
    <tr><td>PCT_WATER_INLAND</td><td>Percentage of total U.S. inland water area represented by the state</td></tr>
  </tbody>
</table>

## Data Cleaning and Exploratory Data Analysis

I took the following steps to clean my data. Note that in this step, I DID NOT impute `NaN` values, as those will be important for future missingness analysis:
1. Set the index of the dataset using a unique identifier `OBS` instead of sticking with the default sequence. This allowed for a cleaner dataset that did not have a duplicate index variable, preventing its inclusion in exploratory data analysis (as it would just be a static range) or predictions.
2. Perform datetime conversions. There seem to be separate columns for the calendar date and the time - combining these would make future analysis easier, and converting to datetime would allow for them to become quantitative discrete variables that I can use in future analysis if I choose to. Specifically, I combine `OUTAGE.START.DATE` and `OUTAGE.START.TIME` and `OUTAGE.RESTORATION.DATE` and `OUTAGE.RESTORATION.TIME` and convert them using `pd.to_datetime()`
3. Drop any unnecessary and/or redundant columns. Although in Steps 3-8 I am already filtering for the columns that I want, this will make my data cleaner and more easily perform the univariate and bi-variate analysis of this step. The columns I drop are `OUTAGE.START.TIME`, `OUTAGE.RESTORATION.TIME`, and `U.S._STATE`. 
   - I drop `OUTAGE.START.TIME` and `OUTAGE.RESTORATION.TIME` because their data has already been included into `OUTAGE.START.DATE` and `OUTAGE.RESTORATION.DATE`
   - I drop `U.S._STATE` because it contains the same information as `POSTAL.CODE`
   - Note that although I am looking at specifically climate and regional information, I don't drop any more columns. This is because in the case that my research question changes further along in the project to account for economic factors (for example, in Step 8), I want the freedom to access these values.
4. I replace 0 values in the columns that I am using to look at my impact with `NaN`. These include `CUSTOMERS.AFFECTED` and `DEMAND.LOSS.MW`. This is because in the data specifications, we know that the amount of customers affected is at least 50,000 and the demand lost is at least 300 MW.
    - I do not replace 0 values in `OUTAGE.DURATION`. Although it is unlikely that a major outage event lasts 0 minutes there is no indication that this is a requirement for the dataset, and it is possible that power was restored immediately. 

Here is the cleaned data:
|   YEAR |   MONTH | POSTAL.CODE   | NERC.REGION   | CLIMATE.REGION     |   ANOMALY.LEVEL | CLIMATE.CATEGORY   | OUTAGE.START.DATE   | OUTAGE.RESTORATION.DATE   | CAUSE.CATEGORY     | CAUSE.CATEGORY.DETAIL   |   HURRICANE.NAMES |   OUTAGE.DURATION |   DEMAND.LOSS.MW |   CUSTOMERS.AFFECTED |   RES.PRICE |   COM.PRICE |   IND.PRICE |   TOTAL.PRICE |   RES.SALES |   COM.SALES |   IND.SALES |   TOTAL.SALES |   RES.PERCEN |   COM.PERCEN |   IND.PERCEN |   RES.CUSTOMERS |   COM.CUSTOMERS |   IND.CUSTOMERS |   TOTAL.CUSTOMERS |   RES.CUST.PCT |   COM.CUST.PCT |   IND.CUST.PCT |   PC.REALGSP.STATE |   PC.REALGSP.USA |   PC.REALGSP.REL |   PC.REALGSP.CHANGE |   UTIL.REALGSP |   TOTAL.REALGSP |   UTIL.CONTRI |   PI.UTIL.OFUSA |   POPULATION |   POPPCT_URBAN |   POPPCT_UC |   POPDEN_URBAN |   POPDEN_UC |   POPDEN_RURAL |   AREAPCT_URBAN |   AREAPCT_UC |   PCT_LAND |   PCT_WATER_TOT |   PCT_WATER_INLAND |
|-------:|--------:|:--------------|:--------------|:-------------------|----------------:|:-------------------|:--------------------|:--------------------------|:-------------------|:------------------------|------------------:|------------------:|-----------------:|---------------------:|------------:|------------:|------------:|--------------:|------------:|------------:|------------:|--------------:|-------------:|-------------:|-------------:|----------------:|----------------:|----------------:|------------------:|---------------:|---------------:|---------------:|-------------------:|-----------------:|-----------------:|--------------------:|---------------:|----------------:|--------------:|----------------:|-------------:|---------------:|------------:|---------------:|------------:|---------------:|----------------:|-------------:|-----------:|----------------:|-------------------:|
|   2011 |       7 | MN            | MRO           | East North Central |            -0.3 | normal             | 2011-07-01 17:00:00 | 2011-07-03 20:00:00       | severe weather     | nan                     |               nan |              3060 |              nan |                70000 |       11.6  |        9.18 |        6.81 |          9.28 | 2.33292e+06 | 2.11477e+06 | 2.11329e+06 |   6.56252e+06 |      35.5491 |      32.225  |      32.2024 |         2308736 |          276286 |           10673 |           2595696 |        88.9448 |        10.644  |         0.4112 |              51268 |            47586 |          1.07738 |                 1.6 |           4802 |          274182 |       1.75139 |             2.2 |      5348119 |          73.27 |       15.28 |           2279 |      1700.5 |           18.2 |            2.14 |          0.6 |    91.5927 |         8.40733 |            5.47874 |
|   2014 |       5 | MN            | MRO           | East North Central |            -0.1 | normal             | 2014-05-11 18:38:00 | 2014-05-11 18:39:00       | intentional attack | vandalism               |               nan |                 1 |              nan |                  nan |       12.12 |        9.71 |        6.49 |          9.28 | 1.58699e+06 | 1.80776e+06 | 1.88793e+06 |   5.28423e+06 |      30.0325 |      34.2104 |      35.7276 |         2345860 |          284978 |            9898 |           2640737 |        88.8335 |        10.7916 |         0.3748 |              53499 |            49091 |          1.08979 |                 1.9 |           5226 |          291955 |       1.79    |             2.2 |      5457125 |          73.27 |       15.28 |           2279 |      1700.5 |           18.2 |            2.14 |          0.6 |    91.5927 |         8.40733 |            5.47874 |
|   2010 |      10 | MN            | MRO           | East North Central |            -1.5 | cold               | 2010-10-26 20:00:00 | 2010-10-28 22:00:00       | severe weather     | heavy wind              |               nan |              3000 |              nan |                70000 |       10.87 |        8.19 |        6.07 |          8.15 | 1.46729e+06 | 1.80168e+06 | 1.9513e+06  |   5.22212e+06 |      28.0977 |      34.501  |      37.366  |         2300291 |          276463 |           10150 |           2586905 |        88.9206 |        10.687  |         0.3924 |              50447 |            47287 |          1.06683 |                 2.7 |           4571 |          267895 |       1.70627 |             2.1 |      5310903 |          73.27 |       15.28 |           2279 |      1700.5 |           18.2 |            2.14 |          0.6 |    91.5927 |         8.40733 |            5.47874 |
|   2012 |       6 | MN            | MRO           | East North Central |            -0.1 | normal             | 2012-06-19 04:30:00 | 2012-06-20 23:00:00       | severe weather     | thunderstorm            |               nan |              2550 |              nan |                68200 |       11.79 |        9.25 |        6.71 |          9.19 | 1.85152e+06 | 1.94117e+06 | 1.99303e+06 |   5.78706e+06 |      31.9941 |      33.5433 |      34.4393 |         2317336 |          278466 |           11010 |           2606813 |        88.8954 |        10.6822 |         0.4224 |              51598 |            48156 |          1.07148 |                 0.6 |           5364 |          277627 |       1.93209 |             2.2 |      5380443 |          73.27 |       15.28 |           2279 |      1700.5 |           18.2 |            2.14 |          0.6 |    91.5927 |         8.40733 |            5.47874 |
|   2015 |       7 | MN            | MRO           | East North Central |             1.2 | warm               | 2015-07-18 02:00:00 | 2015-07-19 07:00:00       | severe weather     | nan                     |               nan |              1740 |              250 |               250000 |       13.07 |       10.16 |        7.74 |         10.43 | 2.02888e+06 | 2.16161e+06 | 1.77794e+06 |   5.97034e+06 |      33.9826 |      36.2059 |      29.7795 |         2374674 |          289044 |            9812 |           2673531 |        88.8216 |        10.8113 |         0.367  |              54431 |            49844 |          1.09203 |                 1.7 |           4873 |          292023 |       1.6687  |             2.2 |      5489594 |          73.27 |       15.28 |           2279 |      1700.5 |           18.2 |            2.14 |          0.6 |    91.5927 |         8.40733 |            5.47874 |