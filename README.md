# Fashion-Trends-Business-Analysis
Analyzed fashion retail sales data (2018–2022) to identify growth drivers, discount dependency, inventory risk signals, seasonality exposure, and pricing optimization opportunities using Python and SQL.

## Business Questions

1. Which product categories drive the majority of revenue growth, and which are structurally declining?
2. What proportion of sales comes from discounted items, and how does discounting impact revenue efficiency?
3. Which products or categories show persistently weak demand signals and pose inventory risk?
4. How strong is seasonality in sales, and which categories are most exposed to seasonal risk?
5. Where should pricing or assortment decisions be adjusted to improve revenue without increasing volume?

## Methodology

- Python (pandas, numpy) for data cleaning and feature engineering
- SQLite for analytical SQL queries
- Revenue engineered as price × sales count
- Quantile-based price bands to identify relative pricing power
- Demand and seasonality proxies used where lifecycle data was unavailable

## Key Insights

### **Q1. Revenue Growth: Invest vs Decline - Which product categories should leadership invest in, and which are structurally declining?**

Approach

Calculated year-over-year revenue by category
Computed 2018–2022 CAGR to identify structural growth
Measured absolute revenue contribution to assess real business impact

````
WITH yearly_revenue AS (
  SELECT category, year, SUM(revenue) AS total_revenue
  FROM sales
  GROUP BY category, year
)
SELECT
  category,
  year,
  total_revenue,
  LAG(total_revenue) OVER (PARTITION BY category ORDER BY year) AS prev_year
FROM yearly_revenue;
````

**Growth Drivers**
- Shirts and Jackets show strong multi-year growth and positive revenue contribution.
- Skirts and Shoes exhibit structural decline post-peak.

![]((https://github.com/minu-nayan/QoQ-Sales-and-Performance-Analysis/blob/main/Sales%20Analytics%20SS%201.png)

### **Q2. Discount Dependency & Revenue Efficiency - What proportion of sales comes from discounted items, and how does discounting impact overall revenue efficiency?**

Approach

Segmented sales into discounted vs full-price
Compared revenue per unit
Tracked discount-driven revenue share over time

````
SELECT
  discount_flag,
  SUM(sales_count) AS units_sold,
  SUM(revenue) / SUM(sales_count) AS revenue_per_unit
FROM sales
GROUP BY discount_flag;
````

**Discounting**
- ~75–85% of revenue is discount-driven, but discount dependency has declined over time.
- Discounted items show ~5% lower revenue per unit.

### **Q3. Inventory Risk: Weak Demand Signals - Which products or categories consistently underperform in terms of sell-through and tie up inventory capital?**

Approach

Used demand proxies due to lack of lifecycle inventory data
Flagged products weak across: Sales volume, Revenue contribution, Stock-out frequency
Identified SKUs weak on ≥2 dimensions

````
SELECT
  product_id,
  category,
  sales_count AS units_sold,
  revenue,
  out_of_stock_times
FROM sales;
````

**Inventory Risk**
- Bottom-quartile products exhibit weak demand signals across volume and stockout frequency.
- Inventory should be reallocated toward high-velocity SKUs.

### **Q4. Seasonality & Demand Volatility - How strong is seasonality in our sales, and which categories are most exposed to seasonal risk?**

Approach

Aggregated monthly revenue by category
Measured relative volatility using coefficient of variation (std / mean)

````
SELECT
  category,
  month,
  SUM(revenue) AS monthly_revenue
FROM sales
GROUP BY category, month;
````
**Seasonality**
- Blouses and Jackets show high month-to-month volatility.
- Shirts and Skirts exhibit stable demand suitable for baseline inventory.

### **Q5. Pricing & Assortment Optimization - Where should pricing or assortment decisions be adjusted to improve revenue without increasing volume?**

Approach

Created quantile-based price tiers to capture relative pricing power
Compared revenue vs volume across price bands by category

````
SELECT
  category,
  price_band,
  SUM(sales_count) AS volume,
  SUM(revenue) AS revenue
FROM sales
GROUP BY category, price_band;
````

**Pricing & Assortment**
- Higher relative price tiers generate disproportionate revenue in select categories.
- Premiumization offers revenue uplift without volume growth.

## 🔗 Connect With Me

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-blue?logo=linkedin)](https://www.linkedin.com/in/minu-nayan/)
[![Email](https://img.shields.io/badge/Email-Contact-red?logo=gmail)](mailto:minunayan08@gmail.com)
