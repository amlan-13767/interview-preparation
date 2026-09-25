# Why Olist Is Losing Customers — End-to-End Analytics Warehouse

> **An end-to-end analytics engineering, data warehouse, statistical analysis, performance optimization, and BI dashboard project built on the Olist Brazilian E-Commerce Public Dataset using MySQL 8, Python, Pandas, SciPy, SQLAlchemy, PyMySQL, Matplotlib, and Streamlit.**

---

## Table of Contents

1. [Project Overview](#project-overview)
2. [The Business Problem](#the-business-problem)
3. [Project in One Picture](#project-in-one-picture)
4. [Dataset](#dataset)
5. [The Most Important Modelling Decision](#the-most-important-modelling-decision)
6. [Grain](#grain)
7. [Star Schema](#star-schema)
8. [Dimensions](#dimensions)
9. [Fact Tables](#fact-tables)
10. [Raw Data and Staging](#raw-data-and-staging)
11. [Python Preprocessing](#python-preprocessing)
12. [Bulk Loading](#bulk-loading)
13. [Data Types and Transformations](#data-types-and-transformations)
14. [Date Dimension and Recursive CTE](#date-dimension-and-recursive-cte)
15. [Geography and ZIP Centroids](#geography-and-zip-centroids)
16. [Product Dimension](#product-dimension)
17. [Fact Orders and Derived Measures](#fact-orders-and-derived-measures)
18. [Multiple Reviews and ROW_NUMBER](#multiple-reviews-and-row_number)
19. [Customer Order Sequence](#customer-order-sequence)
20. [Data Quality Framework](#data-quality-framework)
21. [Idempotency](#idempotency)
22. [Retention Analysis](#retention-analysis)
23. [Cohort Analysis](#cohort-analysis)
24. [RFM Analysis](#rfm-analysis)
25. [Time to Second Order](#time-to-second-order)
26. [Delivery Funnel](#delivery-funnel)
27. [Delivery vs Review](#delivery-vs-review)
28. [Correlation: Pearson vs Spearman](#correlation-pearson-vs-spearman)
29. [Correlation Is Not Causation](#correlation-is-not-causation)
30. [First Review vs Repeat Purchase](#first-review-vs-repeat-purchase)
31. [Late First Order vs Repeat Purchase](#late-first-order-vs-repeat-purchase)
32. [Size the Prize](#size-the-prize)
33. [Revenue Analysis](#revenue-analysis)
34. [AOV](#aov)
35. [New vs Returning Revenue](#new-vs-returning-revenue)
36. [LAG and MoM Growth](#lag-and-mom-growth)
37. [Pareto Analysis](#pareto-analysis)
38. [Seller Scorecard](#seller-scorecard)
39. [RANK vs ROW_NUMBER](#rank-vs-row_number)
40. [PERCENT_RANK](#percent_rank)
41. [Seller Concentration](#seller-concentration)
42. [Haversine Distance](#haversine-distance)
43. [Freight Analysis](#freight-analysis)
44. [Payment Analysis](#payment-analysis)
45. [SQL Performance Optimization](#sql-performance-optimization)
46. [EXPLAIN vs EXPLAIN ANALYZE](#explain-vs-explain-analyze)
47. [Indexing](#indexing)
48. [Covering Index](#covering-index)
49. [Index Trade-offs](#index-trade-offs)
50. [The Index That Initially Made Q2 Slower](#the-index-that-initially-made-q2-slower)
51. [Window Function vs Correlated Subquery](#window-function-vs-correlated-subquery)
52. [The LIMIT Benchmark Trap](#the-limit-benchmark-trap)
53. [Reporting Tables](#reporting-tables)
54. [Stored Procedure and Transactions](#stored-procedure-and-transactions)
55. [MySQL Event Scheduler](#mysql-event-scheduler)
56. [Streamlit Dashboard](#streamlit-dashboard)
57. [SQLAlchemy and PyMySQL](#sqlalchemy-and-pymysql)
58. [Caching](#caching)
59. [Python Statistical Analysis](#python-statistical-analysis)
60. [Why SQL + Python](#why-sql--python)
61. [Technology Stack](#technology-stack)
62. [Architecture](#architecture)
63. [Repository Structure](#repository-structure)
64. [How to Run](#how-to-run)
65. [Dataset Download](#dataset-download)
66. [Business Findings](#business-findings)
67. [Recommendations from the Analysis](#recommendations-from-the-analysis)
68. [Performance Results](#performance-results)
69. [Data Quality Results](#data-quality-results)
70. [Known Limitations](#known-limitations)
71. [Alternatives and Why These Choices Fit](#alternatives-and-why-these-choices-fit)
72. [What I Would Build Next](#what-i-would-build-next)
73. [PwC Interview Preparation](#pwc-interview-preparation)
74. [90-Second Project Explanation](#90-second-project-explanation)
75. [Technical Challenge Story](#technical-challenge-story)
76. [Performance Optimization Story](#performance-optimization-story)
77. [Business Insight Story](#business-insight-story)
78. [Final Mental Model](#final-mental-model)

---

# Project Overview

## Title

**Why Olist Is Losing Customers**

## One-line description

An end-to-end analytics warehouse over approximately 100K Brazilian e-commerce orders, built in MySQL 8, with Python preprocessing, dimensional modelling, automated data-quality testing, retention/delivery/revenue/seller analytics, statistical analysis, SQL performance tuning, pre-aggregated reporting tables, and an interactive Streamlit dashboard.

## What the project demonstrates

This is not simply a SQL dashboard. It demonstrates:

- Data ingestion
- Data cleaning
- ETL / ELT concepts
- Staging architecture
- Data modelling
- Star schema design
- Fact and dimension tables
- Grain management
- Surrogate keys
- Business keys
- Data-quality engineering
- Idempotent transformations
- SQL CTEs
- Recursive CTEs
- Window functions
- `ROW_NUMBER`
- `RANK`
- `NTILE`
- `LAG`
- `PERCENT_RANK`
- Aggregation
- `CASE`
- `HAVING`
- Date functions
- Cohort analysis
- Retention analysis
- RFM segmentation
- Delivery analytics
- Revenue analytics
- Seller scorecards
- Pareto analysis
- Geographic analysis
- Haversine distance
- Statistical correlation
- Pearson correlation
- Spearman correlation
- Python/Pandas
- SciPy
- Matplotlib
- SQLAlchemy
- PyMySQL
- Streamlit
- Query optimization
- `EXPLAIN ANALYZE`
- Indexing
- Covering indexes
- Window-function rewrites
- Reporting tables
- Stored procedures
- Transactions
- Scheduled database events
- BI-oriented architecture

---

# The Business Problem

The project asks:

> **Why are customers not coming back, and what operational factors are associated with customer experience and repeat purchase?**

The analysis investigates five broad areas.

### Customer questions

- How many customers actually return?
- Who are the repeat customers?
- How long does it take to make a second purchase?
- Does the first customer experience affect retention?
- Does the first review score predict repeat purchase?

### Delivery questions

- Does late delivery affect customer satisfaction?
- Does late delivery affect repeat purchase?
- Where in the delivery process are delays occurring?
- Which sellers are associated with the largest amount of late delivery?
- Are promised delivery dates realistic?

### Revenue questions

- How is revenue changing month over month?
- How much revenue comes from new versus returning customers?
- Which categories generate most revenue?
- How concentrated is revenue?

### Seller questions

- Which sellers have high late-delivery rates?
- Which sellers have sufficient volume to be statistically meaningful?
- How concentrated are late deliveries among sellers?

### Logistics questions

- How does freight relate to geographic distance?
- How does freight relate to product weight?
- Which factor has the stronger linear association with freight?

### Engineering questions

- How can the raw data be ingested safely?
- How can analytical grain be protected?
- How can data quality be tested automatically?
- How can repeated queries be made faster?
- How can a dashboard be made responsive without repeatedly scanning detailed facts?

---

# Project in One Picture

```text
                 OLIST RAW DATA
                      │
                      ▼
              9 CSV DATASETS
                      │
                      ▼
             Python preprocessing
                 prep_csvs.py
                      │
                      ▼
              ┌───────────────┐
              │ MySQL STAGING │
              │   stg_*       │
              └───────┬───────┘
                      │
                Transformation
                      │
                      ▼
             ┌─────────────────┐
             │  STAR SCHEMA    │
             │                 │
             │ Dimensions      │
             │ Facts           │
             └────────┬────────┘
                      │
          ┌───────────┼────────────┐
          ▼           ▼            ▼
      Data Quality  Analytics   Performance
          │           │            │
          │           ▼            ▼
          │       Retention     Indexing
          │       Delivery      EXPLAIN
          │       Revenue       Window funcs
          │       Sellers
          │
          └─────────────┬─────────────┐
                        ▼
                Reporting tables
                   rpt_*
                        │
             ┌──────────┴──────────┐
             ▼                     ▼
          Python                Streamlit
        Statistics              Dashboard
```

The complete pipeline is:

```text
Raw CSVs
   ↓
Data Preparation
   ↓
MySQL Staging
   ↓
Star Schema
   ↓
Data Quality
   ↓
Retention Analysis
   ↓
Delivery Analysis
   ↓
Revenue & Seller Analysis
   ↓
Performance Tuning
   ↓
Reporting Tables
   ↓
Python Statistical Analysis
   ↓
Streamlit Dashboard
```

---

# Dataset

The project uses the **Brazilian E-Commerce Public Dataset by Olist**, published by Olist under **CC BY-NC-SA 4.0**.

The dataset contains roughly 100K orders placed between 2016 and 2018 across nine related CSV files.

| Dataset | Approximate rows | Purpose |
|---|---:|---|
| `olist_customers_dataset.csv` | 99,441 | Customer/order-level customer records |
| `olist_orders_dataset.csv` | 99,441 | Order lifecycle |
| `olist_order_items_dataset.csv` | 112,650 | Products/sellers in orders |
| `olist_order_payments_dataset.csv` | 103,886 | Payment information |
| `olist_order_reviews_dataset.csv` | 99,224 | Customer reviews |
| `olist_products_dataset.csv` | 32,951 | Product attributes |
| `olist_sellers_dataset.csv` | 3,095 | Seller information |
| `olist_geolocation_dataset.csv` | 1,000,163 | ZIP-prefix geographic coordinates |
| `product_category_name_translation.csv` | 71 | Portuguese-to-English category translation |

Expected Olist v2 row counts:

```text
customers              99,441
geolocation          1,000,163
orders                 99,441
order_items           112,650
payments              103,886
reviews                99,224
products               32,951
sellers                 3,095
category_translation       71
```

---

# The Most Important Modelling Decision

Olist ships with two customer keys:

| Column | Distinct values | Meaning |
|---|---:|---|
| `customer_id` | 99,441 | A fresh key associated with every order |
| `customer_unique_id` | 96,096 | The actual person |

This distinction changes the retention analysis.

If you group by:

```text
customer_id
```

the same person can appear to be a different customer across different orders.

That creates an artificial picture of almost no repeat customers.

The correct analytical identity is:

```text
customer_unique_id
```

Therefore:

```text
dim_customer
```

is built at the:

```text
customer_unique_id grain
```

and:

```text
map_customer_id
```

resolves the per-order `customer_id` to the correct warehouse customer key.

The retention analysis intentionally reports the repeat rate both ways, and the data-quality suite includes a check to ensure that the two grains do not accidentally collapse to the same count.

---

# Grain

## What is grain?

Grain means:

> **What exactly does one row represent?**

This is one of the most important concepts in the project.

### `dim_customer`

```text
1 row = 1 actual customer/person
```

### `fact_orders`

```text
1 row = 1 order
```

### `fact_order_items`

```text
1 row = 1 product line/order item
```

### `fact_payments`

```text
1 row = 1 payment leg
```

A major reason for using separate facts is that an order can contain multiple items and multiple payments.

For example:

```text
1 order
3 items
2 payments
```

A naive join can create:

```text
3 × 2 = 6 rows
```

and accidentally multiply revenue.

Therefore, grain must be explicitly protected.

---

# Star Schema

The warehouse uses a **star schema** because the project is analytical rather than transactional.

Conceptually:

```text
                 dim_customer
                      │
                      │
dim_date ───── fact_orders ───── dim_seller
                      │
                      │
                dim_product
```

with additional facts:

```text
fact_order_items
fact_payments
```

Dimensions describe entities.

Facts represent measurable business events.

## Why Star Schema?

Advantages:

- Easy for analysts to understand
- Simple BI joins
- Clear business grain
- Efficient analytical querying
- Good separation of measures and descriptive attributes
- Avoids the duplication caused by one giant denormalized table

## Why not one giant table?

Because different source tables have different grains.

Joining orders, items, payments and reviews into one giant table can cause row multiplication.

## Why not snowflake schema?

A snowflake schema normalizes dimensions further:

```text
Fact
 ↓
Product
 ↓
Category
 ↓
Department
```

A star schema keeps dimensions relatively denormalized:

```text
Fact
 ├── Customer
 ├── Product
 ├── Seller
 └── Date
```

For this BI-oriented analytical workload, the star schema is simpler and reduces join complexity.

---

# Dimensions

## `dim_date`

Contains reusable calendar attributes:

```text
date
year
quarter
month
month_name
month_start
year_month
day
weekday
weekend
```

## `dim_customer`

Contains:

```text
customer_sk
customer_unique_id
zip_code_prefix
city
state
```

The grain is the actual person.

## `dim_seller`

Contains:

```text
seller_sk
seller_id
zip_code_prefix
city
state
```

## `dim_product`

Contains:

```text
product_sk
product_id
category_pt
category_en
weight_g
length_cm
height_cm
width_cm
volume_cm3
photos
```

## `dim_geography`

Contains approximately one representative coordinate per ZIP prefix:

```text
zip_code_prefix
latitude
longitude
city
state
```

---

# Fact Tables

## `fact_orders`

Grain:

> One row per order.

Contains order lifecycle and derived analytical measures such as:

```text
order_id
customer_sk
purchase timestamp
approval timestamp
carrier timestamp
delivery timestamp
estimated delivery timestamp
order status
delivery days
promised days
delay days
is_late
review score
order value
payment value
item count
customer order sequence
```

## `fact_order_items`

Grain:

> One row per order line.

Contains:

```text
order_id
order_item_id
product_sk
seller_sk
price
freight_value
```

## `fact_payments`

Grain:

> One row per payment leg.

An order can be split across payment methods, so:

```text
order_id
payment_sequential
payment_type
payment_installments
payment_value
```

are preserved at payment grain.

---

# Surrogate Keys vs Business Keys

The warehouse uses surrogate keys such as:

```text
customer_sk
seller_sk
product_sk
```

Example:

```text
customer_unique_id = abc123
customer_sk        = 45821
```

A surrogate key is a warehouse-generated identifier.

Benefits:

- Integer-based joins
- Stable warehouse relationships
- Separation from source-system identifiers
- Easier dimensional modelling
- Supports future slowly changing dimensions

An alternative is to use source IDs directly. That can work for a small project, but surrogate keys are conventional in analytical warehouses.

---

# Raw Data and Staging

The pipeline first creates nine staging tables:

```text
stg_customers
stg_geolocation
stg_orders
stg_order_items
stg_order_payments
stg_order_reviews
stg_products
stg_sellers
stg_category_translation
```

The staging tables intentionally use text-like fields.

## Why load raw data as TEXT?

The design separates:

```text
ingestion
```

from:

```text
validation + transformation
```

If raw data contains:

```text
"2017-09-01 10:30:00"
```

or malformed values, directly loading into typed columns can cause conversion errors or silent coercion.

Instead:

```text
CSV
 ↓
TEXT staging
 ↓
validation / transformation
 ↓
typed warehouse
```

This makes conversion problems visible and recoverable.

---

# Python Preprocessing

`prep_csvs.py` uses Pandas to make the CSV files safe for MySQL bulk loading.

It:

- Removes embedded newlines
- Removes tabs
- Trims whitespace
- Converts missing strings appropriately
- Writes the MySQL NULL sentinel `\N`
- Removes unused review comment text fields

The free-text columns:

```text
review_comment_title
review_comment_message
```

are removed because they are not required for the current analytical scope and are the main source of embedded-newline problems.

This does **not** mean text columns should always be removed.

If the project required NLP or sentiment analysis, those columns should be retained in a separate text/raw layer.

---

# Bulk Loading

The project uses:

```sql
LOAD DATA LOCAL INFILE
```

instead of individual INSERT statements.

This is a bulk-loading mechanism and is much faster for large CSV files.

The server and client must allow local file loading.

```bash
mysql -u root -p -e "SET GLOBAL local_infile = 1;"
```

and:

```bash
mysql --local-infile=1 -u root -p
```

The loading script prints a receipt of row counts so the source counts can be verified.

---

# Data Types and Transformations

Raw text is converted into analytical types such as:

```text
INT
DECIMAL
DATE
DATETIME
TINYINT
```

This is necessary for:

- Numeric aggregation
- Date arithmetic
- Sorting
- Filtering
- Comparisons
- Statistical calculations

Typical transformations include:

```sql
CAST(...)
STR_TO_DATE(...)
COALESCE(...)
CASE ...
```

---

# Date Dimension and Recursive CTE

The project generates `dim_date` using a recursive CTE.

Conceptually:

```text
2016-01-01
    ↓
2016-01-02
    ↓
2016-01-03
    ↓
...
```

The recursive CTE generates the calendar range and then derives:

```text
year
quarter
month
weekday
weekend
```

## Why a date dimension?

It provides:

- Consistent date attributes
- Reusable reporting logic
- Easier time-series analysis
- Centralized calendar definitions

Alternative:

Calculate `YEAR()`, `MONTH()`, etc. in every query.

The date dimension is cleaner for a warehouse.

---

# Geography and ZIP Centroids

The raw geolocation dataset contains about one million coordinate observations.

The warehouse collapses it to one representative point per ZIP prefix using:

```text
AVG(latitude)
AVG(longitude)
```

This creates an approximate centroid.

## Why?

Instead of carrying approximately one million geolocation rows into every analysis, the warehouse works with a manageable geography dimension.

## Important limitation

This is not exact address-level geography.

It is:

> A ZIP-prefix centroid approximation.

Therefore, distance calculations are estimates.

---

# Product Dimension

The product dimension translates Portuguese categories to English where available.

It also calculates:

```text
volume_cm3 =
length × height × width
```

Approximately 610 products have no category in the source.

They are labelled:

```text
unknown
```

rather than dropped.

## Why?

A missing category does not mean a missing transaction.

Dropping such products would silently remove revenue and break reconciliation with finance.

---

# NULL vs UNKNOWN

These are conceptually different.

### NULL

Means:

> Information is genuinely unavailable.

### `unknown`

Means:

> The warehouse deliberately assigns an analytical bucket to keep the transaction visible.

This distinction is useful in data-quality and BI work.

---

# Fact Orders and Derived Measures

The project derives several delivery measures.

## Delivery days

```text
actual delivery timestamp
-
purchase timestamp
```

## Promised days

```text
estimated delivery timestamp
-
purchase timestamp
```

## Delay days

```text
actual delivery timestamp
-
estimated delivery timestamp
```

Therefore:

```text
delay_days > 0
```

means the order arrived after the promised date.

Example:

```text
Purchase:   Jan 1
Promised:   Jan 10
Delivered:  Jan 13

delay_days = 3
```

## `is_late`

The logic is conceptually:

```sql
CASE
    WHEN delivered_customer_ts IS NULL THEN NULL
    WHEN delivered_customer_ts > estimated_delivery_ts THEN 1
    ELSE 0
END
```

An undelivered order is not automatically treated as on-time.

That is why:

```text
not delivered
```

is different from:

```text
delivered on time
```

---

# Why Materialize Derived Measures?

The project stores reusable measures such as:

```text
delivery_days
promised_days
delay_days
is_late
```

rather than recalculating them in every query.

Advantages:

- Simpler SQL
- Consistent business definitions
- Reusability
- Potential indexing
- Potential performance gains

Alternative:

Calculate them dynamically every time.

That saves storage but can increase repeated computation and create inconsistent definitions.

---

# Multiple Reviews and ROW_NUMBER

An order can have more than one review row.

Because `fact_orders` has one row per order, all review rows cannot simply be joined without changing the grain.

The project uses:

```sql
ROW_NUMBER() OVER (
    PARTITION BY order_id
    ORDER BY review_creation_date, review_id
)
```

and selects:

```text
rn = 1
```

to retain the earliest review.

Alternatives include:

- Latest review
- Average review
- Separate review fact table
- All reviews retained independently

The current project chooses one review to preserve one-row-per-order grain.

---

# Customer Order Sequence

The project creates:

```text
customer_order_seq
```

using:

```sql
ROW_NUMBER() OVER (
    PARTITION BY customer_sk
    ORDER BY purchase_ts
)
```

Example:

```text
Customer A
Order 1 → 1
Order 2 → 2
Order 3 → 3
```

This creates an extremely useful business definition:

```text
customer_order_seq = 1
→ first-time customer
```

and:

```text
customer_order_seq > 1
→ returning customer
```

This feature is reused throughout retention and revenue analysis.

---

# Data Quality Framework

The project creates a 14-check automated SQL data-quality suite.

The checks cover:

### Completeness

- Source row counts
- Warehouse row counts
- Missing required values

### Uniqueness

- Duplicate order IDs
- Key uniqueness

### Referential integrity

- Orphan order items
- Orphan reviews
- Unresolved products
- Unresolved sellers/customers

### Validity

- Negative prices
- Invalid review scores
- Invalid geographic coordinates

### Temporal consistency

- Delivery before purchase
- Invalid timestamps

### Reconciliation

- Payment values versus order values
- Tolerance-based reconciliation

### Date coverage

- Fact dates falling outside the date dimension

The results are persisted in:

```text
data_quality_checks
```

as PASS/FAIL records.

## Why automated tests?

Instead of saying:

> "I looked at a few rows."

the pipeline can say:

> "The 14 automated checks pass."

This makes the analytical pipeline reproducible and auditable.

---

# Tolerance-Based Reconciliation

Payment value and order value do not necessarily match exactly because of:

- Vouchers
- Instalment behaviour
- Rounding

Therefore the quality suite uses a tolerance instead of demanding exact equality.

The project uses approximately:

```text
1% tolerance
```

This is more realistic than pretending all source systems reconcile perfectly.

---

# Idempotency

The transformation is designed to be idempotent.

Each target table is truncated before rebuilding.

Therefore:

```text
Run 1
→ correct

Run 2
→ same correct result
```

rather than:

```text
Run 1 → revenue = 100
Run 2 → revenue = 200
```

A non-idempotent transformation can corrupt an analytical warehouse.

The project explicitly avoids patterns such as repeatedly dividing values by 100.

---

# Retention Analysis

The project asks:

> Do customers come back?

The person-level repeat purchase rate is approximately:

```text
3.12%
```

This number only becomes meaningful after correcting the customer identity issue.

The project also compares the incorrect order-level key interpretation with the person-level interpretation to demonstrate why modelling matters.

---

# Cohort Analysis

Customers are grouped by:

```text
first purchase month
```

For example:

```text
September 2017 cohort
```

Then retention is measured over:

```text
Month 1
Month 2
Month 3
...
Month 12
```

## Why cohort analysis?

A January customer has had more time to return than an October customer.

Comparing them without accounting for acquisition date is unfair.

Cohort analysis normalizes customers by their starting point.

The cohort analysis is truncated at 12 months because the dataset only covers a finite historical period and later cohorts are right-censored.

---

# RFM Analysis

RFM means:

### R — Recency

How recently did the customer purchase?

### F — Frequency

How many orders did the customer make?

### M — Monetary

How much did the customer spend?

The project uses:

```sql
NTILE(5)
```

to create five approximately equal groups.

Conceptually:

```text
5 = top quintile
1 = bottom quintile
```

The project then creates business-readable segments such as:

```text
Champions
Loyal
New / Promising
At Risk
Cannot Lose Them
Hibernating
Needs Attention
```

These are **heuristic business rules**, not machine-learning clusters.

## Alternative: K-means

K-means could automatically discover customer groups.

However, it requires:

- Feature scaling
- Choosing K
- Cluster validation
- Interpretation

RFM is easier for business stakeholders to understand and communicate.

---

# Time to Second Order

The project calculates:

```text
first purchase
→ second purchase
```

using:

```sql
TIMESTAMPDIFF
```

It analyzes:

```text
minimum
average
median
maximum
```

The median is especially useful because purchase intervals can be highly skewed.

Example:

```text
1
2
3
5
1000
```

Mean:

```text
202.2
```

Median:

```text
3
```

Median better represents the typical customer in such a skewed distribution.

---

# Median Without a Native MEDIAN Function

MySQL does not provide the same direct `MEDIAN()` convenience available in some analytical systems.

The project can derive a median using:

```text
ROW_NUMBER()
+
COUNT()
```

Alternative databases such as PostgreSQL can use percentile functions such as:

```text
PERCENTILE_CONT
```

This is an example of adapting analytical logic to the database dialect.

---

# Delivery Funnel

The project analyzes the operational sequence:

```text
Purchase
   ↓
Approval
   ↓
Carrier
   ↓
Customer
```

and calculates durations such as:

```text
purchase → approval
approval → carrier
carrier → customer
```

This identifies where time is spent in the order lifecycle.

---

# Delivery vs Review

Orders are grouped into delivery-timing buckets such as:

```text
10+ days early
4–9 days early
on time
1–3 days late
4–7 days late
8–14 days late
15+ days late
```

For each bucket the project calculates:

```text
average review score
1–2 star percentage
5 star percentage
```

The relationship is strongly negative:

```text
more delay
   ↓
lower review score
```

---

# Correlation: Pearson vs Spearman

The Python analysis calculates both Pearson and Spearman correlation.

Observed delivery-delay/review relationship:

```text
Pearson ≈ -0.291
Spearman ≈ -0.178
```

## Pearson

Measures linear association.

It is sensitive to:

- Outliers
- Non-linear relationships

## Spearman

Measures rank-based monotonic association.

It is less dependent on a strictly linear relationship and is useful when values are not well behaved for Pearson.

Using both provides a broader view of the relationship.

---

# Correlation Is Not Causation

This is one of the most important analytical principles in the project.

The analysis can show:

```text
late delivery
      ↕
lower review score
```

or:

```text
late first order
      ↕
lower repeat rate
```

But it cannot prove:

```text
late delivery CAUSED churn
```

Potential confounders include:

- Geography
- Seller quality
- Product type
- Product weight
- Distance
- Customer characteristics
- Order value

To establish causality, a controlled experiment or stronger causal-inference design would be required.

---

# First Review vs Repeat Purchase

The project checks whether first-order review score predicts repeat purchase.

Observed repeat rates by first-order review score are approximately:

```text
5★ → 3.23%
4★ → 2.86%
3★ → 2.93%
2★ → 2.94%
1★ → 3.10%
```

Chi-square:

```text
7.82
```

Degrees of freedom:

```text
4
```

p-value:

```text
0.098
```

The relationship is not statistically significant at the conventional 5% threshold and is not monotonic.

The important analytical conclusion is:

> Review score does not appear to be a strong predictor of repeat purchase in this dataset.

---

# Late First Order vs Repeat Purchase

The project compares:

```text
First order on time
vs
First order late
```

Observed repeat rates:

```text
On time → approximately 3.13%
Late    → approximately 2.56%
```

The difference is statistically detectable:

```text
chi-square ≈ 7.57
p ≈ 0.006
```

However, statistical significance is not the same as causal proof.

The observed relationship should be interpreted as:

> A lower repeat rate is associated with having a late first order.

---

# Size the Prize

The project estimates the theoretical revenue opportunity if customers whose first orders were late repeated at the same rate as on-time customers.

Conceptually:

```text
affected customers
×
difference in repeat rate
×
average repeat-order value
```

The estimate is approximately:

```text
R$6,235
```

for approximately:

```text
7,592 affected customers
```

This is:

```text
~0.04% of GMV
```

Important:

> This is estimated gross recoverable revenue, not proven incremental revenue and not profit.

The dataset does not contain COGS or complete delivery-cost information, so this should not be interpreted as recoverable margin.

---

# Revenue Analysis

Monthly revenue analysis includes:

```text
Revenue
Orders
Active customers
AOV
Returning revenue percentage
Month-over-month growth
```

The project separates new and returning customer revenue using:

```text
customer_order_seq
```

This is important because total revenue can grow even while customer retention deteriorates.

---

# AOV

AOV means:

> **Average Order Value**

Formula:

```text
AOV = Revenue / Orders
```

It is a standard e-commerce KPI.

---

# New vs Returning Revenue

Using:

```text
customer_order_seq = 1
```

the project identifies new-customer orders.

Using:

```text
customer_order_seq > 1
```

it identifies returning-customer orders.

This allows the business to see whether revenue growth is coming from:

```text
new customer acquisition
```

or:

```text
existing customer repeat purchase
```

---

# LAG and MoM Growth

The project uses:

```sql
LAG(revenue) OVER (ORDER BY ym)
```

to retrieve previous-month revenue.

Then:

```text
MoM growth =
(current month revenue - previous month revenue)
/
previous month revenue
× 100
```

`LAG()` is a window function that accesses a previous row without collapsing the result set.

---

# Pareto Analysis

The project performs category-level Pareto analysis.

Steps:

```text
category revenue
      ↓
rank by revenue descending
      ↓
running revenue
      ↓
cumulative percentage
```

A running sum is calculated using a window frame such as:

```sql
SUM(revenue) OVER (
    ORDER BY revenue DESC
    ROWS BETWEEN UNBOUNDED PRECEDING AND CURRENT ROW
)
```

The result is approximately:

```text
18 of 72 categories
≈ 80% of revenue
```

This is the familiar 80/20 principle.

---

# Seller Scorecard

Seller-level metrics include:

```text
orders
revenue
late percentage
average review
average delivery days
late deliveries
```

The project applies a minimum volume threshold:

```text
30+ orders
```

## Why 30+ orders?

Without a volume floor, a seller with:

```text
1 order
1 late order
```

would have:

```text
100% late rate
```

That is not enough evidence to classify the seller as systematically poor.

A larger minimum sample provides a more stable signal.

This is a business judgement call and an important interview point.

---

# HAVING

The seller analysis uses conditions such as:

```sql
HAVING COUNT(*) >= 30
```

because the condition depends on an aggregate.

The difference is:

```text
WHERE
→ filters rows before aggregation

HAVING
→ filters groups after aggregation
```

This is a standard SQL interview question.

---

# RANK vs ROW_NUMBER

## `ROW_NUMBER()`

Always creates unique ordering:

```text
1
2
3
4
```

## `RANK()`

Ties receive the same rank:

```text
1
2
2
4
```

The project uses ranking logic where ties can legitimately share a position.

---

# PERCENT_RANK

The project uses:

```sql
PERCENT_RANK()
```

to position sellers relative to other sellers.

The dashboard/reporting logic uses business action bands such as:

```text
Top late-rate sellers → ESCALATE
Middle-high range      → WATCH
Others                 → OK
```

There are also explicit operational rules in the reporting layer based on seller volume and late rate, including:

```text
30+ orders and 25%+ late → ESCALATE
```

This separates analytical ranking from operational business rules.

---

# Seller Concentration

The project ranks sellers by late deliveries and calculates cumulative late deliveries.

The analysis finds approximately:

```text
103 sellers
```

out of:

```text
2,970 active sellers
```

representing:

```text
≈ 3.5%
```

account for about half of late deliveries.

This is a concentration analysis.

The practical implication is that the problem is relatively concentrated rather than uniformly distributed across the marketplace.

---

# Haversine Distance

The project estimates seller-to-customer distance using the Haversine formula.

Because latitude and longitude are geographic coordinates on Earth's surface, simple Euclidean distance is not appropriate for larger geographic distances.

Conceptually:

```text
d = 2R asin(
    sqrt(
        sin²(Δlat / 2)
        +
        cos(lat1)cos(lat2)sin²(Δlon / 2)
    )
)
```

where:

```text
R ≈ 6371 km
```

## Why Haversine?

Alternatives include:

- Euclidean distance
- Road-distance APIs
- GIS functions
- PostGIS geography
- Mapping/routing services

Haversine is appropriate here because the dataset provides latitude/longitude but does not provide actual road routes.

## Important limitation

Haversine is:

> Straight-line geographic distance.

It is not:

> Actual driving distance.

---

# Freight Analysis

The project analyzes freight value against:

```text
distance
weight
```

Observed Pearson correlations are approximately:

```text
Distance → freight: r ≈ 0.408
Weight   → freight: r ≈ 0.613
```

Interpretation:

> In this dataset, product weight has a stronger linear association with freight value than estimated geographic distance.

Again:

> Correlation does not prove that weight alone causes freight price.

---

# Payment Analysis

The project groups payments by:

```text
payment_type
```

and calculates:

```text
payment count
average installments
average payment value
total payment value
percentage of GMV
```

Payment types include:

```text
credit card
boleto
voucher
debit card
```

An order can contain multiple payment legs, which is why `fact_payments` preserves payment-level grain.

---

# SQL Performance Optimization

The project uses:

```sql
EXPLAIN ANALYZE
```

to benchmark analytical queries before and after optimization.

The philosophy is:

> Measure performance rather than assume an optimization works.

The workflow is:

```text
Baseline query
   ↓
EXPLAIN ANALYZE
   ↓
Add targeted index / rewrite
   ↓
Run again
   ↓
Compare actual timings
```

---

# EXPLAIN vs EXPLAIN ANALYZE

### `EXPLAIN`

Shows the optimizer's expected execution plan.

### `EXPLAIN ANALYZE`

Actually executes the query and reports observed runtime information.

MySQL 8.0.18+ supports `EXPLAIN ANALYZE`.

This makes it much more useful for real benchmarking.

---

# Indexing

The project creates targeted indexes such as:

```text
ix_fo_status_purchase_cover
ix_fo_late_review
ix_foi_seller_cover
ix_fo_delay
```

These are designed around actual analytical query patterns.

## Why not index everything?

Indexes cost:

- Storage
- Insert/update performance
- Maintenance
- Memory/cache space

Therefore, indexes should be created based on real access patterns.

---

# Covering Index

A covering index contains all columns required by a query.

For example, if a query needs:

```text
seller_sk
order_id
price
freight_value
```

and those values are available in the index, MySQL may answer the query directly from the index without repeatedly accessing the base table.

This can significantly improve read performance.

---

# Index Trade-offs

The project measures the index footprint.

| Table | Data MB | Index MB | Overhead |
|---|---:|---:|---:|
| `fact_orders` | 23.55 | 43.22 | 184% |
| `fact_order_items` | 11.52 | 27.64 | 240% |
| `fact_payments` | 9.52 | 12.06 | 127% |

The index bytes exceed the base data bytes.

That can be acceptable for a read-heavy analytical warehouse.

For a write-heavy OLTP system, the same indexing strategy would need to be reconsidered and narrowed.

---

# The Index That Initially Made Q2 Slower

This is one of the strongest performance stories in the project.

The first Q2 index looked like:

```text
(is_late, review_score, delay_days)
```

It appeared logically reasonable.

However, the query also required:

```text
delivered_customer_ts
```

which was not included.

Therefore MySQL had to:

```text
walk the index
+
perform additional base-row lookups
```

The query went from approximately:

```text
78 ms
```

to:

```text
220 ms
```

After adding the required column and restoring the covering property, it improved to:

```text
45 ms
```

The lesson:

> An index that looks relevant is not automatically a good index. Validate it with the execution plan and actual timings.

---

# Window Function vs Correlated Subquery

The project compares two ways of calculating a per-customer average.

## Slower approach

A correlated subquery executes a lookup repeatedly for each outer row.

Conceptually:

```sql
SELECT ...,
       (
           SELECT AVG(...)
           FROM fact_orders f2
           WHERE f2.customer_sk = fo.customer_sk
       )
FROM fact_orders fo;
```

## Faster approach

A window function performs the analytical calculation without repeatedly executing a correlated lookup:

```sql
AVG(order_value)
OVER (PARTITION BY customer_sk)
```

Measured full-throughput result:

```text
Correlated subquery → ~914 ms
Window function     → ~284 ms
```

Approximately:

```text
3.2× faster
```

This demonstrates that query rewrites can outperform simply adding indexes.

---

# The LIMIT Benchmark Trap

An important performance lesson emerged during benchmarking.

With:

```sql
LIMIT 5000
```

the correlated subquery could appear faster than the window-function version.

Why?

The correlated version can produce only the rows actually requested.

The window function may need to sort/process full partitions before producing the first requested rows.

Therefore:

```text
LIMIT
```

can measure startup/early-output behaviour rather than full query throughput.

The project forces full materialization using a pattern such as:

```sql
SELECT COUNT(*)
FROM (...)
```

so both formulations process the full result.

This produces the more meaningful:

```text
~914 ms → ~284 ms
```

comparison.

---

# Performance Results

The authoritative measured performance report is:

| Query | Before | After | Improvement | Optimization |
|---|---:|---:|---:|---|
| Q1 Monthly revenue split | 105 ms | 100 ms | No meaningful gain | No optimization retained |
| Q2 Delivery lateness cut | 78 ms | **45 ms** | **1.7×** | Covering `ix_fo_late_review` |
| Q3 Seller scorecard | 640 ms | **457 ms** | **1.4×** | Covering `ix_foi_seller_cover` |
| Q4 RFM customer base | 324 ms | **137 ms** | **2.4×** | Covering `ix_fo_status_purchase_cover` |
| Q5 Per-customer average | 914 ms | **284 ms** | **3.2×** | Correlated subquery → window function |

Each query was run multiple times and the documented benchmark uses the warmed-up reading.

---

# Q1: Why It Could Not Be Improved

The monthly revenue query uses:

```text
order_status <> 'canceled'
```

An inequality on the leading indexed column limits the usefulness of the index for seeking.

Additionally:

```text
GROUP BY EXTRACT(YEAR_MONTH FROM purchase_ts)
```

requires grouping work.

A functional index on the extracted date expression was tested, but it ran approximately four times slower because it lost the covering property.

The optimization was therefore reverted.

This is important:

> A performance optimization is successful only if measured performance improves.

---

# Reporting Tables

MySQL does not provide materialized views in the same way as some analytical databases.

The project therefore creates physical reporting tables:

```text
rpt_monthly_kpis
rpt_seller_scorecard
rpt_category_performance
```

The dashboard reads these tables.

## Why?

Instead of making every dashboard interaction re-aggregate:

```text
112K+ line items
+
99K+ orders
+
multiple joins
```

the dashboard can read a small precomputed summary.

This produces a much more responsive BI layer.

---

# Stored Procedure and Transactions

The project creates:

```text
sp_refresh_reporting()
```

The procedure rebuilds the reporting tables.

It uses a transaction:

```text
START TRANSACTION
...
COMMIT
```

and:

```text
ROLLBACK
```

if an error occurs.

## Why?

Without transactional refresh, you could end up with:

```text
monthly KPIs refreshed
seller scorecard refreshed
category table failed
```

and therefore a partially updated dashboard.

A transaction gives the reporting refresh atomicity.

---

# MySQL Event Scheduler

The project creates a MySQL event:

```text
ev_nightly_refresh
```

which runs:

```text
sp_refresh_reporting()
```

on a schedule.

The MySQL event scheduler is disabled by default and must be enabled when scheduling is required.

Alternatives include:

- Airflow
- Cron
- Windows Task Scheduler
- Cloud orchestration
- dbt jobs
- Managed data pipelines

For a local MySQL portfolio project, the MySQL event scheduler is simple and sufficient.

---

# Streamlit Dashboard

The dashboard is built using:

```text
Streamlit
```

with:

```text
Python
Pandas
SQLAlchemy
PyMySQL
```

The dashboard contains:

- Overview KPIs
- Monthly trends
- Seller performance
- Category performance
- Delivery analysis
- Reporting outputs

The key architectural decision is:

> The dashboard reads from `rpt_*` reporting tables rather than directly querying detailed fact tables for every interaction.

---

# Why Streamlit?

Advantages:

- Python-native
- Fast to develop
- Excellent for analytical prototypes
- Easy Pandas integration
- Easy deployment
- Good for internal analytical tools

Alternatives:

### Power BI

Better for enterprise BI and governed reporting.

### Tableau

Excellent visual analytics and BI.

### Looker

Strong semantic modelling and governed analytics.

### React

More UI flexibility but much more development effort.

For this portfolio project, Streamlit is efficient.

For enterprise PwC-style implementation, the BI tool would be chosen based on the client's technology ecosystem.

---

# SQLAlchemy and PyMySQL

The Python-to-MySQL architecture is:

```text
Streamlit
   ↓
Pandas
   ↓
SQLAlchemy
   ↓
PyMySQL
   ↓
MySQL
```

SQLAlchemy provides database-engine and connection abstraction.

PyMySQL is the MySQL driver.

Example architecture:

```python
URL.create(
    drivername="mysql+pymysql",
    username=args.user,
    password=args.password,
    host=args.host,
    port=int(args.port),
    database=args.db,
)
```

`URL.create()` safely handles passwords containing special characters such as:

```text
@
:
/
#
%
```

Database passwords should never be committed to Git.

---

# Caching

The Streamlit dashboard uses caching to avoid unnecessary repeated work.

Conceptually:

```text
@st.cache_resource
```

is used for reusable resources such as the database engine.

```text
@st.cache_data(ttl=600)
```

is used for query results.

A TTL of:

```text
600 seconds
```

means:

```text
10 minutes
```

This reduces:

- Repeated database connection creation
- Repeated execution of identical queries
- Dashboard latency

---

# Python Statistical Analysis

`scripts/analysis.py` uses:

```text
Pandas
SciPy
Matplotlib
SQLAlchemy
PyMySQL
```

The Python layer creates analytical figures including:

1. Lateness vs review
2. Cohort retention heatmap
3. Category Pareto
4. Freight vs distance

It also calculates statistical relationships using:

```python
scipy.stats
```

---

# Why SQL + Python?

The project intentionally separates responsibilities.

## SQL is used for:

- Data transformation
- Joins
- Aggregation
- Window functions
- Warehouse modelling
- Reporting tables
- Data-quality checks
- Query performance

## Python is used for:

- Statistical analysis
- Correlation
- Exploratory analysis
- Visualization
- Dashboarding

Doing everything in Pandas would work, but it would not demonstrate the warehouse/SQL architecture.

Doing everything in SQL would make statistical and visualization workflows less convenient.

Therefore:

```text
SQL + Python
```

is a practical division of responsibilities.

---

# Technology Stack

```text
Data:
Olist Brazilian E-Commerce Public Dataset

Preprocessing:
Python
Pandas

Database:
MySQL 8
InnoDB

Warehouse:
Star Schema
Fact tables
Dimension tables
Surrogate keys

SQL:
CTEs
Recursive CTEs
Window functions
ROW_NUMBER
RANK
NTILE
LAG
PERCENT_RANK
CASE
HAVING
Aggregations
Date functions
Haversine

Quality:
Automated SQL validation suite

Statistics:
SciPy
Pearson
Spearman

Visualization:
Matplotlib

Dashboard:
Streamlit

Python DB layer:
SQLAlchemy
PyMySQL
```

---

# Architecture

```text
data/raw/*.csv
        │
        ▼
scripts/prep_csvs.py
        │
        ▼
data/clean/*.csv
        │
        │ LOAD DATA LOCAL INFILE
        ▼
┌──────────────────────┐
│ MySQL STAGING        │
│ stg_*                │
│ all TEXT             │
└──────────┬───────────┘
           │
           │ 04_transform.sql
           ▼
┌────────────────────────────────────┐
│ MYSQL STAR SCHEMA                  │
│                                    │
│ Dimensions:                        │
│ dim_date                           │
│ dim_customer                       │
│ dim_seller                         │
│ dim_product                        │
│ dim_geography                      │
│                                    │
│ Mapping:                           │
│ map_customer_id                    │
│                                    │
│ Facts:                             │
│ fact_orders                        │
│ fact_order_items                   │
│ fact_payments                      │
└───────────────┬────────────────────┘
                │
        ┌───────┼────────┐
        ▼       ▼        ▼
     Quality Analytics Performance
        │       │        │
        │       │        └── EXPLAIN ANALYZE
        │       │             Indexes
        │       │             Window rewrite
        │       │
        │       ├── Retention
        │       ├── Cohorts
        │       ├── RFM
        │       ├── Delivery
        │       ├── Revenue
        │       ├── Sellers
        │       ├── Freight
        │       └── Payments
        │
        ▼
data_quality_checks
                │
                ▼
       Reporting Tables
          rpt_*
                │
        ┌───────┴────────┐
        ▼                ▼
 Python/SciPy       Streamlit
 Matplotlib         Dashboard
```

---

# Repository Structure

```text
olist-analytics-warehouse/
│
├── data/
│   ├── raw/
│   │   └── Olist source CSVs
│   └── clean/
│       └── LOAD DATA-safe CSVs
│
├── sql/
│   ├── 01_create_staging.sql
│   ├── 02_load_data.sql
│   ├── 03_create_warehouse.sql
│   ├── 04_transform.sql
│   ├── 05_data_quality.sql
│   ├── 06_analysis_retention.sql
│   ├── 07_analysis_delivery.sql
│   ├── 08_analysis_revenue_sellers.sql
│   ├── 09_performance_tuning.sql
│   └── 10_summary_tables.sql
│
├── scripts/
│   ├── prep_csvs.py
│   ├── analysis.py
│   └── make_fake_olist.py
│
├── dashboard/
│   └── app.py
│
├── docs/
│   ├── findings.md
│   ├── performance.md
│   └── figures/
│
├── README.md
├── README_RESULTS.md
└── README_SETUP.md
```

---

# SQL File Responsibilities

## `01_create_staging.sql`

Creates:

```text
9 staging tables
```

All columns are intentionally text-like.

Important detail:

The original product source has a misspelled column:

```text
lenght
```

The staging layer preserves the source spelling so `LOAD DATA` column mapping is not accidentally shifted.

The script drops/recreates the `olist` database, so scripts should be run in numeric order.

---

## `02_load_data.sql`

Loads the nine cleaned CSVs using:

```sql
LOAD DATA LOCAL INFILE
```

and prints row counts.

Expected:

```text
customers              99,441
geolocation          1,000,163
orders                 99,441
order_items           112,650
payments              103,886
reviews                99,224
products               32,951
sellers                 3,095
category_translation       71
```

---

## `03_create_warehouse.sql`

Creates:

```text
5 dimensions
3 fact tables
1 customer mapping table
1 data-quality audit table
```

The critical modelling decision is the `customer_unique_id` grain.

---

## `04_transform.sql`

Transforms:

```text
staging TEXT
→
typed star schema
```

It:

- Generates `dim_date`
- Aggregates geography
- Builds `dim_customer`
- Builds `map_customer_id`
- Builds `dim_seller`
- Builds `dim_product`
- Loads `fact_orders`
- Derives delivery measures
- Loads `fact_order_items`
- Loads `fact_payments`
- Rolls item/payment/review measures onto orders

It is designed to be idempotent.

---

## `05_data_quality.sql`

Runs the 14-check validation suite.

Expected outcome:

```text
14/14 PASS
```

---

## `06_analysis_retention.sql`

Covers:

- Repeat customers
- Cohort retention
- RFM
- Time to second order
- Median purchase gap

---

## `07_analysis_delivery.sql`

Covers:

- Delivery funnel
- Lateness buckets
- Review impact
- Repeat purchase after late first order
- Revenue opportunity sizing
- State-level lateness analysis

---

## `08_analysis_revenue_sellers.sql`

Covers:

- Monthly revenue
- New vs returning revenue
- MoM growth
- Category Pareto
- Seller scorecard
- Seller concentration
- Freight vs distance
- Freight vs weight
- Payment behaviour

Techniques include:

```text
LAG
running SUM window frames
RANK
PERCENT_RANK
HAVING
Haversine
```

---

## `09_performance_tuning.sql`

Contains:

- Baseline queries
- `EXPLAIN ANALYZE`
- Index creation
- After-optimization queries
- Index footprint analysis
- Correlated-subquery benchmark
- Window-function rewrite benchmark

---

## `10_summary_tables.sql`

Creates:

```text
rpt_monthly_kpis
rpt_seller_scorecard
rpt_category_performance
```

plus:

```text
sp_refresh_reporting()
ev_nightly_refresh
```

---

# How to Run

## Prerequisites

- MySQL 8.0+
- Python 3.9+
- MySQL client
- Dataset in `data/raw/`

MySQL 8.0 is required because the project uses:

- CTEs
- Window functions
- Recursive CTEs
- `EXPLAIN ANALYZE`

MySQL 5.7 does not provide the required functionality.

---

## Install Python dependencies

```bash
pip install pandas sqlalchemy pymysql matplotlib scipy streamlit
```

---

## Prepare CSVs

```bash
python scripts/prep_csvs.py --raw data/raw --out data/clean
```

---

## Enable local file loading

```bash
mysql -u root -p -e "SET GLOBAL local_infile = 1;"
```

---

## Run the pipeline

```bash
mysql --local-infile=1 -u root -p < sql/01_create_staging.sql
mysql --local-infile=1 -u root -p < sql/02_load_data.sql
mysql -u root -p < sql/03_create_warehouse.sql
mysql -u root -p < sql/04_transform.sql
mysql -u root -p < sql/05_data_quality.sql
```

The data-quality checks should pass.

---

## Run analysis

```bash
mysql -u root -p < sql/06_analysis_retention.sql
mysql -u root -p < sql/07_analysis_delivery.sql
mysql -u root -p < sql/08_analysis_revenue_sellers.sql
mysql -u root -p < sql/09_performance_tuning.sql
mysql -u root -p < sql/10_summary_tables.sql
```

---

## Generate figures

```bash
python scripts/analysis.py --password YOURPASS
```

---

## Start dashboard

```bash
streamlit run dashboard/app.py
```

---

# Windows PowerShell Workflow

For the Windows environment used during development:

```powershell
cd "E:\NIT ROURKELA\PROJECTS\olist-analytics-warehouse"

.\.venv\Scripts\Activate.ps1

python scripts\prep_csvs.py --raw data/raw --out data/clean

Get-Content .\sql\01_create_staging.sql | mysql --local-infile=1 -u root -p
Get-Content .\sql\02_load_data.sql | mysql --local-infile=1 -u root -p
Get-Content .\sql\03_create_warehouse.sql | mysql --local-infile=1 -u root -p
Get-Content .\sql\04_transform.sql | mysql --local-infile=1 -u root -p
Get-Content .\sql\05_data_quality.sql | mysql --local-infile=1 -u root -p
Get-Content .\sql\06_analysis_retention.sql | mysql --local-infile=1 -u root -p
Get-Content .\sql\07_analysis_delivery.sql | mysql --local-infile=1 -u root -p
Get-Content .\sql\08_analysis_revenue_sellers.sql | mysql --local-infile=1 -u root -p
Get-Content .\sql\09_performance_tuning.sql | mysql --local-infile=1 -u root -p
Get-Content .\sql\10_summary_tables.sql | mysql --local-infile=1 -u root -p

python scripts\analysis.py --password "YOUR_MYSQL_PASSWORD"

streamlit run dashboard\app.py
```

---

# Verify MySQL Independently

```powershell
mysql -u root -p -e "USE olist; SELECT COUNT(*) FROM fact_orders;"
```

Expected:

```text
99441
```

Check tables:

```powershell
mysql -u root -p -e "USE olist; SHOW TABLES;"
```

Check reporting table:

```powershell
mysql -u root -p -e "USE olist; SELECT COUNT(*) FROM rpt_monthly_kpis;"
```

---

# Dataset Download

The dataset is the:

**Brazilian E-Commerce Public Dataset by Olist**

Manual:

Search Kaggle for:

```text
Brazilian E-Commerce Public Dataset by Olist
```

Publisher:

```text
olistbr
```

Download and unzip the nine CSV files into:

```text
data/raw/
```

CLI option:

```bash
pip install kaggle
kaggle datasets download -d olistbr/brazilian-ecommerce -p data/raw --unzip
```

Do not commit the raw CSVs.

The repository should contain:

```text
code
+
SQL
+
documentation
```

rather than approximately 120 MB of source data.

---

# Business Findings

The completed pipeline produces the following headline findings.

## 1. Repeat purchase is very low

Person-level repeat purchase rate:

```text
≈ 3.12%
```

This means the vast majority of customers in the available Olist history do not place another order within the observed period.

---

## 2. Delivery timing strongly affects review scores

Orders delivered early receive substantially higher review scores.

Examples:

```text
10+ days early
≈ 4.32 / 5

More than 14 days late
≈ 1.73 / 5
```

Pearson correlation:

```text
r ≈ -0.291
```

with:

```text
n ≈ 93,607
```

and a highly significant p-value in the original analysis.

This demonstrates a strong relationship between delivery delay and customer satisfaction.

---

## 3. Review score does not strongly predict repeat purchase

Observed repeat rates:

```text
5★ → 3.23%
4★ → 2.86%
3★ → 2.93%
2★ → 2.94%
1★ → 3.10%
```

Chi-square:

```text
7.82
```

Degrees of freedom:

```text
4
```

p-value:

```text
0.098
```

The relationship is not statistically significant at the conventional 5% threshold and is not monotonic.

---

## 4. Late first orders are associated with lower repeat purchase

Observed:

```text
On-time first order → 3.13%
Late first order    → 2.56%
```

Chi-square:

```text
≈ 7.57
```

p-value:

```text
≈ 0.006
```

This is an observed association, not causal proof.

---

## 5. The commercial repeat-revenue opportunity is small in this dataset

The theoretical estimated recoverable revenue is approximately:

```text
R$6,235
```

or:

```text
≈ 0.04% of GMV
```

This is gross revenue, not margin.

---

## 6. Late delivery is concentrated among a small seller group

Approximately:

```text
103 / 2,970 sellers
≈ 3.5%
```

account for about half of late deliveries.

This indicates that the problem is concentrated rather than uniformly distributed.

---

## 7. Delivery estimates are heavily padded

Examples:

```text
São Paulo:
Promised ≈ 18.8 days
Actual   ≈ 8.3 days

Rondônia:
Promised ≈ 38.4 days
Actual   ≈ 18.9 days
```

Nationally, the delivery promise contains an average padding of more than 11 days.

---

## 8. Freight relates more strongly to weight than distance

Observed Pearson correlations:

```text
Distance → freight ≈ 0.408
Weight   → freight ≈ 0.613
```

Therefore weight has the stronger linear association in this dataset.

---

## 9. Revenue is concentrated

Approximately:

```text
18 of 72 categories
```

generate approximately:

```text
80% of revenue
```

This is a classic Pareto concentration pattern.

---

# Recommendations from the Analysis

The original analysis identifies three operational recommendations.

## 1. Reconsider delivery promises

Orders delivered before the promised date have substantially higher average review scores than significantly late orders.

The analysis suggests testing tighter and more accurate delivery estimates where historical performance supports them.

A/B testing would be appropriate before making a broad causal claim.

---

## 2. Focus seller SLA intervention

The dashboard flags high-volume sellers with high late-delivery rates.

The analysis identifies:

```text
10 sellers
```

with:

```text
30+ orders
25%+ late
```

as an `ESCALATE` group.

The broader concentration result shows that a relatively small seller population contributes a large portion of late deliveries.

---

## 3. Do not assume review-triggered retention campaigns will work

The review-score analysis does not show a statistically significant monotonic relationship between first-order review score and repeat purchase.

Therefore a review-score-triggered retention campaign should not be treated as a proven lever based on this dataset alone.

The better next step would be controlled experimentation or a predictive/causal model.

---

# Performance Results

Authoritative documented results:

```text
Q1 Monthly revenue split
105 ms → 100 ms
No meaningful gain

Q2 Delivery lateness cut
78 ms → 45 ms
1.7× improvement

Q3 Seller scorecard
640 ms → 457 ms
1.4× improvement

Q4 RFM customer base
324 ms → 137 ms
2.4× improvement

Q5 Per-customer average
914 ms → 284 ms
3.2× improvement
```

---

# Data Quality Results

The completed real-data pipeline reports:

```text
14/14 data quality checks PASS
```

and:

```text
All 9 source row counts match expected counts
```

The independent spot checks include:

```text
fact_orders = 99,441
```

and:

```text
Average review for 10+ days early ≈ 4.3227
Average review for 2+ weeks late ≈ 1.7288
```

The numbers match the original pipeline output.

---

# Known Limitations

## 1. Correlation is not causation

Late delivery and churn move together, but the same underlying factor could affect both.

Examples:

```text
remote address
bulky item
unreliable seller
```

A controlled experiment is needed to establish causality.

---

## 2. Reviews are self-selected

Approximately 1% of orders have no review.

Unhappy and happy customers may have different probabilities of leaving reviews.

Therefore:

> Absolute review scores are not a perfectly unbiased population measure.

Relative comparison across delivery buckets is more defensible.

---

## 3. Two-year observation window

The dataset covers a limited historical period.

Later cohorts have less time to produce repeat purchases.

Therefore cohort retention is truncated at 12 months and should be interpreted with right-censoring in mind.

---

## 4. No cost data

The:

```text
R$6,235
```

opportunity estimate is gross revenue.

There is no complete:

```text
COGS
delivery cost
margin
```

information.

Therefore it is an upper-bound revenue estimate rather than profit.

---

## 5. Payment/order reconciliation is not exact

`payment_value` and `order_value` do not always reconcile exactly because of:

- vouchers
- instalment rounding

The quality suite therefore allows a tolerance of approximately:

```text
1%
```

---

## 6. Geographic distance is approximate

The Haversine distance uses ZIP-prefix centroids.

It is not:

```text
exact customer address
```

or:

```text
actual road distance
```

---

## 7. RFM segments are heuristic

RFM categories are business rules.

They are not:

```text
machine-learning clusters
```

---

# Alternatives and Why These Choices Fit

| Current choice | Alternative | Why current choice fits |
|---|---|---|
| MySQL 8 | PostgreSQL | Accessible locally and supports required analytical SQL |
| MySQL 8 | Snowflake | Cloud-scale alternative |
| Star schema | Snowflake schema | Simpler BI querying |
| Staging as TEXT | Direct typed load | Safer separation of ingestion and transformation |
| Full refresh | Incremental ETL | Dataset is historical/static for this project |
| RFM rules | K-means | Easier to interpret for business users |
| Haversine | Road-distance API | No routing data is available |
| Streamlit | Power BI | Faster Python-native implementation |
| Streamlit | Tableau | Less setup for this project |
| Reporting tables | Materialized views | MySQL does not provide the same native materialized-view approach |
| SQL statistics | Python/SciPy | Rich statistical ecosystem |
| Window functions | Correlated subqueries | Better measured full-throughput performance |
| Targeted indexes | Index every column | Lower storage/write overhead |
| ZIP centroid | Exact geocoding | Source data does not provide exact addresses |

---

# Why MySQL Instead of Oracle?

The project uses MySQL 8.0.

The analytical SQL concepts demonstrated here are broadly transferable:

```text
CTEs
Window functions
RANK
NTILE
Running totals
Aggregations
```

These concepts transfer to:

```text
Oracle
PostgreSQL
Snowflake
BigQuery
```

Where MySQL requires dialect-specific workarounds, the project demonstrates the reasoning.

Examples:

```text
No direct MEDIAN convenience
No native materialized views in the same form
No FULL OUTER JOIN
```

Understanding database-specific limits is itself an important engineering skill.

---

# Why This Solution Fits the Project

The project choices form a coherent architecture:

```text
MySQL
→ accessible + sufficient analytical SQL

Staging
→ safe ingestion

Star schema
→ clear analytical model

Surrogate keys
→ stable warehouse relationships

Data-quality tests
→ reproducibility and trust

SQL window functions
→ efficient analytical logic

Python
→ statistical analysis + visualization

Summary tables
→ fast dashboard queries

Streamlit
→ fast analytics application

Indexes
→ measured performance improvements
```

The correct interview phrasing is not:

> "This is the best technology."

It is:

> "This was the best fit for the requirements and constraints of this project."

---

# What I Would Build Next

## 1. Incremental loading

Instead of:

```text
full refresh
```

move to:

```text
daily/incremental ingestion
```

using:

```text
watermark
+
upsert
+
ON DUPLICATE KEY UPDATE
```

or a CDC-based approach.

---

## 2. Slowly Changing Dimension Type 2

Implement SCD Type 2 for sellers to preserve historical seller performance states.

For example:

```text
Seller A
Jan → Good
Apr → Watch
Jul → Escalate
```

The warehouse would preserve those historical versions rather than only the current state.

---

## 3. Predictive churn model

Build a logistic regression or other predictive model for:

```text
is_repeat_customer
```

using features such as:

```text
delivery delay
review score
order value
distance
seller quality
category
freight
```

This would rank the drivers against each other rather than evaluating one variable at a time.

---

## 4. Orchestration

For production:

```text
Airflow
dbt
cloud orchestration
```

could replace manual SQL execution and MySQL events.

---

## 5. Production monitoring

Add:

- Pipeline failure alerts
- Data-quality alerts
- Freshness monitoring
- Row-count anomaly detection
- Query-performance monitoring
- Dashboard health checks

---

# PwC Interview Preparation

The project is especially useful for interviews involving:

```text
SQL
Data Analytics
Data Engineering
BI
ETL
Data Modelling
Business Intelligence
Statistics
Performance Optimization
```

The most important idea is:

> **Correct business analysis starts with correct grain and correct keys.**

The most important statistical idea is:

> **Correlation can identify an important relationship, but it does not establish causation.**

The most important engineering idea is:

> **Measure performance with actual execution plans and timings rather than assuming an optimization works.**

---

# Most Important PwC Questions

## Tier 1 — Must Prepare

1. Explain the entire project.
2. What was the `customer_id` vs `customer_unique_id` issue?
3. Explain your star schema.
4. What is the grain of each fact table?
5. Why did you use staging tables?
6. How did you validate data quality?
7. How did you calculate repeat customers?
8. Does your analysis prove causality?
9. How did you optimize SQL performance?
10. What is a covering index?

## Tier 2

11. Explain window functions.
12. Explain RFM.
13. Explain cohort analysis.
14. Explain Haversine distance.
15. Why Streamlit and Python instead of Power BI?

---

# PwC Question: Explain Your Project

### Strong Answer

> "I built an end-to-end analytics warehouse using the Olist Brazilian e-commerce dataset. I first cleaned the raw CSVs using Python and Pandas and loaded them into MySQL staging tables. I then transformed them into a star schema with dimension and fact tables. After that, I implemented automated data-quality checks, retention, delivery, revenue and seller analytics using SQL window functions and CTEs. I used Python and SciPy for statistical analysis and Matplotlib for analytical visualizations. Finally, I created pre-aggregated reporting tables and built a Streamlit dashboard on top of them. I also optimized important queries using indexes, covering indexes and a window-function rewrite."

---

# PwC Question: What Was the Most Challenging Part?

### Strong Answer

> "The most important challenge was understanding the customer keys. `customer_id` represents an order-level customer record, while `customer_unique_id` represents the actual person. If I had used `customer_id` for retention analysis, I would have misclassified customers and distorted the repeat rate. I therefore built `dim_customer` at the unique-person grain and created a mapping table from `customer_id` to the warehouse customer key."

---

# PwC Question: What Is Grain?

### Strong Answer

> "Grain defines what one row represents. In my project, `fact_orders` has one row per order, `fact_order_items` has one row per order line, `fact_payments` has one row per payment leg, and `dim_customer` has one row per unique customer."

---

# PwC Question: Why Star Schema?

### Strong Answer

> "Because the project is analytical rather than transactional. A star schema makes business entities and measures easy to understand, reduces complex repeated joins, and works well with BI tools."

---

# PwC Question: Why Not One Single Table?

### Strong Answer

> "Because the source tables have different grains. An order can contain multiple items and multiple payments. Joining those tables directly can create row multiplication and double-count revenue. Separate fact tables preserve grain and prevent incorrect aggregation."

---

# PwC Question: Why Staging Tables?

### Strong Answer

> "I used staging as a raw landing layer. I intentionally loaded everything as text first so malformed values would not immediately break typed ingestion. I then performed explicit conversions and validation during transformation."

---

# PwC Question: Why Not Directly Load Into Typed Tables?

### Strong Answer

> "Direct typed loading couples ingestion with validation. If one malformed date or numeric field appears, the load can fail or silently coerce values. Staging separates ingestion from transformation and makes data issues visible."

---

# PwC Question: What Is Idempotency?

### Strong Answer

> "An idempotent pipeline produces the same result when executed multiple times. My transformation truncates the target warehouse tables before rebuilding them, so rerunning it does not duplicate data."

---

# PwC Question: What Is a Surrogate Key?

### Strong Answer

> "A surrogate key is a warehouse-generated identifier, such as `customer_sk`. It is separate from the source business identifier and makes warehouse relationships stable and efficient."

---

# PwC Question: Why Use `customer_unique_id`?

### Strong Answer

> "Because it represents the actual customer across orders. `customer_id` is effectively order-level in this dataset, so using it would give an incorrect view of retention."

---

# PwC Question: What Is a Fact Table?

### Strong Answer

> "A fact table stores business events and measurable metrics at a defined grain. In my project, orders, order items and payments are represented as facts."

---

# PwC Question: What Is a Dimension?

### Strong Answer

> "A dimension provides descriptive context around facts, such as customer, product, seller, geography and date."

---

# PwC Question: Why Separate `fact_orders` and `fact_order_items`?

### Strong Answer

> "Because their grains differ. An order can contain multiple line items. Combining them would either lose detail or create duplicated order-level measures."

---

# PwC Question: What Is a Window Function?

### Strong Answer

> "A window function calculates across related rows without collapsing the rows like GROUP BY. I used functions such as ROW_NUMBER, RANK, NTILE, LAG and PERCENT_RANK."

---

# PwC Question: Explain ROW_NUMBER in Your Project

### Strong Answer

> "I used it to assign an order sequence to each customer and to select the earliest review when an order had multiple reviews."

---

# PwC Question: Difference Between WHERE and HAVING?

### Strong Answer

> "WHERE filters rows before aggregation, while HAVING filters groups after aggregation."

Example:

```sql
WHERE order_status <> 'canceled'
```

versus:

```sql
HAVING COUNT(*) >= 30
```

---

# PwC Question: Difference Between RANK and ROW_NUMBER?

### Strong Answer

> "ROW_NUMBER assigns a unique sequence even when values tie. RANK assigns the same rank to ties and leaves gaps afterward."

---

# PwC Question: What Is NTILE?

### Strong Answer

> "NTILE divides ordered rows into approximately equal-sized buckets. I used NTILE(5) to create quintile scores for RFM segmentation."

---

# PwC Question: What Is LAG?

### Strong Answer

> "LAG accesses a previous row within a window. I used it to retrieve previous-month revenue and calculate month-over-month growth."

---

# PwC Question: What Is RFM?

### Strong Answer

> "RFM stands for Recency, Frequency and Monetary value. It segments customers based on how recently they purchased, how frequently they purchase and how much they spend."

---

# PwC Question: Why Use Median?

### Strong Answer

> "Customer purchase intervals can be highly skewed. Median is less affected by extreme values than average and therefore better represents a typical customer."

---

# PwC Question: Why Pearson and Spearman?

### Strong Answer

> "Pearson measures linear association, while Spearman measures rank-based monotonic association. Using both gives a more robust understanding of the relationship."

---

# PwC Question: Does Your Analysis Prove Late Delivery Causes Churn?

### Strong Answer

> "No. It establishes an observed association, not causality. Other variables such as geography, seller performance, product characteristics and distance could confound the relationship. A controlled experiment or causal model would be needed to establish causality."

This is one of the most important answers to memorize.

---

# PwC Question: Why Haversine Instead of Euclidean Distance?

### Strong Answer

> "Because latitude and longitude represent points on Earth's surface. Haversine estimates great-circle distance and is therefore more appropriate than treating latitude and longitude as ordinary Cartesian coordinates."

---

# PwC Question: Is Haversine Actual Delivery Distance?

### Strong Answer

> "No. It is straight-line geographic distance between ZIP-code centroids. Actual road distance would require routing data or a mapping API."

---

# PwC Question: Why Use COALESCE for Missing Categories?

### Strong Answer

> "I don't want to drop revenue just because category information is missing. I assign an `unknown` category so the transaction remains analytically visible."

---

# PwC Question: How Did You Validate the Data?

### Strong Answer

> "I created an automated 14-check SQL quality suite covering row counts, duplicates, orphan records, unresolved products, negative prices, timestamp validity, review score ranges, payment reconciliation and date-dimension coverage."

---

# PwC Question: Why Allow a Payment Mismatch Tolerance?

### Strong Answer

> "Because payment totals and order value don't necessarily reconcile exactly due to vouchers and rounding. A tolerance-based reconciliation is more realistic than requiring exact equality."

---

# PwC Question: How Did You Improve Performance?

### Strong Answer

> "I used EXPLAIN ANALYZE to establish a baseline, then added targeted indexes, including covering indexes, refreshed optimizer statistics, and rewrote a correlated subquery using a window function."

---

# PwC Question: What Is a Covering Index?

### Strong Answer

> "An index containing all the columns needed by a query, allowing the database to answer the query from the index without repeatedly accessing the base table."

---

# PwC Question: Why Not Create Indexes on Every Column?

### Strong Answer

> "Indexes consume storage and slow inserts and updates. I created them based on actual query access patterns and validated them using EXPLAIN ANALYZE."

---

# PwC Question: What Happened When an Index Made Your Query Slower?

### Strong Answer

> "My first delivery-quality index didn't include all the columns required by the query. MySQL had to perform additional base-table lookups, so the query became slower. I changed the index to make it covering, and the query improved from about 78 milliseconds to 45 milliseconds."

---

# PwC Question: Why Use a Window Function Instead of a Correlated Subquery?

### Strong Answer

> "The window-function approach can calculate the per-customer average in a single analytical operation instead of repeatedly executing a correlated lookup. In my benchmark, the full-throughput version improved from around 914 milliseconds to 284 milliseconds."

---

# PwC Question: Why Summary Tables?

### Strong Answer

> "The dashboard shouldn't repeatedly aggregate the large fact tables. I created reporting tables containing monthly KPIs, seller scorecards and category performance, so the dashboard reads a small precomputed dataset."

---

# PwC Question: Why Not Query Fact Tables Directly From Streamlit?

### Strong Answer

> "It would make dashboard interaction dependent on expensive joins and aggregations. The reporting layer isolates BI workloads from the detailed warehouse."

---

# PwC Question: Why Streamlit?

### Strong Answer

> "It allowed me to quickly turn the analytical outputs into an interactive Python dashboard. For an enterprise implementation, I'd evaluate Power BI, Tableau or Looker depending on the client's environment."

---

# PwC Question: Why Python If SQL Already Does the Analysis?

### Strong Answer

> "SQL is better for warehouse transformations and aggregations. Python provides richer statistical and visualization libraries, so I used SciPy for correlation analysis and Matplotlib for analytical figures."

---

# PwC Question: What Would You Improve for Production?

### Strong Answer

> "I would move from full refresh to incremental loading, probably using a watermark or CDC mechanism. I would add slowly changing dimensions for seller history, orchestration using something like Airflow, stronger monitoring and alerting, automated tests in CI/CD, and potentially a cloud warehouse such as Snowflake, BigQuery or Redshift depending on requirements."

---

# 90-Second Project Explanation

> "I built an end-to-end analytics warehouse using the Olist Brazilian e-commerce dataset, which contains around 100,000 orders across customers, products, sellers, payments, reviews and logistics data.
>
> I started by preprocessing the raw CSV files using Python and Pandas and loading them into MySQL staging tables. I intentionally kept the staging layer as text because I wanted ingestion to be separated from validation and type conversion.
>
> I then designed a star schema with dimensions such as customer, seller, product, geography and date, and fact tables for orders, order items and payments. One of the most important modelling challenges was identifying that `customer_id` and `customer_unique_id` have different meanings. I therefore modelled customers at the actual-person level and created a mapping between the source customer key and the warehouse customer key.
>
> After transformation, I implemented an automated data-quality suite covering completeness, uniqueness, referential integrity, validity, reconciliation and date consistency.
>
> For analytics, I used SQL CTEs and window functions such as ROW_NUMBER, RANK, NTILE, LAG and PERCENT_RANK to perform cohort retention, RFM segmentation, delivery analysis, revenue analysis, seller scoring and Pareto analysis.
>
> I also used Python, Pandas, SciPy and Matplotlib to analyze correlations such as delivery delay versus review score and freight versus distance or product weight.
>
> Finally, I optimized important queries using EXPLAIN ANALYZE, covering indexes and a window-function rewrite, and created summary reporting tables that power a Streamlit dashboard.
>
> The main business finding was that delivery delays were strongly associated with lower review scores, while the person-level repeat purchase rate was only around 3.12%. I also found that a relatively small group of sellers accounted for a large share of late deliveries.
>
> The important limitation is that these are observational relationships, so I would not claim causality without an experiment or causal analysis."

---

# Technical Challenge Story

> "One of the biggest challenges was understanding the customer identifiers in the source data. Initially, it looked like `customer_id` could be used directly for retention analysis. However, after checking the cardinality, I found that `customer_id` had almost as many values as orders, while `customer_unique_id` represented the actual customer.
>
> If I had grouped by `customer_id`, I would have treated the same person as different customers and obtained a misleading retention result. I solved this by creating `dim_customer` at the `customer_unique_id` grain and a mapping table from `customer_id` to the warehouse surrogate key.
>
> I then added a data-quality test specifically to make sure the person-level customer count remained lower than the order-level customer-key count. This made the modelling assumption testable rather than just documenting it."

---

# Performance Optimization Story

> "For performance optimization, I first established baselines using MySQL EXPLAIN ANALYZE rather than assuming an index would improve performance.
>
> I then added targeted covering indexes based on the actual query access patterns. One interesting result was that my first delivery-quality index actually made the query slower because it didn't contain all the columns needed by the query, resulting in additional table lookups.
>
> After changing the index to provide better coverage, the query improved from roughly 78 milliseconds to 45 milliseconds.
>
> I also compared a correlated subquery with a window-function implementation for calculating customer-level averages. The full-throughput benchmark improved from around 914 milliseconds to 284 milliseconds.
>
> The main lesson was that performance optimization should be measurement-driven rather than based on assumptions."

---

# Business Insight Story

> "The most important business insight was the relationship between delivery reliability and customer experience.
>
> Orders delivered significantly later than the promised date had substantially lower average review scores, and the correlation between delivery delay and review score was negative.
>
> I then checked whether this translated into repeat purchasing. Customers whose first order arrived late had a lower observed repeat rate than customers whose first order arrived on time.
>
> However, I would be careful not to describe this as causal because this is observational data. Factors such as seller quality, geography, product type and distance could affect both delivery performance and customer retention.
>
> Operationally, the analysis also showed that late deliveries were concentrated among a relatively small group of sellers, which makes seller-level SLA monitoring a more targeted intervention than treating the entire marketplace as equally problematic."

---

# Final Mental Model

Before the interview, remember the project in eight layers:

```text
1. RAW DATA
   ↓
2. CLEANING
   ↓
3. STAGING
   ↓
4. STAR SCHEMA
   ↓
5. DATA QUALITY
   ↓
6. ANALYTICS
   ↓
7. PERFORMANCE
   ↓
8. DASHBOARD
```

Inside analytics:

```text
CUSTOMERS
   ├── Retention
   ├── Cohorts
   └── RFM

DELIVERY
   ├── Funnel
   ├── Lateness
   └── Reviews

REVENUE
   ├── MoM
   ├── AOV
   └── Pareto

SELLERS
   ├── Scorecard
   ├── Ranking
   └── Concentration

LOGISTICS
   ├── Haversine
   ├── Freight
   └── Weight

PAYMENTS
   ├── Payment mix
   └── Installments
```

## Three concepts to remember above everything else

### 1. Data modelling

> **Correct business analysis starts with correct grain and correct keys.**

### 2. Statistics

> **Correlation can identify an important relationship, but it does not establish causation.**

### 3. Engineering

> **Measure performance with actual execution plans and timings instead of assuming an optimization works.**

---

# Project Completion Checklist

```text
[✓] Repository cloned
[✓] Dataset downloaded
[✓] Raw CSVs placed in data/raw
[✓] Python virtual environment created
[✓] Python dependencies installed
[✓] MySQL installed / initialized
[✓] LOCAL INFILE enabled
[✓] Staging layer created
[✓] CSV data loaded
[✓] Warehouse created
[✓] Transformations completed
[✓] Data quality checks passed
[✓] Retention analysis completed
[✓] Delivery analysis completed
[✓] Revenue & seller analysis completed
[✓] Performance tuning completed
[✓] Summary tables created
[✓] Python analysis completed
[✓] Figures generated
[✓] Streamlit dashboard running
```

---

# Final Result

The completed system provides a reproducible end-to-end analytics workflow:

```text
Raw Data
   ↓
Warehouse
   ↓
Validation
   ↓
Business Analysis
   ↓
Statistical Analysis
   ↓
Performance Optimization
   ↓
Reporting Layer
   ↓
BI Dashboard
```

The dashboard and Python analysis read from the warehouse/reporting layer rather than directly from raw CSV files.

---

## Data Attribution

Data:

**Olist Store / Brazilian E-Commerce Public Dataset**

License:

**CC BY-NC-SA 4.0**

This is an independent analysis and is not affiliated with or endorsed by Olist.

---

## Interview Takeaway

If an interviewer remembers only one thing about this project, it should be that it combines:

```text
Data Engineering
+
Data Modelling
+
SQL Analytics
+
Statistics
+
Performance Engineering
+
BI
```

rather than being only a dashboard or only a collection of SQL queries.
