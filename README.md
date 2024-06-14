# Python-and-R-code
Projects


# MSQM Health Analytics - Team 2

### Author:
- Kunal Shukla (ks765)


## Retail Data Analysis

The objective of this assignment is to introduce the pandas DataFrame and some of its associated methods that can help with data wrangling. We focus on a Retail Data use case involving historical sales data for 45 stores located in different regions, each containing several departments. Our primary analytical question is: **For each type of store, what is the average weekly sales after excluding holidays?**

### Datasets:
1. **Sales**: Weekly sales by department for each store.
2. **Stores**: Features of each store including 'Type' (indicates the type of store) and 'Size' (indicates the number of weekly visitors).

The dataset for this project can be found [here](https://www.kaggle.com/manjeetsingh/retaildataset?select=sales+data-set.csv).

## Tasks Overview

### Task 0: Initial Data Formatting

#### 0a) Load Data Frames
```python
import pandas as pd
import numpy as np

# Read in the sales csv as a pandas data frame
sales = pd.read_csv("sales.csv")

# Read in the stores csv as a pandas data frame
stores = pd.read_csv("stores.csv")
```

#### 0b) Examine the Stores Data
```python
# Display the first few rows of the stores data
print(stores.head())

# Display the last few rows of the stores data
print(stores.tail())

# Display the count of unique values in the stores data
print(stores.value_counts())

# Get a concise summary of the stores data
print(stores.info())
```

- **Observations**:
  - The stores dataset lists out the Store number, the Type of store, and the Size.
  - There are 45 unique columns for stores 1 through 45.
  - Each column has 45 entries without null values.

### Task 1: Modifying a Column

#### 1a) Convert 'Date' Column to Datetime
```python
# Convert the 'Date' column with the specified date format to datetime format
sales.loc[:, 'Date'] = pd.to_datetime(sales['Date'], format='%d/%m/%Y', errors='coerce')
```

#### 1b) Create 'Store_Size' Column
```python
# Designate a new column based on values in Size column
stores.loc[:, 'Store_Size'] = np.where(stores['Size'] > 100000, "Large", "Small")
print(stores.head())
```

#### 1c) Modify 'IsHoliday' Column
```python
# Assign True/False values as integers, which converts boolean values to 0/1
sales['IsHoliday'] = sales['IsHoliday'].astype(int)
```

### Task 2: Filtering the Data Frame

#### 2a) Filter Out Holiday Observations
```python
# Filter out the observations occurring on a holiday
sales_on_holiday = sales.query('IsHoliday == False')
print(sales_on_holiday.head())
```

#### 2b) Rewrite Using .loc[]
```python
# Rewrite the code from 2a using .loc[] and save it to a new data frame
sales_2b = sales.loc[sales['IsHoliday'] == False].copy()
print(sales_2b.head())
```

### Task 3: Merge Stores and Sales Data Frames

#### 3a) Merge Data Frames
```python
# Left merge, based on Store column value
sales_stores = stores.merge(sales_2b, how='inner', on='Store')
print(sales_stores.head())
```

#### 3b) Implications of Join Type
- **Left Join**: Adds the data from the second dataframe, keeping the unique columns of the right designated dataframe.
- **Implication**: No difference in output for each type of join, as there are no missing data values.

### Task 4: Summarize the Data Frame

#### 4a) Calculate Weekly Sales
```python
# Calculate the weekly sales by grouping the data by Store, Date and Type, then summing the Weekly_Sales
weekly_store_sales = (sales_stores
                      .groupby(['Store', 'Date', 'Type'])['Weekly_Sales']
                      .sum()
                      .reset_index())
print(weekly_store_sales.head())
```

#### 4b) Calculate Sales Statistics by Store Type
```python
# Calculate the mean, median, and standard deviation of the weekly sales for different store types
store_type_sales_stats = (weekly_store_sales
                          .groupby('Type')
                          .agg({'Weekly_Sales': ['mean', 'median', 'std']})
                          .reset_index())

# Print the calculated statistics for each store type
print(store_type_sales_stats)
```

- **Observations**:
  - **Type A** stores demonstrate larger sales by measures of mean and median, and a larger standard deviation, compared with Type B and C stores.

### Task 5: Method Chaining

#### Complete Tasks 2a, 3a, and 4a in a Single Code Chunk
```python
final = (
    sales.query('IsHoliday == False')
         .merge(stores, how='left', on='Store')
         .groupby(['Store', 'Date', 'Type'])['Weekly_Sales']
         .sum()
         .reset_index()
)
print(final.head())
```

This README provides a concise overview of our approach to analyzing retail data using pandas DataFrame methods, focusing on data wrangling, filtering, merging, and summarizing operations.
