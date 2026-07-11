# Domain 2: Ingest and Transform Data

This domain covers 30-35% of the DP-700 exam. It focuses on how to bring data into Fabric and transform it using various compute engines.

## 🗺️ Sub-Topics

1. [[05_Batch_Ingestion_and_Transformation]]
   - Copy Activity vs. Dataflows Gen2 vs. Notebooks.
   - The T-SQL `COPY` statement.
   - Dimensional Modeling & Joins.
   
2. [[06_Streaming_and_Real_Time]]
   - Real-Time Intelligence & Eventstreams.
   - Routing and transforming streaming events.
   - Data Activator for triggering actions based on events.
   
3. [[07_KQL_and_Real_Time_Intelligence]]
   - Storing and querying data in KQL Databases (Eventhouses).
   - Kusto Query Language (KQL) syntax and aggregations.
   - Update Policies & Materialized Views.
   
4. [[08_PySpark_and_Notebooks]]
   - Spark Architecture (Driver, Executors, Workers).
   - PySpark DataFrames and Spark SQL.
   - Delta tables.

---
> *Tip: For the exam, know exactly when to choose Dataflows Gen2 (low code/Power Query) vs. Data Pipelines (orchestration & high throughput copying) vs. Notebooks (complex data engineering).*
