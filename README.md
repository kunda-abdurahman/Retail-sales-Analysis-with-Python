# Kakuma Fresh Mart: Retail Sales Analysis with Python & Pandas

> An end-to-end data analysis project: exploring, cleaning and analysing a messy multi-branch retail sales dataset to uncover where revenue comes from, how customers behave, and when the business sells most.

![Python](https://img.shields.io/badge/Python-3.x-blue)
![Pandas](https://img.shields.io/badge/Pandas-Data%20Analysis-150458)
![Jupyter](https://img.shields.io/badge/Jupyter-Notebook-F37626)
![Status](https://img.shields.io/badge/Status-Complete-success)

---

## Table of Contents

1. [Executive Summary](#executive-summary)
2. [Business Problem](#business-problem)
3. [Dataset](#dataset)
4. [Methodology](#methodology)
5. [Key Findings](#key-findings)
6. [Recommendations](#recommendations)
7. [Limitations & Next Steps](#limitations--next-steps)
8. [Skills Demonstrated](#skills-demonstrated)
9. [Repository Structure](#repository-structure)
10. [How to Run](#how-to-run)
11. [Author](#author)

---

## Executive Summary

Kakuma Fresh Mart is a three-branch retail business (**Kakuma Town, Lodwar and Kitale**). This project takes its raw sales export, which contained duplicates, inconsistent labels, mixed date formats, missing values and impossible quantities, and turns it into clear, decision-ready insights.

After cleaning roughly 2,500 raw transaction lines, the analysis shows that sales are healthy at every branch, but the business is **concentrated in a few products and in the weekend**:

- **Kakuma Town generates about 40% of revenue**, driven by sales *volume* rather than higher spending per customer.
- **Five of 19 products bring in about 54% of revenue**, and three categories (grains, cooking and dairy) account for roughly 72%.
- **Loyalty (returning) customers do not spend more per line** than new customers.
- **Weekend days earn about 41% more than weekdays** (KES 57,141 vs KES 40,425 per day), and the lift appears in every branch.

**Bottom line:** staffing and stock levels should follow the products and days that matter most, especially the top five products and weekends.

---

## Business Problem

The management of Kakuma Fresh Mart needed answers to practical operational questions, but the raw sales data was too messy to trust. This project answers four questions:

| # | Business Question | Why It Matters |
|---|-------------------|----------------|
| 1 | Which branch brings in the most revenue, and is it because customers spend more or because there are more of them? | Decides where to invest in staff, space and marketing |
| 2 | Which categories and products drive revenue? | Protects best-sellers from stock-outs and guides purchasing |
| 3 | Do returning customers spend differently, and how do customers pay? | Tests whether the loyalty programme adds value and shows payment mix |
| 4 | When does the business sell most? | Supports weekly staff scheduling and stock planning |

---

## Dataset

| Attribute | Detail |
|-----------|--------|
| **File** | `kakuma_fresh_mart_sales.csv` (raw) → `kakuma_fresh_mart_sales_clean.csv` (cleaned) |
| **Size (raw)** | 2,509 rows × 9 columns |
| **Branches** | Kakuma Town, Lodwar, Kitale |
| **Products** | 19 products across several categories |
| **Period covered** | About three weeks of transactions |
| **Granularity** | One row per sales line (item sold), not per customer visit |
| **Key fields** | `date`, `branch`, `category`, `product`, `qty`, `price`, `payment`, `customer`, `receipt` |

### Data quality issues found

| Issue | Count / Detail |
|-------|----------------|
| Duplicate rows | 30 |
| Missing quantity | 12 |
| Missing price (plus `"TBC"` text and zero prices) | 44 missing, plus placeholders |
| Missing payment method | 61 |
| Missing customer ID | 1,394 |
| Inconsistent labels | Branch and category spacing/casing; payment spelled `mpesa`, `Mpesa`, `M-PESA` |
| Mixed date formats | `YYYY-MM-DD` and `DD/MM/YYYY` |
| Extreme quantities | Values such as 999, 500 and 250 |

---

## Methodology

The project follows a three-stage workflow, with one notebook per stage.

### 1. Explore: `Exploring_Data_with_pandas.ipynb`
- Inspected shape, head, tail and random samples to understand the data.
- Used `info()` and `isna().sum()` to find wrong data types and missing values.
- Counted duplicates and profiled categorical columns (`branch`, `category`, `product`, `payment`).
- Identified the mixed date formats.

### 2. Clean: `Cleaning_Data_with_Pandas.ipynb`
- Worked on a **copy** of the raw data so the original stays untouched.
- **Removed 30 duplicate rows.**
- **Standardised labels:** trimmed whitespace and fixed casing for `branch` and `category`; mapped payment variants to a single `M-Pesa`; filled missing payments with `unknown`.
- **Parsed two date formats** with `pd.to_datetime(..., errors="coerce")` and combined them into one clean date column.
- **Handled invalid values:** dropped rows with missing quantity, converted `price` to numeric (`"TBC"` → `NaN`), and set zero prices to `NaN`.
- **Flagged instead of deleting:** created `is_return` (negative quantity), `unusual_qty` (quantity above 100) and `new_customer` (missing customer ID), so no information was lost.
- Exported the result to a clean CSV.

### 3. Analyse: `Analysising_Data_with_Pandas.ipynb`
- Created a `revenue` column (`qty × price`) and excluded flagged unusual quantities from net revenue.
- **Branch analysis:** `groupby` and `agg` for total revenue, line counts and average per line.
- **Category and product analysis:** revenue share (%) and a revenue-vs-volume comparison.
- **Customer behaviour:** compared new vs returning customers (count, mean, median) and used `crosstab` and `pivot_table` for payment methods by branch.
- **Time analysis:** daily revenue, weekday patterns, and weekend vs weekday revenue per day, normalised by the number of days in each group for a fair comparison.

---

## Key Findings

### 1. Branch performance
Kakuma Town earns about **40% of total revenue**, but because it **sells more**, not because customers spend more. It records roughly 1,000 sales lines against about 730 at each of the other branches, while its average spend per line (about KES 376) is the **lowest** of the three.

### 2. Category and product concentration
- Grains, cooking and dairy categories together make up about **72% of revenue** (25.2%, 24.3% and 22.5%).
- The smallest category contributes only about 6.5%.
- **Five of 19 products generate about 54% of revenue.** Revenue and unit volume tell different stories: eggs leads by revenue, not by units.

### 3. Customer behaviour
- Returning customers average about **KES 386** per line vs **KES 384** for new customers (medians 250 vs 260), which is practically no difference.
- "No difference" is a valid finding: the data gives no sign that loyalty members spend more per line.
- Payment patterns are broadly similar across branches.

### 4. Timing
- Weekend days earn about **41% more** than weekdays: **KES 57,141 vs KES 40,425** per day.
- The weekend lift holds at every branch: **Kakuma Town +38%, Kitale +42%, Lodwar +45%**.

---

## Recommendations

1. **Protect the top five products.** Make sure they never run out of stock, since they carry over half of revenue.
2. **Plan around the weekend.** Schedule more staff and fully stock shelves for Saturday and Sunday at all branches.
3. **Grow Kakuma Town through traffic, not basket size.** Its strength is volume, so protect checkout speed and availability. Test upselling to lift the lowest average spend per line.
4. **Review the loyalty programme.** Members spend about the same as non-members per line, so the programme should be assessed on retention and visit frequency rather than basket value.

---

## Limitations & Next Steps

**Limitations**
- The dataset covers only about **three weeks**, so each weekday appears just three times. Differences between *individual* weekdays are weak evidence; the weekend vs weekday comparison is more reliable.
- The file records **items sold, not customer visits**, so basket size, customer counts and visit frequency cannot be measured.
- Missing customer IDs were treated as "new customers", which is an assumption that should be confirmed with the business.
- Extreme quantities (999, 500, 250) were excluded from revenue as likely entry errors, and slash-formatted dates were read as day-first. Both should be confirmed with the data owner.

**Next steps**
- Add visualisations (revenue by branch, product Pareto chart, weekday trend).
- Extend the analysis to a longer time period to test seasonality.
- Build a simple dashboard (Power BI, Tableau or Streamlit) for managers.

---

## Skills Demonstrated

| Area | Skills |
|------|--------|
| **Data cleaning** | Deduplication, label standardisation, handling mixed date formats, type conversion, missing and impossible values, flagging vs deleting |
| **Data analysis** | `groupby`, `agg`, `pivot_table`, `crosstab`, percentage share, normalised comparisons |
| **Exploratory analysis** | Data profiling, structure and type checks, categorical analysis |
| **Python & pandas** | `pd.to_datetime`, `pd.to_numeric`, `str` methods, boolean filtering, datetime accessors (`dt`) |
| **Business thinking** | Translating business questions into analysis, separating volume from value, reporting "no difference" honestly |
| **Communication** | Insight-led findings, actionable recommendations, clear statement of caveats and assumptions |
| **Tools** | Python, pandas, Jupyter Notebook, Git/GitHub |

---

## Repository Structure

```
├── dataset/
│   ├── kakuma_fresh_mart_sales.csv          # Raw data
│   └── kakuma_fresh_mart_sales_clean.csv    # Cleaned data (output of notebook 2)
├── Exploring_Data_with_pandas.ipynb         # Step 1: Explore
├── Cleaning_Data_with_Pandas.ipynb          # Step 2: Clean
├── Analysising_Data_with_Pandas.ipynb       # Step 3: Analyse
└── README.md
```

---



Run the notebooks in this order: **Exploring → Cleaning → Analysing**. The cleaning notebook produces the clean CSV that the analysis notebook reads.

---

## Author

**kunda Abdurahman** 
Data Analyst
