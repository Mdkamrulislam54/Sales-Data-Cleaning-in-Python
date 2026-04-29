# 🧹 Sales Data Cleaning — Python & Pandas

A complete data wrangling project on a real-world food-service sales dataset using Python and Pandas in Google Colab.

---

## 📊 Dataset

**File:** `Sales-Data-Analysis.xlsx`  
**Period:** November 7 – December 29, 2022  
**Business:** Food-service chain across 5 European cities (London, Madrid, Lisbon, Berlin, Paris)

| Metric | Value |
|--------|-------|
| Raw rows | 262 |
| Clean rows | **254** |
| Columns | 9 |
| Date range | 53 distinct days |

---

## 🔧 What Was Cleaned

| # | Issue | Fix |
|---|-------|-----|
| 1 | Blank column (`Unnamed: 0` — 100% null) | `df.drop(columns='Unnamed: 0')` |
| 2 | Real column headers stored in row 0 | `df.columns = df.loc[0]` → drop row 0 |
| 3 | Manager names with irregular whitespace (14 apparent → 5 real) | `.str.strip().str.replace(r'\s+', ' ', regex=True)` |
| 4 | 4 exact duplicate rows | `df.drop_duplicates()` |
| 5 | 3 rows with outlier prices (data-entry errors ~10×) | Manual inspection + `df.drop([65, 66, 69])` |
| 6 | All columns typed as `object` | Cast `Order ID → int64`, `Price → float64`, `Quantity → int64`, `Date → datetime64` |

---

## 📁 Project Structure

```
sales-data-cleaning/
│
├── Data_Cleaning.ipynb          # Main notebook (Google Colab)
├── Sales-Data-Analysis.xlsx     # Raw dataset
├── requirements.txt             # Python dependencies
└── README.md
```

---

## 🚀 Getting Started

### Run in Google Colab
[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/)

1. Upload `Sales-Data-Analysis.xlsx` to your Colab session
2. Open `Data_Cleaning.ipynb`
3. Run all cells (`Runtime → Run all`)

### Run Locally

```bash
# Clone the repo
git clone https://github.com/your-username/sales-data-cleaning.git
cd sales-data-cleaning

# Install dependencies
pip install -r requirements.txt

# Launch Jupyter
jupyter notebook Data_Cleaning.ipynb
```

---

## 🛠️ Libraries Used

| Library | Version | Purpose |
|---------|---------|---------|
| `pandas` | ≥ 1.5 | Data loading, cleaning, transformation |
| `matplotlib` | ≥ 3.5 | Visualisation |
| `seaborn` | ≥ 0.12 | Statistical plots |
| `openpyxl` | ≥ 3.0 | Reading `.xlsx` files |

---

## 📋 Final Data Schema

```
Order ID        int64          — Unique order identifier
Date            datetime64[ns] — Order date
Product         object         — 5 categories: Fries, Burgers, Beverages, Chicken Sandwiches, Sides & Other
Price           float64        — Item price in USD
Quantity        int64          — Units sold
Purchase Type   object         — Online / In-store / Drive-thru
Payment Method  object         — Gift Card / Credit Card / Cash
Manager         object         — 5 managers across 5 cities
City            object         — London, Madrid, Lisbon, Berlin, Paris
```

---

## 💡 Key Learnings

- **Structural issues** (shifted headers, blank columns) must be resolved before any other cleaning
- **Whitespace normalisation** is critical for text columns — same person can appear as 14 unique values
- **Duplicate detection** should be done in two passes: exact row duplicates, then domain-key duplicates (e.g., same Order ID with different prices)
- **Type conversion order matters** — float → round → int avoids data loss

---

## 🔗 Connect

Feel free to fork, star ⭐, or open an issue if you find improvements!

---

*Cleaned with ❤️ using Python 3 · Pandas · Google Colab*
