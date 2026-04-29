# 🧹 Sales Data Cleaning in Python

<div align="center">

![Python](https://img.shields.io/badge/Python-3.x-3776AB?style=for-the-badge&logo=python&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-2.0-150458?style=for-the-badge&logo=pandas&logoColor=white)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626?style=for-the-badge&logo=jupyter&logoColor=white)
![Google Colab](https://img.shields.io/badge/Google_Colab-Ready-F9AB00?style=for-the-badge&logo=googlecolab&logoColor=white)
![Excel](https://img.shields.io/badge/Excel-Dataset-217346?style=for-the-badge&logo=microsoftexcel&logoColor=white)

<br/>

> **A production-style data cleaning pipeline for a real-world food-service sales dataset.**
> Covers structural fixes, type casting, whitespace normalization, duplicate removal, and outlier detection — all in Python & Pandas.

<br/>

| 📓 Notebook | 📊 Dataset |
|:-----------:|:----------:|
| [![View Notebook](https://img.shields.io/badge/Open-Data__Cleaning.ipynb-blue?style=for-the-badge&logo=jupyter)](https://github.com/Mdkamrulislam54/Sales-Data-Cleaning-in-Python/blob/e18cf7b76cb2906e0d7810ad7aec312a7d33059e/Data_Cleaning.ipynb) | [![View Dataset](https://img.shields.io/badge/Open-Sales--Data--Analysis.xlsx-217346?style=for-the-badge&logo=microsoftexcel)](https://github.com/Mdkamrulislam54/Sales-Data-Cleaning-in-Python/blob/6ade69dbc914e449a3710b12add806ef10245afe/Sales-Data-Analysis.xlsx) |

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Mdkamrulislam54/Sales-Data-Cleaning-in-Python/blob/e18cf7b76cb2906e0d7810ad7aec312a7d33059e/Data_Cleaning.ipynb)

</div>

---

## 📁 Project Structure

```
Sales-Data-Cleaning-in-Python/
│
├── 📓 Data_Cleaning.ipynb          ← Full cleaning notebook (Google Colab)
├── 📊 Sales-Data-Analysis.xlsx     ← Raw dataset (262 rows × 10 cols)
├── requirements.txt                ← Python dependencies
└── README.md
```

---

## 📦 Dataset at a Glance

```
File     : Sales-Data-Analysis.xlsx
Business : Food-service chain — 5 European cities
Period   : November 7, 2022 → December 29, 2022
Raw      : 262 rows  ×  10 columns
Clean    : 254 rows  ×   9 columns
```

| Column | Raw dtype | Clean dtype | Notes |
|--------|-----------|-------------|-------|
| `Order ID` | `object` | `int64` | Cast to integer |
| `Date` | `object` | `datetime64[ns]` | Parsed with `pd.to_datetime()` |
| `Product` | `object` | `object` | 5 categories — already clean |
| `Price` | `object` | `float64` | Cast to float |
| `Quantity` | `object` | `int64` | `float → round → int` |
| `Purchase Type` | `object` | `object` | Online / In-store / Drive-thru |
| `Payment Method` | `object` | `object` | Gift Card / Credit Card / Cash |
| `Manager` | `object` | `object` | Whitespace stripped & normalised |
| `City` | `object` | `object` | 5 cities — already clean |

---

## 🚀 Quickstart

```bash
# 1. Clone the repo
git clone https://github.com/Mdkamrulislam54/Sales-Data-Cleaning-in-Python.git
cd Sales-Data-Cleaning-in-Python

# 2. Install dependencies
pip install -r requirements.txt

# 3. Launch notebook
jupyter notebook Data_Cleaning.ipynb
```

> Or open instantly in the cloud → [![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Mdkamrulislam54/Sales-Data-Cleaning-in-Python/blob/e18cf7b76cb2906e0d7810ad7aec312a7d33059e/Data_Cleaning.ipynb)

---

## 🔧 Cleaning Pipeline — Full Code Walkthrough

### Step 1 — Import Libraries

```python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns
```

---

### Step 2 — Load Dataset & Inspect

```python
data = pd.read_excel('Sales-Data-Analysis.xlsx')
data.info()
```

```
# OUTPUT
RangeIndex: 262 entries, 0 to 261
Data columns (total 10 columns):
 Unnamed: 0       0 non-null    float64   ← 100% null — must drop
 Unnamed: 1       262 non-null  object
 Unnamed: 2       262 non-null  object
 ...
dtypes: float64(1), object(9)
```

> ⚠️ **Issues detected:** Blank column, headers in row 0, all columns typed as `object`.

---

### Step 3 — Drop the Blank Column

```python
# Unnamed: 0 has 0 non-null values — completely empty
data['Unnamed: 0'].isna().all()   # → True

data.drop(columns='Unnamed: 0', inplace=True)
data.shape   # → (262, 9)
```

---

### Step 4 — Promote Row 0 as Column Headers

```python
# Before — columns are unnamed placeholders
print(data.columns.tolist())
# → ['Unnamed: 1', 'Unnamed: 2', ..., 'Unnamed: 9']

# Row 0 holds the actual column names — promote it
data.columns = data.loc[0]
data.drop(0, inplace=True)

# After — proper column names
print(data.columns.tolist())
# → ['Order ID', 'Date', 'Product', 'Price', 'Quantity',
#    'Purchase Type', 'Payment Method', 'Manager', 'City']
```

---

### Step 5 — Normalise the Manager Column

```python
# Problem — same manager name with irregular whitespace
data['Manager'].unique()
# → ['Tom      Jackson', '       Pablo Perez', 'Joao    Silva',
#    'Pablo   Perez', 'Pablo  Perez', 'Remy    Monet', ...]
# → 14 apparent unique values — should be only 5!

# Fix — strip edges, collapse internal spaces
data['Manager'] = (
    data['Manager']
    .str.strip()                            # remove leading/trailing whitespace
    .str.replace(r'\s+', ' ', regex=True)   # collapse internal spaces to one
)

data['Manager'].nunique()   # → 5 ✓
data['Manager'].unique()
# → ['Tom Jackson', 'Pablo Perez', 'Joao Silva', 'Walter Muller', 'Remy Monet']
```

---

### Step 6 — Remove Exact Duplicate Rows

```python
# Detect — rows that are identical across all 9 columns
data[data.duplicated()]
# → 4 rows found (same Order ID + all fields match)

# Remove
data.drop_duplicates(inplace=True)
data.shape   # → (257, 9)
```

---

### Step 7 — Detect & Drop Outlier-Price Rows

```python
# After exact-dup removal, some Order IDs still appear twice
data[data['Order ID'].duplicated()][['Order ID', 'Product', 'Price']]
# → Order 10483 : Beverages     — $2.95  vs  $33.92
# → Order 10484 : Sides & Other — $4.99  vs  $44.99
# → Order 10485 : Burgers       — $12.99 vs $112.99

# Confirm — inspect one pair
data[data['Order ID'] == '10483'][['Order ID', 'Product', 'Price']]
# → row 64: Beverages  $2.95   ← correct standard price
# → row 65: Beverages  $33.92  ← ~10× inflated, data-entry error

# Drop the 3 erroneous rows
data.drop([65, 66, 69], inplace=True)
data.shape   # → (254, 9)
```

| Order ID | Product | Correct Price | ❌ Bad Price | Row Dropped |
|----------|---------|:-------------:|:-----------:|:-----------:|
| 10483 | Beverages | $2.95 | **$33.92** | Row 65 |
| 10484 | Sides & Other | $4.99 | **$44.99** | Row 66 |
| 10485 | Burgers | $12.99 | **$112.99** | Row 69 |

---

### Step 8 — Fix Column Data Types

```python
# Before — all numeric columns are still object dtype
data.dtypes
# → Order ID          object
# → Price             object
# → Quantity          object

# Quantity: 3-step conversion (has decimal noise e.g. 1.0, 2.0)
data['Quantity'] = data['Quantity'].astype(float).round().astype(int)

# Order ID: direct cast to integer
data['Order ID'] = data['Order ID'].astype(int)

# Price: direct cast to float
data['Price'] = data['Price'].astype(float)

# After
data[['Order ID', 'Price', 'Quantity']].dtypes
# → Order ID    int64
# → Price       float64
# → Quantity    int64
```

---

### Step 9 — Parse the Date Column

```python
# Before — Date is stored as plain text
data['Date'].dtype   # → dtype('O')

# Convert to proper datetime
data['Date'] = pd.to_datetime(data['Date'])

# After
data['Date'].dtype   # → datetime64[ns] ✓

# Final info — clean dataset
data.info()
# → 254 entries, 0 to 260
# → Order ID        254 non-null  int64
# → Date            254 non-null  datetime64[ns]
# → Product         254 non-null  object
# → Price           254 non-null  float64
# → Quantity        254 non-null  int64
# → Purchase Type   254 non-null  object
# → Payment Method  254 non-null  object
# → Manager         254 non-null  object
# → City            254 non-null  object
```

---

## ✅ Cleaning Summary

| Step | Action | Before | After | Method |
|:----:|--------|--------|-------|--------|
| 1 | Import libraries | — | — | `import pandas as pd` |
| 2 | Load dataset | — | 262 × 10 | `pd.read_excel()` |
| 3 | Drop blank column | 10 cols | 9 cols | `df.drop(columns=...)` |
| 4 | Fix column headers | `Unnamed: 1…9` | Proper names | `df.columns = df.loc[0]` |
| 5 | Normalise Manager | 14 unique | 5 unique | `.str.strip().str.replace()` |
| 6 | Remove exact dupes | 261 rows | 257 rows | `df.drop_duplicates()` |
| 7 | Remove outlier rows | 257 rows | 254 rows | `df.drop([65, 66, 69])` |
| 8 | Fix dtypes | all `object` | `int / float` | `.astype()` |
| 9 | Parse dates | `object` | `datetime64[ns]` | `pd.to_datetime()` |

---

## 🛠️ Requirements

```txt
pandas>=1.5.0
matplotlib>=3.5.0
seaborn>=0.12.0
openpyxl>=3.0.0
```

```bash
pip install -r requirements.txt
```

---

## 💡 Key Learnings

```
1. Fix structural issues FIRST (blank cols, shifted headers) before any cleaning
2. Whitespace is silent — same name appeared as 14 unique values in Manager column
3. Two-pass duplicate detection:
      Pass 1 → exact row duplicates   (drop_duplicates)
      Pass 2 → same key, diff value   (manual outlier inspection)
4. Type conversion order matters:
      object → float → round() → int   (not object → int directly)
5. Always parse dates — string dates break groupby, resample, and plotting
```

---

## 🔗 Links

| Resource | URL |
|----------|-----|
| 📓 Notebook | [Data_Cleaning.ipynb](https://github.com/Mdkamrulislam54/Sales-Data-Cleaning-in-Python/blob/e18cf7b76cb2906e0d7810ad7aec312a7d33059e/Data_Cleaning.ipynb) |
| 📊 Dataset | [Sales-Data-Analysis.xlsx](https://github.com/Mdkamrulislam54/Sales-Data-Cleaning-in-Python/blob/6ade69dbc914e449a3710b12add806ef10245afe/Sales-Data-Analysis.xlsx) |
| ☁️ Open in Colab | [Launch Notebook](https://colab.research.google.com/github/Mdkamrulislam54/Sales-Data-Cleaning-in-Python/blob/e18cf7b76cb2906e0d7810ad7aec312a7d33059e/Data_Cleaning.ipynb) |

---

<div align="center">
  <sub>Built with ❤️ using Python 3 · Pandas · Google Colab</sub>
</div>
