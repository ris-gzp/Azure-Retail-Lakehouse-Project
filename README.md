<div align="center">

<h1>🛍️ Azure Retail Lakehouse — Medallion Architecture</h1>

<p>
  <img src="https://img.shields.io/badge/Azure%20Data%20Factory-0078D4?style=for-the-badge&logo=microsoftazure&logoColor=white" />
  <img src="https://img.shields.io/badge/Azure%20Databricks-FF3621?style=for-the-badge&logo=databricks&logoColor=white" />
  <img src="https://img.shields.io/badge/ADLS%20Gen2-0078D4?style=for-the-badge&logo=microsoftazure&logoColor=white" />
  <img src="https://img.shields.io/badge/Delta%20Lake-003366?style=for-the-badge&logo=databricks&logoColor=white" />
  <img src="https://img.shields.io/badge/PySpark-E25A1C?style=for-the-badge&logo=apachespark&logoColor=white" />
  <img src="https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black" />
  <img src="https://img.shields.io/badge/Azure%20SQL-CC2927?style=for-the-badge&logo=microsoftsqlserver&logoColor=white" />
  <img src="https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white" />
</p>

<p>
  <img src="https://img.shields.io/badge/Architecture-Medallion%20%28Bronze%20→%20Silver%20→%20Gold%29-orange?style=flat-square" />
  <img src="https://img.shields.io/badge/Sources-Multi--Source%20%28SQL%20%2B%20REST%20API%29-blue?style=flat-square" />
  <img src="https://img.shields.io/badge/Storage%20Format-Delta%20Lake-003366?style=flat-square" />
  <img src="https://img.shields.io/badge/Stores%20Covered-5%20Cities-brightgreen?style=flat-square" />
</p>

</div>

---

## 📌 Project Overview

A production-grade **retail analytics lakehouse** built on Microsoft Azure, implementing the **Medallion Architecture** (Bronze → Silver → Gold) to unify multi-source retail data — transactions from **Azure SQL Database** and customer records from a **REST API** — into a single analytics platform. Raw data is orchestrated via **Azure Data Factory**, transformed with **PySpark on Databricks**, stored in **Delta Lake** format on **ADLS Gen2**, and surfaced through **Power BI** dashboards covering sales performance, store analytics, and product intelligence across 5 cities in India.

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────────────────────────┐
│                        MULTI-SOURCE DATA PIPELINE                               │
└─────────────────────────────────────────────────────────────────────────────────┘

  ┌─────────────────────────┐        ┌──────────────────────────────────────────┐
  │      DATA SOURCES        │        │         Azure Data Factory               │
  │                          │        │         (pipelinemigration)              │
  │  ┌───────────────────┐  │        │                                          │
  │  │  Azure SQL DB      │──┼───────▶│  ① Copy: transactions  → Parquet        │
  │  │  • transactions    │  │        │  ② Copy: products      → Parquet        │
  │  │  • products        │  │        │  ③ Copy: stores        → Parquet        │
  │  │  • stores          │  │        │  ④ Copy: customers API → Parquet        │
  │  └───────────────────┘  │        │     (sequential with dependency chain)   │
  │                          │        └──────────────────┬───────────────────────┘
  │  ┌───────────────────┐  │                           │
  │  │  REST API (HTTP)   │──┼───────────────────────────┘
  │  │  • customers.json  │  │
  │  └───────────────────┘  │
  └─────────────────────────┘

                    ┌──────────────────────────────────────────────┐
                    │          ADLS Gen2  (container: retail)       │
                    │                                              │
                    │  ┌────────────────────────────────────────┐  │
                    │  │  🥉 BRONZE  (Raw Parquet)               │  │
                    │  │  bronze/transaction/  bronze/product/   │  │
                    │  │  bronze/store/        bronze/customer/  │  │
                    │  └───────────────────┬────────────────────┘  │
                    │                      │  PySpark (Databricks)  │
                    │                      ▼                        │
                    │  ┌────────────────────────────────────────┐  │
                    │  │  🥈 SILVER  (Cleaned Delta)             │  │
                    │  │  • Type casting & null removal          │  │
                    │  │  • Deduplication per entity             │  │
                    │  │  • String trimming & standardisation    │  │
                    │  │  • 4-table join + total_amount derived  │  │
                    │  └───────────────────┬────────────────────┘  │
                    │                      │  PySpark aggregations  │
                    │                      ▼                        │
                    │  ┌────────────────────────────────────────┐  │
                    │  │  🥇 GOLD  (Aggregated Delta)            │  │
                    │  │  • total_quantity_sold per product      │  │
                    │  │  • total_sales_amount per store/date    │  │
                    │  │  • number_of_transactions               │  │
                    │  │  • average_transaction_value            │  │
                    │  └───────────────────┬────────────────────┘  │
                    └──────────────────────┼──────────────────────┘
                                           │
                    ┌──────────────────────▼──────────────────────┐
                    │               Power BI                       │
                    │  Sales trends • Store performance            │
                    │  Product analytics • Customer insights       │
                    └─────────────────────────────────────────────┘
```

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| **Orchestration** | ![ADF](https://img.shields.io/badge/Azure%20Data%20Factory-0078D4?style=flat-square&logo=microsoftazure&logoColor=white) — sequential Copy activities with dependency chaining |
| **Sources** | ![Azure SQL](https://img.shields.io/badge/Azure%20SQL-CC2927?style=flat-square&logo=microsoftsqlserver&logoColor=white) + HTTP REST API (JSON) |
| **Storage** | ![ADLS Gen2](https://img.shields.io/badge/ADLS%20Gen2-0078D4?style=flat-square&logo=microsoftazure&logoColor=white) ![Delta Lake](https://img.shields.io/badge/Delta%20Lake-003366?style=flat-square&logo=databricks&logoColor=white) |
| **Processing** | ![Databricks](https://img.shields.io/badge/Databricks-FF3621?style=flat-square&logo=databricks&logoColor=white) ![PySpark](https://img.shields.io/badge/PySpark-E25A1C?style=flat-square&logo=apachespark&logoColor=white) |
| **Visualisation** | ![Power BI](https://img.shields.io/badge/Power%20BI-F2C811?style=flat-square&logo=powerbi&logoColor=black) |
| **Language** | ![Python](https://img.shields.io/badge/Python-3776AB?style=flat-square&logo=python&logoColor=white) |

---

## ✨ Key Features

- **Medallion Architecture** — Strict Bronze → Silver → Gold layering ensures raw data is always preserved, transformations are auditable, and analytics-ready Gold tables are clean and aggregated
- **Multi-Source Ingestion** — ADF pipeline (`pipelinemigration`) ingests from two heterogeneous sources: Azure SQL Database (transactions, products, stores) and a remote REST API (customers JSON) in a single orchestrated run
- **Sequential Dependency Chain** — ADF activities are chained (`transaction → productdata → store → customerCopyfromapi`) so each copy completes before the next begins, guaranteeing referential integrity at Bronze
- **PySpark Transformations** — Silver layer applies type casting, null removal, deduplication, and string trimming across all four entities; then joins all tables on `product_id`, `store_id`, and `customer_id` with a derived `total_amount` column
- **Delta Lake ACID Storage** — Silver and Gold layers are written in Delta format, enabling schema enforcement, time-travel queries, and safe concurrent reads
- **Business-Ready Gold Layer** — Gold aggregations compute `total_quantity_sold`, `total_sales_amount`, `number_of_transactions`, and `average_transaction_value` grouped by product, store, and date — directly consumable by Power BI
- **Scalable Across Cities** — Retail network spans 5 Indian cities: Mumbai, Delhi, Bangalore, Pune, Chennai — with store-level granularity throughout the pipeline

---

## 📊 Data Model

### Source Tables

| Table | Source | Key Columns |
|---|---|---|
| `transactions` | Azure SQL DB | `transaction_id`, `customer_id`, `product_id`, `store_id`, `quantity`, `transaction_date` |
| `products` | Azure SQL DB | `product_id`, `product_name`, `category`, `price` |
| `stores1` | Azure SQL DB | `store_id`, `store_name`, `location` |
| `customers` | REST API (public dataset) | `customer_id`, `first_name`, `last_name`, `email`, `city`, `registration_date` |

### Product Categories
`Electronics` &nbsp;•&nbsp; `Fitness` &nbsp;•&nbsp; `Accessories` &nbsp;•&nbsp; `Stationery`

### Store Locations
`Mumbai` &nbsp;•&nbsp; `Delhi` &nbsp;•&nbsp; `Bangalore` &nbsp;•&nbsp; `Pune` &nbsp;•&nbsp; `Chennai`

### Gold Aggregation Schema
```
transaction_date  |  product_id  |  product_name  |  category
store_id          |  store_name  |  location
total_quantity_sold  |  total_sales_amount
number_of_transactions  |  average_transaction_value
```

---

## 📁 Project Structure

```
Azure-Retail-Lakehouse-Project/
│
├── databricks_code/
│   └── bronze+silver+gold/
│       └── retail-project-multipletables.ipynb   # Full Bronze→Silver→Gold notebook
│
├── dataset/                                       # ADF dataset definitions
│   ├── AzureSqlTable.json                         # Source: transactions
│   ├── AzureSqlTable1.json                        # Source: products
│   ├── AzureSqlTable2.json                        # Source: stores
│   ├── Json1.json                                 # Source: customers (HTTP API)
│   ├── Parquet1.json                              # Sink: bronze/transaction
│   ├── Parquet2.json                              # Sink: bronze/product
│   ├── Parquet3.json                              # Sink: bronze/store
│   └── Parquet4.json                              # Sink: bronze/customer
│
├── linkedService/                                 # ADF linked service configs
│   ├── AzureDataLakeStorage1.json                 # ADLS Gen2 connection
│   ├── AzureSqlDatabase1.json                     # Azure SQL connection
│   └── HttpServer1.json                           # REST API connection
│
├── pipeline/
│   └── pipelinemigration.json                     # ADF pipeline (4 copy activities)
│
├── factory/
│   └── project2dfrishu.json                       # ADF factory config
│
├── retail project power bi visualization.pbix     # Power BI dashboard
├── publish_config.json
└── README.md
```

---

## 📈 Results & Impact

<table>
  <tr>
    <td align="center" width="25%">
      <h3>4</h3>
      <sub>Heterogeneous data sources unified (SQL DB + REST API) into one lakehouse</sub>
    </td>
    <td align="center" width="25%">
      <h3>3-Layer</h3>
      <sub>Medallion architecture with full lineage from raw Bronze to aggregated Gold</sub>
    </td>
    <td align="center" width="25%">
      <h3>5 Cities</h3>
      <sub>Store-level retail analytics across Mumbai, Delhi, Bangalore, Pune & Chennai</sub>
    </td>
    <td align="center" width="25%">
      <h3>100%</h3>
      <sub>Automated end-to-end pipeline — ingestion, transformation, and reporting</sub>
    </td>
  </tr>
</table>

### Business Insights Delivered
- **Sales Performance** — Revenue trends by product category and store location over time
- **Store Analytics** — Comparative performance of all 5 city stores by transaction volume and revenue
- **Product Intelligence** — Top-selling products by quantity and sales amount across categories
- **Customer Analytics** — Purchase patterns linked to customer registration city and demographics
- **Transaction Metrics** — Average order value and transaction frequency per store and product

---

## 🔄 Pipeline Execution Flow

```
1. ADF pipeline triggered (manual / scheduled)
         ↓
2. Copy Activity ①: Azure SQL transactions → ADLS Bronze (Parquet)
         ↓ (on success)
3. Copy Activity ②: Azure SQL products → ADLS Bronze (Parquet)
         ↓ (on success)
4. Copy Activity ③: Azure SQL stores → ADLS Bronze (Parquet)
         ↓ (on success)
5. Copy Activity ④: REST API customers.json → ADLS Bronze (Parquet)
         ↓
6. Databricks notebook: Bronze Parquet → Silver Delta (clean + join)
         ↓
7. Databricks notebook: Silver Delta → Gold Delta (aggregate KPIs)
         ↓
8. Power BI reads Gold Delta → live dashboards rendered
```

---

<div align="center">

**Built by [Rishu Yadav](https://github.com/ris-gzp)**

[![LinkedIn](https://img.shields.io/badge/LinkedIn-0A66C2?style=flat-square&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/rishuyadav2001/)
[![GitHub](https://img.shields.io/badge/GitHub-181717?style=flat-square&logo=github&logoColor=white)](https://github.com/ris-gzp)
[![Email](https://img.shields.io/badge/Email-D14836?style=flat-square&logo=gmail&logoColor=white)](mailto:ris.gzp@gmail.com)

</div>
