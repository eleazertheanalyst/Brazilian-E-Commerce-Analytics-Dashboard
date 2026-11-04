# Data Preparation Using Microsoft Fabric Dataflows Gen2

## Project: Brazilian E-Commerce Analytics Dashboard

### Overview

Microsoft Fabric Dataflows Gen2 was used to perform data preparation and cleaning for the Brazilian E-Commerce Public Dataset by Olist.

The Dataflow, named `Olist_Dataflow`, connects to the raw datasets stored in the `Olist_LH` Lakehouse and provides a Power Query environment for preparing the data for downstream transformation and analysis.

### Dataflow Configuration

| Component | Configuration |
|---|---|
| Platform | Microsoft Fabric |
| Dataflow | Olist_Dataflow |
| Source Lakehouse | Olist_LH |
| Source Layer | Bronze |
| Transformation Tool | Power Query |
| Data Destination | Lakehouse |

### Source Queries

The Dataflow contains the following source queries:

| Query | Description |
|---|---|
| bronze customers | Customer information |
| bronze geolocation | Brazilian geographic information |
| bronze order_items | Order item and product transaction information |
| bronze order_payments | Customer payment information |
| bronze order_reviews | Customer review information |
| bronze orders | Order and delivery information |
| bronze products | Product attributes |
| bronze category_translation | Product category translations |
| bronze sellers | Seller information |

### Data Cleaning and Preparation

Power Query transformations were applied to prepare the source datasets.

The visible transformations in the `bronze customers` query include:

- **Source and Navigation:** Connected to the source data and selected the relevant table.
- **Column Operations:** Inserted and removed columns where required.
- **Text Standardization:** Applied capitalization to text values.
- **Whitespace Removal:** Used trimming operations to remove unnecessary leading and trailing spaces.
- **Data Type Changes:** Updated column data types.
- **Text Cleaning:** Applied text-cleaning operations to prepare values for analysis.

Additional preparation activities included reviewing missing values, checking date formats, validating date sequences, and preserving genuine null date values.

### Data Destination

The Dataflow is configured with a Lakehouse destination.

The prepared datasets form part of the data preparation workflow within the Microsoft Fabric environment and support subsequent analytical transformations.

### Role in the Medallion Architecture

| Layer | Purpose |
|---|---|
| Bronze | Stores the original raw datasets |
| Silver | Contains cleaned and prepared datasets |
| Gold | Contains analytical fact and dimension tables used for reporting |

Dataflows Gen2 supports the preparation stage of this architecture, while subsequent transformations and analytical table development are handled through the project's downstream Fabric workflow.

### Project Outcome

The Dataflows Gen2 process provides a structured approach to preparing the raw Olist datasets for analysis.

The prepared data supports:

- SQL business analysis.
- Fact and dimension table development.
- Power BI semantic modeling.
- DAX measure development.
- Interactive sales and logistics reporting.

### Technology Used

- Microsoft Fabric Lakehouse
- Microsoft Fabric Dataflows Gen2
- Power Query
- Data cleaning and transformation
- Data type standardization
- Text cleaning and standardization
- Data quality validation

### Evidence

Screenshots of the Dataflow configuration and Power Query transformations can be included in this folder to demonstrate the data preparation process.

**Note:** This documentation describes the Dataflow configuration and visible transformations. It does not represent an exported, executable copy of the original Dataflow.
