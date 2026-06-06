# Power_Outages_Prediction
by Yuxuan Zhu, Andy Li

## Introduction
This project provides a comprehensive overview, investigation, and analysis of power outages within the United States. Utilizing a dataset compiled by Mukherjee et al. with data sourced from the U.S. Department of Energy, this study explores the circumstances, regional vulnerabilities, and climate conditions surrounding power grid failures. 

Given the substantial economic losses and societal disruptions caused by large-scale grid failures, identifying the specific times and locations of these events is critical for optimizing resource allocation and hardening infrastructure. Consequently, this analysis aims to answer a central question: **When and where are major power outages most likely to occur?**

> **Definition of a Major Outage:** As defined by the Department of Energy, a "major" power outage is an event that exceeds 50,000 customers affected OR results in a 300 MW power loss. 

By isolating these major events, we aim to discover seasonal trends and regional vulnerabilities that can inform future grid-strengthening initiatives and emergency response planning.

The original dataset can be found at the [Purdue University LASCI Data Repository](https://engineering.purdue.edu/LASCI/research-data/outages), containing 1,534 rows and 57 columns. While the 57 columns represent various features of power outages, our study narrows its focus to the 13 most relevant columns outlined below:

| Column | Description |
| :--- | :--- |
| `YEAR` | Year the outage occurred |
| `MONTH` | Month the outage occurred |
| `OUTAGE.START.DATE` | Exact day, month, and year the outage occurred |
| `OUTAGE.START.TIME` | Exact time the outage occurred during the day |
| `U.S._STATE` | Specific U.S. state where the outage occurred |
| `CLIMATE.REGION` | U.S. climate region where the outage occurred (specified by NCEI) |
| `CAUSE.CATEGORY` | Primary reason or category of the event that triggered the outage |
| `CLIMATE.CATEGORY` | Classification of the generalized climate conditions during the outage |
| `DEMAND.LOSS.MW` | Amount of power demand lost during the outage (in Megawatts) |
| `ANOMALY.LEVEL` | The Oceanic Niño Index (ONI) numeric value at the time of the outage |
| `CUSTOMERS.AFFECTED` | Total number of utility customers that lost power during the outage |
| `POPULATION` | Total population of the affected state during the year of the outage |
| `OUTAGE.DURATION` | Total length of time the power outage lasted (in minutes) |

---

## Data Cleaning 
Before diving into the analysis, the raw dataset required systematic cleaning and preprocessing:

* **Structure Extraction:** We loaded the power outage dataset from its source Excel file, stripping away unnecessary metadata rows and units to isolate a clean tabular structure.
* **Feature Selection:** Filtered the dataset down to the 13 features critical to our study goals.
* **Type Conversion & Coercion:** Converted critical target columns (`OUTAGE.DURATION`, `DEMAND.LOSS.MW`, and `ANOMALY.LEVEL`) from text objects to numerical types. This step is essential for quantitative graphing and statistical analysis; non-numeric or corrupted entries were safely coerced into missing values (`NaN`).
* **Feature Engineering:** Combined `OUTAGE.START.DATE` and `OUTAGE.START.TIME` into a single unified `pandas` Timestamp column named `OUTAGE.START`. The redundant original columns were then dropped.

The first few rows of our cleaned dataset are displayed below:

<iframe
  src="assets/cleaned.html"
  width="100%"
  height="600"
  frameborder="0"
></iframe>

---

## Exploratory Data Analysis

### Univariate Analysis
First, we analyzed individual variables to understand their distributions across the country.

**Distribution of Outages by State:**
<iframe src="assets/fig1.html" width="100%" height="450px" frameborder="0"></iframe>

> **Observation:** California suffered from a significantly higher frequency of major power outages compared to all other U.S. states.

**Historical Outage Trends by Year:**
<iframe src="assets/fig2.html" width="100%" height="450px" frameborder="0"></iframe>

> **Observation:** Power outages have generally trended upward over the past two decades, reaching a notable historical peak in 2011.

### Bivariate Analysis
To uncover relationships between variables, we expanded our scope to look at pairs of features.

**Annual Trends Across Different Climate Categories:**
<iframe src="assets/fig4.html" width="100%" height="450px" frameborder="0"></iframe>

> **Observation:** While the overall historical trends mirror one another closely across all environments, regions experiencing "Warm" climate macro-categories appear to experience a slightly lower overall frequency of outages.

**Monthly Vulnerability Mapping:**
<iframe src="assets/fig6.html" width="100%" height="450px" frameborder="0"></iframe>

> **Observation:** At first glance, late summer (September) and mid-winter (December) appear to be the most vulnerable months for power grid failures.

---

## Grouping and Aggregates

To understand regional impacts, we aggregated the data across **climate regions**, calculating total outages, median customers affected, and median duration:

<iframe src="assets/agg1.html" width="100%" height="450px" frameborder="0"></iframe>

* **Regional Inversion:** Interestingly, the region with the highest absolute number of incidents resolves them relatively quickly.
* **Infrastructure Crisis:** Conversely, a separate region experiencing 137 outages suffers from a prolonged median duration of 3,120 minutes. In this severely delayed region, the median number of affected customers climbs to 111,196.5, suggesting widespread infrastructure stress.

Next, we aggregated the data by the **underlying cause** of the outage:

<iframe src="assets/agg2.html" width="100%" height="450px" frameborder="0"></iframe>

* **Environmental Dominance:** Severe weather conditions drive the vast majority of outages, resulting in massive consumer disruption alongside an extended median recovery time of 2,460 minutes.
* **Operational Errors:** In contrast, internal system errors or operational faults occur frequently but are resolved rapidly, maintaining a low median duration of 56 minutes.

---

## Assessment of Missingness

An evaluation of missing data reveals the following breakdown across our target columns:

| Column Name | Missing Values |
| :--- | ---: |
| `YEAR` | 0 |
| `MONTH` | 0 |
| `U.S._STATE` | 0 |
| `CLIMATE.REGION` | 5 |
| `CAUSE.CATEGORY` | 0 |
| `CLIMATE.CATEGORY` | 0 |
| `DEMAND.LOSS.MW` | 672 |
| `ANOMALY.LEVEL` | 0 |
| `CUSTOMERS.AFFECTED` | 420 |
| `POPULATION` | 0 |
| `OUTAGE.DURATION` | 0 |
| `OUTAGE.START` | 0 |

### NMAR (Not Missing At Random) Analysis
The two columns most likely to be **NMAR (Not Missing At Random)** are `DEMAND.LOSS.MW` and `CUSTOMERS.AFFECTED`. 

The data originates from the Department of Energy's OE-417 Schedule 1 emergency forms, which utilities are legally obligated to file during major incidents. During catastrophic grid failures, conditions are often too chaotic to record accurate telemetry. Because the data values are extreme, they are left blank simply because they are unmeasurable at that time.

Furthermore, utility companies face strategic under-reporting pressures. Since a "Major Outage" triggers stricter regulatory oversight if it crosses the threshold of 50,000+ customers or 300+ MW for over 30 minutes, companies may selectively omit borderline values to avoid compliance audits.

### Missingness Dependency (Permutation Testing)
We established permutation tests to determine whether the missingness of the `CUSTOMERS.AFFECTED` column structurally depends on other features.

#### Test 1: Dependency on `CLIMATE.CATEGORY` (Dependent)
* **Null Hypothesis ($H_0$):** The missingness of customers affected is independent of the macro climate category.
* **Alternative Hypothesis ($H_A$):** The missingness of customers affected depends on the macro climate category.
* **Test Statistic:** Total Variation Distance (TVD) between the distribution of climate categories when customers affected is missing versus when it is present.
* **Observed TVD:** 0.142
* **p-value:** `< 0.01`
* **Conclusion:** **Reject the Null Hypothesis**. The missingness of customer records exhibits a statistically significant dependency on climate conditions.

<iframe src="assets/fig7.html" width="100%" height="400px" frameborder="0"></iframe>

#### Test 2: Dependency on `YEAR` (Independent)
* **Null Hypothesis ($H_0$):** The missingness of customers affected is independent of the year the outage took place.
* **Alternative Hypothesis ($H_A$):** The missingness of customers affected depends on the year the outage took place.
* **Test Statistic:** Absolute difference in mean year between the missing and non-missing groups.
* **p-value:** `0.54`
* **Conclusion:** **Fail to Reject the Null Hypothesis**. The likelihood of a customer count being missing shows no statistically verifiable link to the calendar year of the incident.

<iframe src="assets/fig8.html" width="100%" height="400px" frameborder="0"></iframe>

---

## Hypothesis Testing
To assess whether different outage triggers present structurally unique severity footprints, we investigated the restoration durations between environmental disasters and systemic grid failures.

* **Null Hypothesis ($H_0$):** The distribution of outage durations for severe weather events and system malfunctions is identical. Any observed difference in medians is entirely due to random sampling chance.
* **Alternative Hypothesis ($H_A$):** The distribution of outage durations for severe weather events has a higher median than that of system malfunctions, reflecting physical barriers to infrastructure restoration.
* **Test Statistic:** Difference in Sample Medians ($\text{Median}_{\text{Weather}} - \text{Median}_{\text{Malfunction}}$).
* **Significance Level ($\alpha$):** 0.05.

### Test Results
Following 1,000 random reshuffling permutations, our simulation yielded a **p-value of < 0.001**. 

<iframe src="assets/fig9.html" width="100%" height="400px" frameborder="0"></iframe>

### Conclusion
Because our p-value is far below our significance threshold ($\alpha = 0.05$), we **reject the null hypothesis**. The empirical data strongly supports the alternative hypothesis that weather-related outages face significantly longer restoration times compared to internal equipment malfunctions. 

> *Note: Since this is an observational study rather than a randomized controlled trial, this result demonstrates a robust association but does not mathematically prove direct causation.*

---

## Framing a Prediction Problem

### Task & Metric
Our objective is framed as a **Regression task** to predict the continuous variable `OUTAGE.DURATION` (in minutes). We selected **Root Mean Squared Error (RMSE)** as our primary evaluation metric. 

RMSE is highly appropriate for this application because it squares residuals, heavily penalizing large missed predictions. In state-level grid management, underestimating outage durations can cause catastrophic emergency-response bottlenecks, making RMSE a safer optimization metric than a linear alternative like MAE.

### Feature Constraints
To preserve real-world deployment validity, the model is strictly trained using features **known at or before the exact start of the outage**. 

Features such as `DEMAND.LOSS.MW` and `CUSTOMERS.AFFECTED` were explicitly barred from training, as those figures are metrics that are finalized *after* power has been fully restored to the grid.

---

## Baseline Model

Our baseline model utilized a **Linear Regression** framework predicting `OUTAGE.DURATION` based on two initial features:
1. `CAUSE.CATEGORY` (Categorical): Transformed using One-Hot Encoding via a `ColumnTransformer` to convert string labels into binary vectors.
2. `ANOMALY.LEVEL` (Quantitative): Kept raw as a numerical scalar representing localized climatic anomaly strength.

### Performance Evaluation
Evaluated on a 20% test split, the baseline model yielded an **RMSE of ~7,189.3 minutes**.

### Limitations
The model performed poorly due to structural underfitting. Relying on only two features introduces an extreme inductive bias, attempting to fit a simple linear plane onto data governed by highly complex, non-linear real-world interactions.

---

## Final Model

### Engineered Features
To capture the intricate dynamics of grid failures, we engineered three additional features for our final model pipeline:

* `POPULATION` (Quantitative): Outages in densely populated areas impact larger, high-complexity networks. This can either delay restoration due to scale or accelerate it due to urban prioritization. Because population data is heavily right-skewed, we applied a `QuantileTransformer` to normalize the distribution and prevent extreme population centers from dominating the model's loss function.
* `CLIMATE.REGION` (Nominal): Geographic and regional climates dictate a local grid's physical resilience to specific weather hazards (e.g., a southern region unaccustomed to severe winter freezes might respond slower to ice storms).
* `START_HOUR` (Quantitative): Extracted from the timestamp, the hour an outage begins directly impacts restoration logistics. Navigating repair crews during peak traffic hours or pitch-black night shifts takes significantly longer than standard business hours. This feature was normalized using `StandardScaler`.

### Algorithm & Hyperparameter Tuning
We selected a **Random Forest Regressor** as our final modeling algorithm to effectively capture non-linear relationships and intricate feature interactions. 

We deployed a 5-fold cross-validation scheme via `GridSearchCV` to optimize tree depth, splitting thresholds, and estimator counts, evaluating across negative RMSE limits. 

The optimal hyperparameter configuration selected was:
* `max_depth`: 3
* `min_samples_split`: 2
* `n_estimators`: 150

### Final Performance
The Final Model successfully reduced prediction error by approximately **255.34 minutes (~4.25 hours)** over the baseline.

While this error reduction is a clear step forward, our final absolute RMSE remains high (~4.8 days). The fact that `GridSearchCV` selected a highly restricted `max_depth` of 3 reveals an important domain truth: power outage durations contain extreme, chaotic outliers driven by unpredictable external events that cannot be easily forecasted by broad demographic variables alone. 

However, by shifting to an ensemble Random Forest framework, our Final Model successfully integrated non-linear variables and noisy features into a significantly more stable model that generalizes much better to unseen data without overfitting.

---

## Fairness Analysis

To confirm that our final model performs equitably across different demographic scales, we evaluated its consistency between states with high versus low populations.

* **Group X (Low Population States):** Outages occurring in states with a population *below* the national median population.
* **Group Y (High Population States):** Outages occurring in states with a population *above* the national median population.
* **Evaluation Metric:** Root Mean Squared Error (RMSE).
* **Test Statistic:** $RMSE_{\text{Low Pop}} - RMSE_{\text{High Pop}}$

### Hypotheses
* **Null Hypothesis ($H_0$):** The model is fair. The RMSE for low population states and high population states is roughly equal, and any observed difference is purely due to random split chance.
* **Alternative Hypothesis ($H_A$):** The model is unfair. The RMSE for low population states is significantly different from the RMSE for high population states, indicating a systemic demographic bias.
* **Significance Level ($\alpha$):** 0.05.

### Results & Evaluation
After running a permutation test with 1,000 demographic label swaps, we calculated a final **p-value of 0.38**. 

Because $0.38 > 0.05$, we fail to reject the null hypothesis. We conclude that there is no statistically significant evidence of a fairness disparity in our model's accuracy between low-population and high-population areas; the model remains structurally equitable across both groups.
