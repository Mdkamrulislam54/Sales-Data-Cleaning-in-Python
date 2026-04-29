🧹 Sales Data Cleaning — Python & Pandas

A complete end-to-end data wrangling project on a real-world food-service sales dataset using Python and Pandas in Google Colab.

📊 Dataset

File: Sales-Data-Analysis.xlsxPeriod: November 7 – December 29, 2022Business: Food-service chain across 5 European cities (London, Madrid, Lisbon, Berlin, Paris)

Raw rows: 262

Clean rows: 254

Columns: 9

Date range: 53 distinct days

🔧 Cleaning Steps

Step 1 — Import Libraries

pandas for data manipulation

matplotlib and seaborn for visualisation

import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

Step 2 — Load Dataset & Inspect

Read Excel file

Initial inspection: 262 rows × 10 columns, all object dtype

First column (Unnamed: 0) is entirely null

data = pd.read_excel('Sales-Data-Analysis.xlsx')
data.info()

Step 3 — Drop Blank Column

Unnamed: 0 dropped → 9 columns remain

data.drop(columns='Unnamed: 0', inplace=True)

Step 4 — Fix Column Headers

Real headers stored in row 0

Promote row 0 as column names, then drop it

data.columns = data.loc[0]
data.drop(0, inplace=True)

Step 5 — Normalise Manager Column

14 apparent values due to irregular whitespace → reduced to 5 unique managers

data['Manager'] = data['Manager'].str.strip().str.replace(r'\s+', ' ', regex=True)

Step 6 — Remove Exact Duplicate Rows

4 fully identical rows removed

data.drop_duplicates(inplace=True)

Step 7 — Detect & Drop Outlier Prices

3 rows with ~10× inflated prices dropped after manual inspection

data.drop([65, 66, 69], inplace=True)

Step 8 — Fix Column Data Types

Cast columns to correct types

data['Order ID'] = data['Order ID'].astype(int)
data['Price'] = data['Price'].astype(float)
data['Quantity'] = data['Quantity'].astype(float).round().astype(int)

Step 9 — Parse Date Column

Convert Date to datetime64

data['Date'] = pd.to_datetime(data['Date'])

📋 Cleaning Summary

Step

Action

Before

After

Key Method

1

Import libraries

—

—

import pandas

2

Load dataset

—

262 rows × 10 cols

pd.read_excel()

3

Drop blank column

10 cols

9 cols

df.drop()

4

Fix column headers

Unnamed: 1 … 9

Proper names

df.columns = df.loc[0]

5

Normalise Manager

14 unique

5 unique

.str.strip().replace()

6

Remove duplicates

261 rows

257 rows

df.drop_duplicates()

7

Remove outlier rows

257 rows

254 rows

df.drop([65,66,69])

8

Fix data types

all object

int/float/object

.astype() / .round()

9

Parse dates

object

datetime64[ns]

pd.to_datetime()

📁 Project Files

📊 Sales Dataset: Sales-Data-Analysis.xlsx

📝 Data Cleaning Notebook: Data_Cleaning.ipynb

🚀 Getting Started

Run in Google Colab



Upload Sales-Data-Analysis.xlsx to your Colab session

Open Data_Cleaning.ipynb

Run all cells (Runtime → Run all)

Run Locally

# Clone the repo
git clone https://github.com/your-username/sales-data-cleaning.git
cd sales-data-cleaning

# Install dependencies
pip install -r requirements.txt

# Launch Jupyter
jupyter notebook Data_Cleaning.ipynb

🛠️ Libraries Used

Library

Version

Purpose

pandas

≥ 1.5

Data loading, cleaning, transformation

matplotlib

≥ 3.5

Visualisation

seaborn

≥ 0.12

Statistical plots

openpyxl

≥ 3.0

Reading .xlsx files

📋 Final Data Schema

Order ID        int64          — Unique order identifier
Date            datetime64[ns] — Order date
Product         object         — 5 categories: Fries, Burgers, Beverages, Chicken Sandwiches, Sides & Other
Price           float64        — Item price in USD
Quantity        int64          — Units sold
Purchase Type   object         — Online / In-store / Drive-thru
Payment Method  object         — Gift Card / Credit Card / Cash
Manager         object         — 5 managers across 5 cities
City            object         — London, Madrid, Lisbon, Berlin, Paris

💡 Key Learnings

Structural issues (shifted headers, blank columns) must be resolved before any other cleaning

Whitespace normalisation is critical for text columns — same person can appear as multiple unique values

Duplicate detection should be done in two passes: exact row duplicates, then domain-key duplicates

Type conversion order matters — float → round → int avoids data loss

Cleaned with ❤️ using Python 3 · Pandas · Google Colab
