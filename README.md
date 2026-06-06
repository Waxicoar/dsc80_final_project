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

# Hypothesis Testing

# Framing a Prediction Problem

# Baseline Model

# Final Model

# Fairness Analysis
