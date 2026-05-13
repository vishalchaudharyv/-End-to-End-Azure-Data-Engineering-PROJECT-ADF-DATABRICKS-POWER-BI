# Retail Analytics End-to-End Data Engineering Project

## Project Overview

This project demonstrates an **end-to-end Retail Analytics Data Pipeline** built using **Azure Data Factory (ADF), Azure Blob Storage, Azure Databricks, PySpark, and Delta Lake**.

The pipeline extracts retail data from multiple sources:

- **SQL Server** (Products, Stores, Transactions)
- **GitHub API (JSON)** (Customer data)

Raw data is stored in a data lake, transformed using Databricks, and modeled into analytics-ready datasets using the **Medallion Architecture (Bronze → Silver → Gold)**.

---

## Architecture Diagram

```text
SQL Server + GitHub API
        ↓
Azure Data Factory (ADF)
        ↓
Azure Blob Storage (Bronze Layer)
        ↓
Azure Databricks (PySpark)
        ↓
Silver Layer (Cleaned & Transformed Data)
        ↓
Gold Layer (Business Analytics)
```

---

# Technologies Used

- Azure Data Factory (ADF)
- Azure Blob Storage
- Azure Databricks
- PySpark
- Delta Lake
- SQL Server
- GitHub REST API
- SQL

---

# Data Sources

## 1. SQL Server Source

Used SQL Server as structured data source.

### Tables Created

### Products Table

Stores product catalog and pricing information.

Columns:

- product_id
- product_name
- category
- price

---

### Stores Table

Stores store information and locations.

Columns:

- store_id
- store_name
- location

---

### Transactions Table

Stores retail sales transactions.

Columns:

- transaction_id
- customer_id
- product_id
- store_id
- quantity
- transaction_date

---

## 2. GitHub API Source (Customer Data)

Customer data was fetched from a **GitHub-hosted JSON API** using **ADF REST connector**.

### Customer Fields

- customer_id
- first_name
- last_name
- email
- phone
- city
- registration_date

---

# Medallion Architecture

This project follows the **Medallion Architecture** design pattern.

---

## Bronze Layer (Raw Data)

Raw data from all source systems is stored **as-is** in **Parquet format**.

### Storage Paths

```text
/bronze/transaction/
/bronze/product/
/bronze/store/
/bronze/customer/
```

### Purpose

- Preserve raw source data
- Enable data traceability
- Support reprocessing

---

## Silver Layer (Cleaned & Transformed Data)

Used **Azure Databricks + PySpark** to clean and transform data.

### Processing Steps

- Mounted Azure Blob Storage
- Read Bronze data
- Converted data types
- Removed duplicates
- Joined all datasets
- Created derived business columns

### Business Logic

Calculated total sales amount:

```python
total_amount = quantity * price
```

### Final Joined Data Includes

- Customer details
- Product details
- Store details
- Transaction details

### Silver Storage Path

```text
/mnt/retail_project/silver/
```

### Delta Table Created

```sql
CREATE TABLE retail_silver_cleaned
USING DELTA
LOCATION '/mnt/retail_project/silver/'
```

---

## Gold Layer (Business Analytics)

Created aggregated business-ready dataset for analytics.

### Metrics Generated

- Total Quantity Sold
- Total Sales Amount
- Number of Transactions
- Average Transaction Value

### Aggregation Dimensions

- Transaction Date
- Product
- Category
- Store
- Location

### Gold Storage Path

```text
/mnt/retail_project/gold/
```

### Delta Table Created

```sql
CREATE TABLE retail_gold_sales_summary
USING DELTA
LOCATION '/mnt/retail_project/gold/'
```

---

# Pipeline Workflow

## Step 1: Data Ingestion with Azure Data Factory

ADF pipelines were used to extract data from:

- SQL Server
- GitHub REST API

Data was copied into Azure Blob Storage Bronze layer.

---

## Step 2: Mount Blob Storage in Databricks

```python
dbutils.fs.mount(
  source="wasbs://retail@retailproject.blob.core.windows.net",
  mount_point="/mnt/retail_project",
  extra_configs={
   "fs.azure.account.key.retailproject.blob.core.windows.net":"<access_key>"
  }
)
```

---

## Step 3: Data Cleaning & Transformation

Used PySpark to:

- Read Parquet files
- Standardize schemas
- Join multiple datasets
- Generate calculated columns
- Save Delta tables

---

## Step 4: Business Aggregation

Performed group-by aggregations to create Gold analytics layer.

---

# Project Folder Structure

```text
retail-analytics-project/
│
├── README.md
├── sql/
│   └── source_data.sql
├── adf_pipeline/
├── databricks_notebooks/
├── screenshots/
│
└── retail_project/
    ├── bronze/
    ├── silver/
    └── gold/
```

---

# Key Learnings

- End-to-end data pipeline development
- Multi-source data ingestion
- REST API integration in ADF
- Azure Blob Storage handling
- Databricks transformations using PySpark
- Delta Lake implementation
- Data modeling with Medallion Architecture
- Business analytics aggregation

---

# Future Improvements

- Incremental data loading
- Parameterized ADF pipelines
- Data quality validation
- Power BI dashboard integration
- CI/CD deployment automation

---

# Author

**Vishal Chaudhary**  
Azure Data Engineer  
Pune, India
