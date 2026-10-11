# Olist E-Commerce Sales Analysis

**Business question:** Where is a Brazilian online marketplace's growth stalling, what is hurting customer satisfaction, and where should it expand next?

**Stack:** PostgreSQL (cleaning, views) → Python/pandas (analysis) → Power BI (3-page dashboard) → one-page memo

## Key findings

1. **Growth has stalled.** Monthly orders have held at about 6,100 to 7,100 since January 2018. Revenue peaked in November 2017 at R\$987,765 (+52% vs. October), and August 2018 was about 14% below the May 2018 peak.
2. **Late delivery drives dissatisfaction.** The 8.0% of orders delivered after the estimated date average a 2.57 review score vs. 4.29 for on-time orders. Late rates reach 23.4% in Alagoas, 19.2% in Maranhão and 13.3% in Rio de Janeiro.
3. **Revenue is concentrated, and the northeast is underpenetrated.** São Paulo generates 38.3% of revenue and the top 3 states 63.4%. Bahia, Pernambuco, Ceará, Pará and Maranhão (about 47M residents) sell at roughly 30% to 60% of Paraná's revenue per person, a gap of about R\$1.5M (R\$739K conservative).

**Recommendation:** Expand marketing and seller recruitment in Bahia, Pernambuco and Ceará, and pair it with more realistic delivery estimates and carrier audits in the northeast and Rio de Janeiro.

Read the full one-page memo: [memo/olist_findings.pdf](memo/olist_findings.pdf)

## Dashboard

Power BI, three pages with date, state and category slicers.

| Executive Overview | Geographic & Category | Delivery & Satisfaction |
|---|---|---|
| ![Executive Overview](dashboard/page1_executive_overview.png) | ![Geographic and Category](dashboard/page2_geographic_category.png) | ![Delivery and Satisfaction](dashboard/page3_delivery_satisfaction.png) |

## Approach

1. **Load:** 8 Olist CSVs loaded into PostgreSQL with pandas (`load_data.py`), then data types fixed and primary/foreign keys added (`sql/01_setup.sql`).
2. **Clean and validate:** null, duplicate and integrity checks documented in `sql/02_cleaning_checks.sql`, which also builds two analysis views:
   - `sales_analysis` (one row per order item) for revenue questions.
   - `delivery_review_analysis` (one row per order) for delivery and review questions.
   - Payments are deliberately not joined to items, because 2,961 orders have several payment rows and the join would duplicate revenue. View revenue was validated against the raw table (R\$13,221,498.11).
3. **Analyse:** four business questions in `notebooks/01_analysis.ipynb` (concentration, delivery vs. satisfaction, seasonality, underserved states), each with the reasoning and caveats.
4. **Visualise:** Power BI connected to the PostgreSQL views, plus a state lookup table that links the two data tables.

## Repository structure

```
sql/          setup, cleaning checks and view definitions
notebooks/    Python analysis with findings and caveats
dashboard/    Power BI file and page screenshots
memo/         one-page findings and recommendations (PDF and Markdown)
load_data.py  loads the CSVs into PostgreSQL
```

## How to reproduce

1. Download the [Olist Brazilian E-Commerce dataset](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce) from Kaggle into a `data/` folder (not included in this repo).
2. Create a PostgreSQL database named `ecommerce_analysis`.
3. `pip install -r requirements.txt`, then edit the connection details in `load_data.py` and run it.
4. Run `sql/01_setup.sql`, then `sql/02_cleaning_checks.sql`.
5. Open the notebook (it asks for your database password when run), and point the Power BI file at your local database.

## Caveats

- Revenue is item price excluding freight, for delivered orders only, in Brazilian reais. Q3's trend uses Jan 2017 to Aug 2018, because 2016 is sparse.
- Seasonality is suggestive, not proven: the data has only one November.
- Delivery time and review score are correlated (-0.33), which does not prove causation.
- Revenue per person uses 2022 IBGE Census populations and is a relative index, not real spending. The R\$1.5M gap assumes states could reach Paraná's level, so it is an upper bound.
- The dashboard's late rate includes all delivered orders, while the notebook uses only orders with a review, so figures differ by a few tenths of a percentage point.

## Data

Olist Brazilian E-Commerce Public Dataset (Kaggle). Credit and licence terms are on the dataset page.
