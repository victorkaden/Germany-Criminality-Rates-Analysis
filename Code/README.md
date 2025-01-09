# Germany Criminality Rates Project - Code Overview

This document provides an overview of the code used to extract, analyze, and visualize data for the **Germany Criminality Rates** project.

---

## 1. SQL Queries

### Data Extraction
The criminality data was stored in a database, and I used SQL to extract relevant information for analysis. Below is a sample query used to retrieve data:

```sql
SELECT 
    crime_type, 
    COUNT(*) AS crime_count, 
    year
FROM 
    germany_criminality
GROUP BY 
    crime_type, year
ORDER BY 
    year, crime_count DESC;
```

This query extracts the count of crimes by type and year, allowing me to analyze trends over time.

### Filtering Specific Crimes
To focus on the top 10 crimes, we used the following query:

```sql
WITH ranked_crimes AS (
    SELECT 
        crime_type, 
        COUNT(*) AS total_crimes
    FROM 
        germany_criminality
    GROUP BY 
        crime_type
    ORDER BY 
        total_crimes DESC
    LIMIT 10
)
SELECT 
    g.*
FROM 
    germany_criminality g
JOIN 
    ranked_crimes r
ON 
    g.crime_type = r.crime_type;
```

This query ranks crimes by their total occurrences and filters the dataset to only include the top 10 crime types.

---

## 2. Python Scripts

### Data Cleaning
I used Python's `pandas` library to clean and preprocess the data. Below is an example of how missing values were handled:

```python
import pandas as pd

# Load the dataset
df = pd.read_csv('germany_criminality.csv')

# Check for missing values
missing_values = df.isnull().sum()
print("Missing Values:\n", missing_values)

# Fill missing values with the median for numerical columns
df.fillna(df.median(), inplace=True)
```

### Data Aggregation
To summarize crime data by year and type:

```python
# Group data by year and crime type
crime_summary = df.groupby(['year', 'crime_type']).size().reset_index(name='crime_count')

print(crime_summary.head())
```

### Data Visualization
I used `matplotlib` and `seaborn` for creating visualizations. Below are the scripts for the visualizations:

#### Crime Rate Trend in Germany (2010–2023)

```python
import matplotlib.pyplot as plt

# Group data by year
crime_trend = df.groupby('year')['crime_count'].sum()

# Plot the trend
plt.figure(figsize=(10, 6))
plt.plot(crime_trend.index, crime_trend.values, marker='o', color='blue')
plt.title('Crime Rate Trend in Germany (2010–2023)')
plt.xlabel('Year')
plt.ylabel('Crime Rate (in millions)')
plt.grid(True)
plt.show()
```

#### Top 10 Crimes in Germany – 2023

```python
# Filter data for the year 2023
data_2023 = df[df['year'] == 2023]

# Group by crime type
top_crimes = data_2023['crime_type'].value_counts().head(10)

# Plot the top 10 crimes
plt.figure(figsize=(10, 6))
top_crimes.plot(kind='barh', color='skyblue')
plt.title('Top 10 Crimes in Germany – 2023')
plt.xlabel('Number of Cases (Millions)')
plt.ylabel('Crime Type')
plt.show()
```

#### Crime Rate Distribution

```python
# Calculate crime rate distribution by city
city_distribution = df['city'].value_counts(normalize=True) * 100

# Plot the distribution
plt.figure(figsize=(8, 8))
plt.pie(city_distribution, labels=city_distribution.index, autopct='%1.1f%%', startangle=140)
plt.title('Crime Rate Distribution')
plt.show()
```

---

This document provides a high-level overview of the code and tools used in the **Germany Criminality Rates** project. For detailed scripts and queries, please refer to the actual code files in the repository.

