# Power_Outages_Prediction
by Yuxuan Zhu, Andy Li

# Introduction
This is a project overview for investigation and analysis of power outages in the United States. The power outages dataset with data from the US Department of Energy and compiled by Mukherjee et al. contains information on the circumstances and related information surrounding power outages that occur within the United States. Given the substantial economic losses and societal disruptions caused by large-scale grid failures, identifying the specific times and locations of these events is critical for optimizing resource allocation and hardening infrastructure. Consequently, we have decided to answer the question of when and where are major power outages most likely to occur. Major power outages are power outages that exceed 50000 customers affected or 300 MW power loss, as defined by the Department of Energy. By isolating the major events, we aim to discover seasonal trends and regional vulnerabilities that can inform future grid-strengthening initiatives and emergency response planning.

Original dataset can be found at https://engineering.purdue.edu/LASCI/research-data/outages with 1534 rows and 57 columns in total. 57 columns generally indicates 57 features of power outages, while only 13 columns are relevant to our focus of studies:

| Column | Description |
| :--- | :--- |
| `'YEAR'` | Year an outage occurred |
| `'MONTH'` | Month an outage occurred |
| `'OUTAGE.START.DATE'` | Exact day/month/year an outage occured |
| `'OUTAGE.START.TIME'` | Exact time an outage occured during the day |
| `'U.S._STATE'` | Specific U.S. state where an outage occured |
| `'CLIMATE.REGION'` | U.S. climate region where the outage occurred, specified by NCEI |
| `'CAUSE.CATEGORY'` | Primary reason or category of the event that triggered the outage |
| `'CLIMATE.CATEGORY'` | Classification of the generalized climate climate conditions during the outage |
| `'DEMAND.LOSS.MW'` | Amount of power demand lost during the outage in MW |
| `'ANOMALY.LEVEL'` | The Oceanic Niño Index numeric value at the time of the outage |
| `'CUSTOMERS.AFFECTED'` | Total number of utility customers that lost power during the outage |
| `'POPULATION'` | Total population of the affected state during the year of the outage |
| `'OUTAGE.DURATION'` | Total length of time the power outage lasted |

# Data Cleaning 
Before we dig into the dataset, we need to clean it first.

The data cleaning begins by loading the power outage dataset from an Excel file and removing intial rows and columns that are unnecessary(like varaible names and units). It then filters the dataset to retain only a specific subset of 13 key features relevant to interests of our studies, namely: ["YEAR", "MONTH", "OUTAGE.START.DATE", "OUTAGE.START.TIME", "U.S._STATE", "CLIMATE.REGION", "CAUSE.CATEGORY", "CLIMATE.CATEGORY", "DEMAND.LOSS.MW", "ANOMALY.LEVEL", "CUSTOMERS.AFFECTED", "POPULATION", "OUTAGE.DURATION"]. 
Furthermore, we explicitly converted critical numeric columns ("OUTAGE.DURATION", "DEMAND.LOSS.MW", and "ANOMALY.LEVEL") to numeric data types, which is crucial for our analysis as the original data type is object that cannot be used in graphing. During this process, any non-numeric or corrupted values are safely coerced into missing values (NaN). 
We also combine 'OUTAGE.START.DATE' and 'OUTAGE.START.TIME' into a new pd.Timestamp column named 'OUTAGE.START', and dropped the now-redundant original date and time columns.
The first few rows of the dataset after cleaning looks like this
<iframe
  src="assets/cleaned.html"
  width="800"
  height="600"
  frameborder="0"
></iframe>

# Exploratory Data Analysis
For our anlaysis, we choose to perform a nivariate analysis on single variables.

We are interested in how power outages varied over states:
<iframe src="assets/fig1.html" width="100%" height="450px" frameborder="0"></iframe>
It seems califronia suffered from significantly higher power outages compared to other states

We also want to know how have powers outages been changing over years:
<iframe src="assets/fig2.html" width="100%" height="450px" frameborder="0"></iframe>
The graph indicates that power outages have been increasing generally with 2011 reaching a peak.


Now that we have an impression of some single variables, we would like to investigate connection between variables by performing Bivariate Analysis.

We plotted outage trend by year of three different climate categories on a single graph for comparison:
<iframe src="assets/fig4.html" width="100%" height="450px" frameborder="0"></iframe>
We can see the trend is close but warm regions seems to have a slightly lower power outages trend.

Could there be a difference in month where a certain month is more likey to experience power outages?
<iframe src="assets/fig6.html" width="100%" height="450px" frameborder="0"></iframe>
At first glance, September and December appears to be vulnerable months when power outages take place.

# Grouping and Aggregates

We first aggregated between region, customers, and duration:
<iframe src="assets/agg1.html" width="100%" height="450px" frameborder="0"></iframe>
It seems the region with the highest number of incidents resolves them relatively quickly, whereas the region with 137 outages suffers from a prolonged median duration of 3,120 minutes. In the most severely delayed region, the median number of affected customers climbs to a number of 111,196.5, suggesting massive infrastructure crises.

We then aggregate between cause, customers, and duration:
<iframe src="assets/agg2.html" width="100%" height="450px" frameborder="0"></iframe>
The graph to some extends tells severe weather conditions drive the vast majority of outages, resulting in massive consumer disruption alongside an extended median recovery time of 2,460 minutes. In contrast, internal or operational errors occur frequently but are resolved rapidly with a median duration of 56 minutes.


# Assessment of Missingness
| Column Name | Missing Values |
| :--- | ---: |
| YEAR | 0 |
| MONTH | 0 |
| U.S._STATE | 0 |
| CLIMATE.REGION | 5 |
| CAUSE.CATEGORY | 0 |
| CLIMATE.CATEGORY | 0 |
| DEMAND.LOSS.MW | 672 |
| ANOMALY.LEVEL | 0 |
| CUSTOMERS.AFFECTED | 420 |
| POPULATION | 0 |
| OUTAGE.DURATION | 0 |
| OUTAGE.START | 0 |

The two columns most likely to be NMAR are DEMAND.LOSS.MW and CUSTOMERS.AFFECTED. The data is from OE-417 form Schedule 1 (DOE emergency form) from the Department of Energy, which electric utilities are legally required to file for major incidents. During extreme chaos, where the values are likely extremely high for these two columns, they may be left blank because the amount is uncountable.

Additionally, under-reporting may happen for these two columns. Because the definition of "Major Outage" is defined as 50,000+ customers affected or continuous loss of 300MW for over 30 minutes, which are the two columns in question, utility companies may leave these blank to avoid strict regulation regarding borderline values.

For Missingness Dependency, we set up permutation tests to determine whether the missingness of the **`CUSTOMERS.AFFECTED`** column depends on other features.

1.  **Dependency on `CLIMATE.CATEGORY` (Dependent)**
    * **Null Hypothesis ($H_0$):** The missingness of customers affected is independent of the macro climate category.
    * **Alternative Hypothesis ($H_A$):** The missingness of customers affected depends on the macro climate category.
    * **Test Statistic:** Total Variation Distance (TVD) between the distribution of climate categories when customers affected is missing vs. when it is present.
    * **Observed TVD:** 0.142
    * **p-value:** `< 0.01`
    * **Conclusion:** We reject the null hypothesis. The missingness of customer records exhibits a statistically significant dependency on climate conditions.
  <iframe src="assets/fig7.html" width="100%" height="400px" frameborder="0"></iframe>

2.  **Dependency on `YEAR` (Independent)**
    * **Null Hypothesis ($H_0$):** The missingness of customers affected is independent of the year the outage took place.
    * **Alternative Hypothesis ($H_A$):** The missingness of customers affected depends on the year the outage took place.
    * **Test Statistic:** Absolute difference in mean year between the missing and non-missing groups.
    * **p-value:** `0.54`
    * **Conclusion:** We fail to reject the null hypothesis. The likelihood of a customer count being missing shows no statistically verifiable link to the calendar year of the incident.
  <iframe src="assets/fig8.html" width="100%" height="400px" frameborder="0"></iframe>

# Hypothesis Testing
To assess whether the underlying triggers of outages have systematically different severity footprints, we investigated the duration patterns between environmental disasters and systemic grid failures.

* **Null Hypothesis ($H_0$):** The distribution of outage durations for severe weather events and system malfunctions is identical. Any observed difference in medians is due to random sampling chance.
* **Alternative Hypothesis ($H_A$):** The distribution of outage durations for severe weather events has a higher median than that of system malfunctions, reflecting the added physical barriers to restoration.
* **Test Statistic:** Difference in Sample Medians ($\text{Median}_{\text{Weather}} - \text{Median}_{\text{Malfunction}}$).
* **Significance Level ($\alpha$):** 0.05.

### Test Results
Following 1,000 random reshuffling permutations, our simulation yielded a **p-value of < 0.001**. 

  <iframe src="assets/fig9.html" width="100%" height="400px" frameborder="0"></iframe>

### Conclusion
Because our p-value is far below our significance threshold ($\alpha = 0.05$), we reject the null hypothesis. The empirical data strongly supports the alternative hypothesis that weather-related outages face significantly delayed restoration times compared to internal equipment malfunctions. However, since this is an observational study rather than a randomized controlled trial, this result does not absolutely prove direct causation.

# Framing a Prediction Problem
Our objective is framed as a Regression task, specifically predicting the continuous variable OUTAGE.DURATION in minutes. We chose **Root Mean Squared Error (RMSE)** as our primary evaluation metric. It is highly appropriate for this project because RMSE heavily penalizes large missed predictions by squaring residuals. For states level outages underestiamting the outage duration can cause catastrophic consequences; usnig other metrics like MAE treats this error linearly, whereas RMSE appropriately highlights severe modeling failures. 
To ensure the model operates appropriately under real life context, we only train using features known *at or before the exact start of the outage*. Features like `DEMAND.LOSS.MW` and `CUSTOMERS.AFFECTED` were explicitly barred from training, as those figures are calculated and finalized only *after* power is completely restored to the grid.

# Baseline Model
The linear regression baseline model is designed to predict the severity of power outages (OUTAGE.DURATION). The model is a simple regression fit with two features: CAUSE.CATEGORY and ANOMALY.LEVEL. CAUSE.CATEGORY is categorical and is transformed using One-Hot Encoding (ColumnTransformer) to convert string labels into binary vectors. ANOMALY.LEVEL (represents climatic strength) is used as is as it's numerical. On a 20% test split, the result is RMSE of ~7189.3 min.

The model's performance is not as expected, as using only two features leads to underfitting and assuming a simple linear relationship introduces bias, where highly non-linear underlying relationships actually exist.

# Final Model

# Fairness Analysis
