# Introduction

# Data Cleaning and Exploratory Data Analysis
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



# Assessment of Missingness

# Hypothesis Testing

# Framing a Prediction Problem

# Baseline Model

# Final Model

# Fairness Analysis
