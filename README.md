This project demonstrates an **end-to-end data engineering pipeline** built using **Azure Databricks**, focusing on **incremental data loading**, **data transformation**, and **optimized Delta Lake storage**. The workflow simulates a retail business dataset that includes customers, products, orders, and regional information.

The main goal is to build a **scalable, fault-tolerant, and efficient data pipeline** that supports both full and incremental refreshes while maintaining data integrity across layers.

---

## 🏗️ Architecture

**Tools & Technologies**

* **Azure Databricks** – Core compute and transformation engine
* **Azure Data Lake Storage (ADLS Gen2)** – Data source & sink
* **PySpark** – ETL transformations and business logic
* **Delta Lake** – For ACID-compliant storage and incremental updates
* **Parquet Format** – Input data format for optimized columnar storage

**Pipeline Flow**

```
Source Data (Parquet) 
      ↓
Bronze Layer (Raw Ingestion)
      ↓
Silver Layer (Cleansed & Joined Data)
      ↓
Gold Layer (Aggregated Business Insights)
```

---

## 🧩 Dataset Description

The dataset consists of several Parquet files representing different entities:

| Table/File                                             | Description                                                    | Example Fields                                                        |
| ------------------------------------------------------ | -------------------------------------------------------------- | --------------------------------------------------------------------- |
| `customers_first.parquet` & `customers_second.parquet` | Contains customer records with incremental changes             | customer_id, name, email, region_id                                   |
| `products_first.parquet` & `products_second.parquet`   | Product catalog with price and category updates                | product_id, product_name, price                                       |
| `orders_first.parquet` & `orders_second.parquet`       | Transactional sales data, used to calculate revenue and growth | order_id, customer_id, product_id, order_date, quantity, total_amount |
| `regions.parquet`                                      | Geographic mapping table for customer and sales segmentation   | region_id, region_name                                                |

The files are structured to simulate **incremental updates** (first load vs. subsequent load).

---

## ⚙️ Incremental Data Loader

The **incremental data loader** in Databricks is designed to automatically detect new, modified, or deleted records and apply only necessary updates to the Delta table.

### **Logic Overview**

1. **Initial Load**

   * The first run ingests all records from the `_first.parquet` files.
   * Data is written to the Bronze and Silver Delta tables.

2. **Incremental Load**

   * On subsequent runs, `_second.parquet` files are compared with the existing Delta tables.
   * New and updated records are upserted using `MERGE INTO`.
   * Deleted or inactive records can be flagged based on business rules.

### **Sample Pseudo-Code**

```python
from delta.tables import DeltaTable

delta_table = DeltaTable.forPath(spark, "/mnt/silver/orders")

updates_df = spark.read.parquet("/mnt/raw/orders_second.parquet")

delta_table.alias("t") \
    .merge(
        updates_df.alias("s"),
        "t.order_id = s.order_id"
    ) \
    .whenMatchedUpdateAll() \
    .whenNotMatchedInsertAll() \
    .execute()
```

---

## 📊 Key Outcomes

✅ Established an **automated pipeline** from raw to analytics-ready data
✅ Implemented **incremental load logic** for efficient refresh cycles
✅ Designed **clean Delta schemas** to ensure query performance
✅ Produced **joined analytical views** combining orders, customers, and products
✅ Enabled future scalability for Power BI or Synapse integration

---

## 📂 Repository Structure

```
├── Databricks ETE Project.dbc          # Databricks notebooks export
├── data/
│   ├── customers_first.parquet
│   ├── customers_second.parquet
│   ├── products_first.parquet
│   ├── products_second.parquet
│   ├── orders_first.parquet
│   ├── orders_second.parquet
│   └── regions.parquet
└── README.md
```

Would you like me to make this README **GitHub-SEO optimized** (e.g., adding searchable keywords like “Databricks incremental load,” “Azure Delta Lake pipeline,” “PySpark ETL project”)?
That can help the repo rank higher when recruiters search for “Azure Databricks projects.”
