# Dataset Documentation

## Project: Brazilian E-Commerce Analytics Dashboard

### Dataset Overview

This project uses the **Brazilian E-Commerce Public Dataset by Olist**, a publicly available dataset containing information about e-commerce transactions in Brazil.

The dataset provides information about customers, orders, products, sellers, payments, order items, product categories, and geographic locations.

The data was used to analyze sales performance, customer activity, product performance, payment preferences, and delivery operations.

### Dataset Source

**Dataset Name:** Brazilian E-Commerce Public Dataset by Olist

**Source:** [Kaggle – Brazilian E-Commerce Public Dataset by Olist](https://www.kaggle.com/datasets/olistbr/brazilian-ecommerce)

### Original CSV Datasets

The project started with nine raw CSV datasets:

| No. | Dataset | Description |
|---|---|---|
| 1 | Orders | Order records, purchase timestamps, approval dates, and delivery information |
| 2 | Customers | Customer identifiers, location information, city, and state |
| 3 | Products | Product identifiers, categories, dimensions, and weight |
| 4 | Sellers | Seller identifiers and geographic information |
| 5 | Order Items | Products purchased, seller information, prices, freight values, and shipping limits |
| 6 | Order Payments | Payment types, installment information, and payment values |
| 7 | Product Category Translation | Portuguese product category names and their English translations |
| 8 | Geolocation | Brazilian ZIP codes and geographic coordinates |
| 9 | Order Reviews | Customer reviews and ratings associated with orders |

**Note:** Order Reviews was excluded from the current analytical model.

### Data Storage

The original CSV files were uploaded into Microsoft Fabric Lakehouse:

- **Lakehouse:** `Olist_LH`
- **Initial Storage:** Lakehouse Files section
- **Bronze Layer:** Raw datasets stored for subsequent preparation and transformation.

### Dataset Preparation

The raw datasets were prepared using Microsoft Fabric Dataflows Gen2.

The preparation process included:

- Standardizing column names.
- Reviewing and preparing data types.
- Checking for missing values.
- Reviewing inconsistent date formats.
- Validating date sequences.
- Preserving genuine null date values.

### Analytical Tables

The project produced the following Gold analytical tables:

| Table | Description | Record Count |
|---|---|---:|
| fact_orders | Order-level information and delivery performance | 99,441 |
| fact_sales | Order item-level sales and freight information | 112,650 |
| dim_products | Product attributes and category information | 32,951 |
| dim_customers | Customer information and geographic attributes | 99,441 |
| dim_sellers | Seller information | 3,095 |
| dim_date | Date dimension for time-based analysis | 774 |

### Data Usage

The prepared datasets support the following business analyses:

- Sales and revenue performance.
- Product category performance.
- Customer purchasing behavior.
- Geographic sales distribution across Brazilian states.
- Payment preferences.
- Order fulfillment and delivery performance.
- Freight expenditure analysis.

### Important Note

The original CSV files are not included in this repository. They can be downloaded from the original Kaggle source using the link provided above.

The repository contains project documentation, analytical scripts, notebooks, and dashboard artifacts rather than the complete raw dataset.
