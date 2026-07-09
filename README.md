# Cafe Sales Analysis

Exploratory analysis of a messy cafe transactions dataset — cleaning, product performance, payment/location trends, and time-based patterns using Python (pandas, matplotlib).

## Dataset

`data/dirty_cafe_sales.csv` — 10,000 transactions with intentionally introduced data quality issues (missing values, `"ERROR"`/`"UNKNOWN"` placeholder strings, inconsistent types). Columns: Transaction ID, Item, Quantity, Price Per Unit, Total Spent, Payment Method, Location, Transaction Date.

## Tools

Python, pandas, numpy, matplotlib

## How to run

```bash
pip install -r requirements.txt
jupyter notebook notebook/cafe_sales_analysis.ipynb
```

## Data cleaning approach

1. Replaced placeholder strings (`ERROR`, `UNKNOWN`) with `NaN`, then coerced numeric and date columns.
2. Recovered missing values using column relationships: `Total Spent = Quantity × Price Per Unit`, applied in cascading order so each fill could feed the next.
3. Mapped missing `Price Per Unit` from a fixed per-item price lookup, then used that to back-fill missing `Item` values where the price was unique to one item (e.g., $2.00 → Coffee).
4. Where a price point was shared by two items (e.g., $3.00 → Cake or Juice), assigned the missing item alternately between the two candidates, since the true item couldn't be recovered from the data. **This is a modeling assumption, not an inferred fact** — it affects the Cake/Juice/Sandwich/Smoothie counts specifically.
5. Filled missing `Payment Method` and `Location` with `"Unknown"` rather than dropping rows, and reported the missing percentage explicitly instead of hiding it.
6. Dropped the remaining ~5% of rows where key fields (Item, Date, Quantity, Total Spent) couldn't be recovered at all.

Final cleaned dataset: 9,501 transactions (from an original 10,000).

## Key findings

**Sales overview** — ~$84.8k total revenue, $8.92 average transaction, 9,501 transactions.

**Product performance** — Salad drives the most revenue ($18.2k, $15.02 avg order), Cookie the least ($3.4k, $2.99 avg order). Coffee is the most frequently ordered item by volume.

**Payment & location** — Digital Wallet is the most-used payment method, narrowly ahead of Credit Card and Cash. 31.6% of payment method values were missing. In-store transactions edge out Takeaway in both total revenue and average order value, though the gap is small. 39.6% of location values were missing/unknown, and interestingly that "Unknown" group has the highest total revenue of all three groups and an average order value that sits between In-store and Takeaway.

**Time trends** — June had the highest monthly revenue (~$7.35k), though October is a close second (~$7.31k) — not a clear standout. Friday is the busiest day for transaction volume, with Monday and Sunday essentially tied for second.

## Limitations

- Two item categories per shared price point were assigned via alternation, not inference — treat Cake/Juice/Sandwich/Smoothie splits as directional, not exact.
- No customer demographic or segment data exists in this dataset, so any explanation of *why* a pattern occurs (e.g., seasonal or day-of-week effects) is speculative and not something this data can confirm.