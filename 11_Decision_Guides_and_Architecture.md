# 11. Decision Guides & Architecture

One of the most critical skills tested in the DP-700 exam is knowing *which* Fabric item or compute engine to choose for a specific architectural scenario. Microsoft loves to test you on edge cases where two tools *could* work, but one is clearly better.

## 1. Choosing a Data Store

Fabric offers multiple ways to store data in OneLake. Choosing the right one depends on the nature of your data and the primary skillset of the team querying it.

| Data Store | Primary Data Type | Best Persona | Key Features / Best For |
| :--- | :--- | :--- | :--- |
| **Lakehouse** | Unstructured, semi-structured (JSON, CSV), and structured (Delta). | Data Engineer / Data Scientist (Python/Spark users) | Building a medallion architecture (Bronze, Silver, Gold). Supports machine learning models. |
| **Warehouse** | Strictly structured data only. | SQL Developer / BI Team (T-SQL users) | Full transactional T-SQL support. Best for teams migrating from an on-premise SQL Server. |
| **KQL Database (Eventhouse)** | Streaming, high-velocity, time-series, or heavy log data. | Real-Time Analysts | Extremely fast ingestion. Optimized for time-based querying and telemetry. |

![[Pasted image 20260501173106.png]]

## 2. Choosing an Ingestion & Transformation Tool

Depending on the volume of data, the frequency of updates, and the persona doing the work:

| Tool | Persona / Skillset | Best Use Case |
| :--- | :--- | :--- |
| **Data Pipelines (Copy Activity)** | Data Engineer | Moving terabytes of raw data quickly without transforming it in flight. Highly scalable. |
| **Dataflows Gen2** | Citizen Developer / BI Dev | Visually transforming data using Power Query (M). Good for low-to-medium data volumes. |
| **Notebooks (PySpark)** | Data Engineer / Data Scientist | Complex transformations, handling nested JSON, applying machine learning, and highly scalable programmatic logic. |

![[Pasted image 20260501173835.png]]

## 3. Shortcutting vs Mirroring

Both features allow you to bring external data into Fabric without building complex ETL pipelines, but they operate very differently.

- **Shortcuts (Virtual Links):** 
  - Pointing to data in an external data lake (like Amazon S3 or ADLS Gen2) *without* moving the data. 
  - It's a live, active link. If the data is deleted in S3, the shortcut breaks. 
  - Best for: Querying massive external data lakes where copying the data would be too expensive or slow.
  
- **Mirroring (Continuous Replication):** 
  - Continuously replicating data from an external transactional database (like Cosmos DB, Azure SQL, or Snowflake) into OneLake.
  - Fabric automatically converts the source data into open Delta Parquet format in near real-time.
  - Best for: Offloading analytical queries from an operational database. You query the mirrored Delta tables in Fabric, completely protecting the source transactional database from heavy analytical query loads.

---

## 🧠 Knowledge Check

Test your Architectural Decision Making skills (Exam Style):

1. **Scenario:** Your company has a team of Data Analysts who only know T-SQL. They need to build a highly structured, relational database for reporting. They do not know Python and do not want to deal with unstructured data. Which Fabric item should they create?
   - *Answer:* A **Warehouse**. It provides a fully relational, T-SQL native experience perfectly suited for SQL developers.

2. **Scenario:** You need to ingest 10 million rows of data per day from an operational Azure SQL Database into Fabric for analytics. You want the data in Fabric to be as up-to-date as possible (near real-time) without writing any ETL code or putting a heavy query load on the operational database. What feature should you use?
   - *Answer:* **Mirroring**. It will continuously replicate the Azure SQL database into OneLake Delta tables automatically.

3. **Scenario:** You need to perform complex data cleansing on a dataset containing deeply nested JSON structures and arrays, and then apply a custom Python machine learning library to the data. Which transformation tool must you use?
   - *Answer:* **Notebooks (PySpark)**. Data Pipelines and Dataflows Gen2 cannot natively run custom Python ML libraries or handle deeply nested JSON arrays as efficiently as PySpark.

---
**Return to Syllabus:** [[prep-content]]
