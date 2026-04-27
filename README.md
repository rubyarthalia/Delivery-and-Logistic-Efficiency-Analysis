# Olist E-Commerce Delivery & Logistics Analysis

## Overview
Analysis of 96,304 delivered orders from Olist, Brazil's largest e-commerce marketplace (2016–2018), focusing on delivery performance, freight costs, revenue trends, and customer satisfaction.

🔗 **[View Notebook](./notebook/LogisticAnalysis.ipynb)**

---

## Dataset
**Source:** [Kaggle — Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce)

9 raw tables: orders, order items, payments, reviews, products, customers, sellers, geolocation, and category translations.

---

## Tools & Tech Stack
| Tool | Purpose |
|---|---|
| **Python (Pandas, NumPy)** | Cleaning, merging, feature engineering |
| **Matplotlib & Seaborn** | Visualization |
| **Jupyter Notebook** | End-to-end analysis |

---

## Data Pipeline

**Cleaning:** Dropped undelivered/cancelled orders, removed null delivery dates, fixed 186 negative processing times, filled missing review scores with median, deduplicated geolocation by zip prefix, aggregated payments per order before merging.

**Key engineered features:**
```python
df_combined['delivery_delay_days']  = (order_delivered - order_estimated).dt.days
df_combined['actual_delivery_days'] = (order_delivered - order_purchase).dt.days
df_combined['processing_time_days'] = (carrier_date - order_purchase).dt.days
df_combined['freight_ratio']        = freight_value / price
df_combined['is_late']              = (delivery_delay_days > 0).astype(int)
```

**Final shape: 110,645 rows × 29 columns → 96,304 unique delivered orders**

---

## Key Findings

**1. Conservative delivery estimates — orders arrive 12 days early on average**
Olist systematically over-estimates delivery time as a buffer strategy. The delay distribution is heavily left-skewed, with most orders arriving well ahead of schedule.

**2. 93.4% on-time delivery rate**
Only 7,292 orders arrived later than estimated. Given the conservative estimates, the true performance bar is even higher than it appears.

**3. Beating expectations drives satisfaction more than speed**
1-star reviewers received orders averaging 6 days early. 5-star reviewers averaged 13 days early. Customers don't care how long delivery takes — they care whether it beat their expectation.

**4. AM and MA are severe outliers**
Amazonas (AM) has a ~33% late rate, 5× the national average. Maranhão (MA) follows at ~20%. All other states cluster below 8%. The problem is geographic, not systemic.

**5. Freight is expensive for low-value items**
Median freight ratio is 23.2%. For items under BRL 50, freight often exceeds 40–50% of the product price — a real barrier for low-value purchases.

**6. SP dominates demand, Southeast Brazil rules overall**
São Paulo accounts for ~40K orders — 3× more than second-place RJ (~12K). The top 3 states (SP, RJ, MG) represent the majority of total volume.

**7. Revenue grew 10× from 2016 to 2017, peaked at Black Friday**
Monthly revenue grew from near zero in late 2016 to ~BRL 1M by November 2017, with a clear Black Friday spike. Revenue stabilized at ~BRL 850K–980K/month through 2018.

**8. Health & beauty leads all categories; satisfaction is polarized**
`health_beauty` tops revenue at ~BRL 1.25M. Review scores follow a J-curve 57.9% give 5 stars, but 11.3% give 1 star, with little middle ground.

---

## Recommendations

- **Fix AM/MA logistics** —> partner with regional carriers or set longer estimated windows for northern states to reset expectations
- **Reduce freight burden on low-value orders** —> subsidize shipping or introduce minimum order thresholds for affordable categories
- **Formalize the expectation-beating strategy** —> set delivery estimates that are consistently beatable rather than just accurate; it's the cheapest lever for improving review scores

---

## Repository Structure
```
olist-analysis/
├── README.md
├── notebook/
│   └── LogisticAnalysis.ipynb
└── charts/
│   └── all charts in PNG
```

---

## What I Learned
- Joining and cleaning 9 relational tables into one analysis-ready DataFrame
- Engineering delivery and freight features from raw timestamps and prices
- The most valuable insight (expectation-beating drives satisfaction) wasn't in the data directly, it emerged from combining delay and review score into one chart
