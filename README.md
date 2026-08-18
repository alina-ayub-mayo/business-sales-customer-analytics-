# E-Commerce Sales & Customer Analytics

SQL + Python analysis of a 12-month e-commerce dataset: revenue and growth
trends, product and category performance, geographic breakdown, and RFM
customer segmentation — ending in an interactive Plotly dashboard and a set
of costed business recommendations.

> **Note on data:** `<state here where the CSVs came from and under what
> licence — see "Data" below. Fill this in before making the repo public.>`

---

## What this project does

| Stage | Detail |
|---|---|
| **Model** | Five CSVs loaded into a normalised SQLite schema (`customers`, `orders`, `order_items`, `products`) |
| **Validate** | Referential-integrity checks, duplicate-key checks, null profiling, and an orders-to-line-items reconciliation |
| **Analyse** | 20+ SQL queries — window functions (`NTILE`, `LAG`), CTEs, multi-table joins, month-over-month growth |
| **Segment** | RFM scoring (quintiles) mapped to seven named customer segments |
| **Visualise** | Matplotlib and Seaborn exploratory charts, plus a single-page Plotly dashboard |
| **Recommend** | Findings translated into prioritised commercial actions |

## Dataset

| Table | Rows | Notes |
|---|---|---|
| `customers` | 300 | one row per customer, with country |
| `orders` | 1,000 | 80.5% Completed, 10.3% Cancelled, 9.2% Returned |
| `order_items` | 2,000 | line items, quantity × price |
| `products` | 50 | across four categories |

All analysis is scoped to **completed orders only**. Cancelled and returned
orders are excluded from every revenue figure.

### Data-quality finding

805 orders carry a `Completed` status, but only **695** have matching rows in
`order_items` — 110 completed orders carry no line items and therefore no
revenue. Rather than paper over the gap, every revenue metric in this notebook
is explicitly computed over the 695 orders that join cleanly, and the
discrepancy is surfaced as a reconciliation query. In a real engagement this is
the first question to take back to the client's data team.

## Headline results

- **$311,111** in revenue from 695 completed orders, 4,848 units sold
- **Hair** is the strongest category at $101,637 (32.7% of revenue); **Skin**
  is the weakest at $52,378 (16.8%)
- Revenue is seasonal: January ($30,948) and October ($30,860) peak, February
  troughs at $23,299; October posted the strongest month-over-month growth at **+32.3%**
- Customers split into seven RFM segments — `<insert the corrected segment
  table here after re-running>`

## Repository layout

```
ecommerce-sales-customer-analytics/
├── README.md
├── requirements.txt
├── LICENSE
├── .gitignore
├── notebooks/
│   └── ecommerce_sales_customer_analytics.ipynb
├── data/
│   ├── raw/                 # customers.csv, orders.csv, order_items.csv, products.csv
│   └── README.md            # provenance + licence for the data
├── sql/                     # each analysis query as a standalone .sql file
│   ├── 01_data_quality_checks.sql
│   ├── 02_revenue_and_status.sql
│   ├── 03_monthly_trend.sql
│   ├── 04_product_category_performance.sql
│   ├── 05_country_category.sql
│   └── 06_rfm_segmentation.sql
└── outputs/
    ├── dashboard.html       # exported Plotly dashboard
    └── charts/              # exported PNGs used in this README
```

Splitting the SQL out into `sql/` matters more than it looks: it is the fastest
way for a reviewer to see the actual query craft without opening a notebook,
and it is the part of this repo a hiring manager will skim first.

## Running it

```bash
git clone https://github.com/techemayo/ecommerce-sales-customer-analytics.git
cd ecommerce-sales-customer-analytics
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
jupyter lab notebooks/ecommerce_sales_customer_analytics.ipynb
```

The notebook builds `sales.db` from the CSVs on first run; no external database
is required.

## Method notes

**RFM scoring.** Recency is measured as days since each customer's last
completed order, relative to the most recent order date *in the dataset* rather
than today's date — so the analysis reproduces identically whenever it is run.
All three dimensions are scored 1–5 on quintiles under one convention: **5 is
always best**. Because a low recency value (a recent buyer) must map to a high
score, the recency tile is inverted; an assertion in the notebook verifies this
holds rather than trusting it.

**Insight generation.** Every figure in the insights and executive-summary
sections is computed from the dataframes at runtime, not typed in. Hardcoded
numbers in a portfolio notebook go stale the moment anything upstream changes.

## Tech stack

Python · pandas · SQLite (SQL window functions, CTEs) · Matplotlib · Seaborn · Plotly · Jupyter

## Data

`<Required before publishing. State the source of the CSVs and confirm the
licence permits redistribution. If the data is synthetic, say so and include
the generator script. If it came from Kaggle or similar, link the source, name
the licence, and check whether redistribution is allowed — several popular
datasets permit use but not re-hosting. If in doubt, ship a generator that
produces equivalent synthetic data and keep the repo self-contained.>`

## Licence

MIT — see [LICENSE](LICENSE). Note that the code licence does not cover the
dataset; the dataset's own terms apply.
