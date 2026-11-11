# Brazilian E-Commerce Analytics Dashboard (Microsoft Fabric, SQL & Power BI)

### Dashboard 
<img width="1415" height="798" alt="Executive sales and logistic_main" src="https://github.com/user-attachments/assets/1c41cadf-61e6-46ac-9bf4-5284204a0ecb" />

## Project Overview

This project focuses on analyzing the Brazilian E-Commerce Public Dataset by Olist to uncover insights into sales performance, customer behavior, product performance, payment preferences, and delivery operations.

The project demonstrates an end-to-end data analytics workflow using Microsoft Fabric, SQL, PySpark, and Power BI. It follows the Medallion Architecture (Bronze, Silver, and Gold) to ingest, clean, transform, model, and analyze raw e-commerce data before building an interactive Power BI dashboard.

The final dashboard provides a consolidated view of business performance, helping stakeholders understand revenue trends, product category performance, geographic sales distribution, and delivery operations.

## Business Objective

The primary objective of this project is to transform raw e-commerce data into meaningful business insights that can support data-driven decision-making.

The analysis focuses on:

- Evaluating overall sales and revenue performance.
- Identifying top-performing product categories.
- Analyzing sales performance across Brazilian states.
- Understanding customer purchasing behavior.
- Evaluating payment preferences.
- Monitoring order fulfillment and delivery performance.
- Identifying operational opportunities to improve business performance.

## Steps followed

### Phase 1: Lakehouse Provisioning and Data Ingestion

- Step 1 : Created a Microsoft Fabric workspace for the Brazilian E-Commerce Analytics project.
- Step 2 : Provisioned a Lakehouse named `Olist_LH`.
- Step 3 : Downloaded and uploaded the raw CSV datasets into the Lakehouse Files section.

Raw datasets included:

1. Orders
2. Customers
3. Products
4. Sellers
5. Order Items
6. Order Payments
7. Product Category Translation
8. Geolocation
9. Order Reviews

Note: Order Reviews was excluded from the current analytical model.

- Step 4 : Used Dataflows Gen2 to perform initial data preparation.
- Step 5 : Standardized column names and data types.
- Step 6 : Checked for missing values and inconsistent date formats.
- Step 7 : Validated date sequences while preserving genuine null date values.

### Phase 2: Data Transformation Using Medallion Architecture

Implemented the Medallion Architecture to organize data into three layers:

Bronze Layer

- Stored the original raw datasets.
- Preserved source data for traceability and future transformations.

Silver Layer

- Cleaned and standardized the raw datasets.
- Handled missing values and data inconsistencies.
- Prepared datasets for analytical modeling.
- Transformed date columns and validated data types.

Gold Layer

Created the final analytical tables used for SQL analysis and Power BI reporting.

| Gold Table    | Record Count |
| ------------- | ------------ |
| fact_orders   | 99,441       |
| fact_sales    | 112,650      |
| dim_products  | 32,951       |
| dim_customers | 99,441       |
| dim_sellers   | 3,095        |
| dim_date      | 774          |

### Phase 3: SQL Analysis Using Microsoft Fabric Warehouse

Used SQL to query the Gold and Silver tables and answer key business questions.

1\. Total Sales and Revenue

```
SELECT
    SUM(price) AS total_revenue,
    SUM(freight_value) AS total_freight,
    SUM(total_item_cost) AS total_sales_including_freight
FROM gold.fact_sales;
```

2\. Revenue by Product Category

```
SELECT
    p.product_category_name_english,
    SUM(f.price) AS total_sales
FROM gold.fact_sales AS f
INNER JOIN gold.dim_products AS p
    ON f.product_id = p.product_id
GROUP BY p.product_category_name_english
ORDER BY total_sales DESC;
```

3\. Sales Performance by Brazilian State

```
SELECT
    c.customer_state,
    SUM(s.price) AS total_sales,
    COUNT(DISTINCT o.order_id) AS total_orders
FROM gold.fact_sales AS s
INNER JOIN gold.fact_orders AS o
    ON s.order_id = o.order_id
INNER JOIN gold.dim_customers AS c
    ON o.customer_id = c.customer_id
GROUP BY c.customer_state
ORDER BY total_sales DESC;
```

4\. Order Status Distribution

```
SELECT
    order_status,
    COUNT(*) AS total_orders,
    ROUND(
        COUNT(*) * 100.0 / SUM(COUNT(*)) OVER (),
        2
    ) AS percentage_of_orders
FROM gold.fact_orders
GROUP BY order_status
ORDER BY total_orders DESC;
```

5\. Average Delivery Days by Delivery Status

```
SELECT
    delivery_status,
    COUNT(*) AS total_orders,
    AVG(CAST(delivery_days AS FLOAT)) AS average_delivery_days
FROM gold.fact_orders
WHERE order_status = 'Delivered'
  AND delivery_days IS NOT NULL
GROUP BY delivery_status
ORDER BY total_orders DESC;
```

6\. Payment Preferences Analysis

```
SELECT
    payment_type,
    COUNT(*) AS total_payment_records,
    SUM(payment_value) AS total_payment_value,
    AVG(payment_value) AS average_payment_value
FROM silver.order_payments
GROUP BY payment_type
ORDER BY total_payment_value DESC;
```

### Phase 4: Data Modeling in Power BI

Connected the Gold tables to Power BI and developed a relational data model.

Created the following relationships:

- `dim_products[product_id]` → `fact_sales[product_id]`
- `dim_sellers[seller_id]` → `fact_sales[seller_id]`
- `dim_customers[customer_id]` → `fact_orders[customer_id]`
- `dim_date[date]` → `fact_orders[purchase_date]`
- `fact_orders[order_id]` → `fact_sales[order_id]`

Relationship configuration:

- One-to-many relationships where applicable.
- Active relationships.
- Single-direction filtering.

### Phase 5: DAX Measures and Calculations

Created DAX measures to calculate key performance indicators and support interactive analysis.

Sales Performance Measures

```
Total Sales =
SUM(fact_sales[price])
```

```
Total Freight =
SUM(fact_sales[freight_value])
```

```
Total Sales Including Freight =
[Total Sales] + [Total Freight]
```

```
Total Orders =
DISTINCTCOUNT(fact_orders[order_id])
```

```
Total Customers =
DISTINCTCOUNT(dim_customers[customer_unique_id])
```

```
Total Items Sold =
COUNTROWS(fact_sales)
```

```
Average Item Price =
DIVIDE(
    [Total Sales],
    [Total Items Sold]
)
```

```
Average Order Value =
DIVIDE(
    [Total Sales],
    [Total Orders]
)
```

Order and Delivery Measures

```
Delivered Orders =
CALCULATE(
    [Total Orders],
    fact_orders[order_status] = "Delivered"
)
```

```
Canceled Orders =
CALCULATE(
    [Total Orders],
    fact_orders[order_status] = "Canceled"
)
```

```
Cancellation Rate =
DIVIDE(
    [Canceled Orders],
    [Total Orders]
)
```

```
Average Delivery Days =
CALCULATE(
    AVERAGE(fact_orders[delivery_days]),
    fact_orders[order_status] = "Delivered"
)
```

```
Late Deliveries =
CALCULATE(
    [Total Orders],
    fact_orders[delivery_status] = "Delivered Late"
)
```

```
On-Time Deliveries =
CALCULATE(
    [Total Orders],
    fact_orders[delivery_status] = "Delivered On Time"
)
```

```
On-Time Delivery % =
DIVIDE(
    [On-Time Deliveries],
    [Delivered Orders]
)
```

Time Intelligence Measures

```
Previous Month Sales =
CALCULATE(
    [Total Sales],
    DATEADD(dim_date[date], -1, MONTH)
)
```

```
Month-over-Month Growth % =
DIVIDE(
    [Total Sales] - [Previous Month Sales],
    [Previous Month Sales]
)
```

### Phase 6: Dashboard Development in Power BI

Designed a one-page interactive dashboard titled Executive Sales & Logistics Overview.

The dashboard provides a consolidated view of sales performance, customer activity, product categories, geographic distribution, and delivery operations.

Key Performance Indicators (KPIs)

- Total Sales
- Total Orders
- Total Customers
- Average Order Value

Visualizations

- Line Chart: Monthly Sales Trend.
- Bar Chart: Sales by Product Category.
- Bar Chart: Shipping Cost by State.
- Bar Chart: Revenue by State.
- Map or Column Chart: Average Delivery Days by State.
- Donut Chart: Orders by Delivery Status (On-Time vs. Late).

Interactive Features

- Year slicer.
- Customer City slicer.
- Interactive filtering across dashboard visuals.

Dashboard Design

- Used a blue and navy color theme.
- Organized KPI cards at the top of the dashboard.
- Positioned sales trends and geographic analysis in the middle.
- Placed product and delivery performance breakdowns in the lower section.
- Applied consistent formatting to improve readability and usability.

# Snapshot of Dashboard

### Power BI Service
<img width="1915" height="940" alt="image" src="https://github.com/user-attachments/assets/35778c9c-ae45-42bf-9085-8c2eb8155f33" />

### Power BI Desktop
<img width="1914" height="998" alt="image" src="https://github.com/user-attachments/assets/ca348e62-01df-4022-ab36-5723c92e6fef" />

# Dashboard Insights

### Sales & Commercial Performance

Revenue & Volume Scale: The business achieved $14M in Total Sales across 99K total orders and 96K unique customers, maintaining an Average Order Value (AOV) of $137.

Trajectory & Seasonality: Monthly revenue scaled aggressively through 2017, surging past $1.0M in late 2017 before stabilizing around $0.8M–$0.9M per month through mid-2018.

Order Cancellations: Cancellations remained strictly controlled at 625 orders, representing less than 1% of total transaction volume.

### Product Analysis

Top Revenue Driver: Health Beauty leads all product categories, generating $1.26M in total revenue.

Key Contributors: Watches Gifts ($1.21M) and Bed Bath Table ($1.04M) round out the top three categories, each exceeding the million-dollar threshold.

Mid-Tier Momentum: Sports Leisure ($0.99M) and Computers Accessories ($0.91M) contribute substantial revenue, followed by Furniture Decor ($0.73M), Cool Stuff ($0.64M), and Housewares ($0.63M).

### Geographic & State Performance

São Paulo Dominance: São Paulo (SP) is the leading market, generating $5.2M in total revenue and accounting for $0.72M in shipping expenditure.

Southeast Concentration: Rio de Janeiro (RJ) ($1.82M sales / $0.31M freight) and Minas Gerais (MG) ($1.59M sales / $0.27M freight) rank second and third, concentrating a significant share of commercial activity in the Southeast region.

Regional Freight Disparity: Freight expenditure is highest in the south-central states (SP, RJ, MG, RS, PR), while peripheral states account for lower absolute shipping totals due to smaller order volumes.

### Logistics & Delivery Efficiency

Strong Fulfillment Punctuality: 92% (88.64K orders) of shipments arrive on time, while only 8% (7.83K orders) are delivered late.

Severe Northern Delivery Lag: Northern and remote states experience the longest delivery lead times, led by Roraima (RR) at 29 days, Amapá (AP) at 27 days, Amazonas (AM) at 26 days, Alagoas (AL) at 25 days, and Pará (PA) at 24 days.

Transit Discrepancies: Industrialized southern states experience faster deliveries, while shipments to Northern and Northeastern regions can take nearly a month from purchase to delivery.

# Strategic Recommendations

Establish Northern Fulfillment Partnerships: Partner with local third-party logistics (3PL) providers and regional sorting hubs in Northern cities such as Manaus and Belém to reduce the 24–29 day delivery lead times in RR, AP, and AM.

Capitalize on High-Performing Categories: Bundle complementary products with leading categories (Health Beauty, Watches Gifts, and Bed Bath Table) to increase the average order value above the current $137 baseline.

Targeted Regional Freight Subsidies: Test subsidized or flat-rate shipping promotions in secondary regions such as RS, PR, BA, and SC to encourage customer conversion and improve geographic market penetration.

Target Late-Delivery Hotspots: Conduct root-cause audits of carrier routes responsible for the 8% late deliveries (7.83K orders). Establish tighter service-level agreements (SLAs) to improve delivery reliability and protect customer retention.

# Technical Skills Demonstrated

- Microsoft Fabric: Lakehouse management, Dataflows Gen2, PySpark notebooks, SQL Analytics Endpoint.
- SQL: Data exploration, multi-table joins, aggregations, window functions, and business reporting.
- Power BI: Star-schema semantic modeling, relationships, interactive visual design, and Treemap visualization.
- DAX: KPI development, CALCULATE, DIVIDE, DISTINCTCOUNT, and filter context.
- Data Analysis: Operational lead-time tracking, revenue trend identification, and regional logistics optimization.
