# 07. KQL & Real-Time Intelligence

**KQL Databases** (hosted within an Eventhouse) are the primary storage engine in Fabric for real-time data. They excel at ingesting and querying massive volumes of structured, semi-structured (JSON), and unstructured log data.

Data in a KQL database is automatically partitioned by ingestion time, making queries over recent time periods incredibly fast.

## 1. Querying with Kusto Query Language (KQL)

KQL is designed to be highly readable. A query starts with a table name, followed by pipes (`|`) to pass the data through a chain of operators.

```kusto
// Example: Get the average bid price of stocks over the last 5 minutes
stock
| where ["time"] > ago(5m)
| summarize avgPrice = avg(todouble(bidPrice)) by symbol
| project symbol, avgPrice
```

### Common Operators
- `where`: Filters rows.
- `project`: Selects, renames, or creates calculated columns.
  ```kusto
  Bikestream | project StreetName = Street, ["Empty Docks"] = No_Empty_Docks
  ```
- `summarize`: Aggregates data (similar to SQL `GROUP BY`).
- `take` or `limit`: Returns a specified number of rows.

## 2. Optimizing KQL Queries

Because KQL databases often hold billions of rows, query optimization is heavily tested.

- **Limit Results:** Always use `limit` or `take` when exploring data to prevent the engine from returning millions of rows to the UI.
- **Time Filters First:** Always place your time filters (`where timestamp > ago(1d)`) as early in the pipeline as possible to prune partitions immediately.
- **Join Ordering:** When joining tables, **always put the smaller table on the left side** of the `join` operator. KQL keeps the left table in memory.

```kusto
// GOOD: Small dimension table on the left, large fact table on the right
VendorInfo        
| join kind=inner TaxiTrips on vendor_id

// BAD: Large fact table on the left (Will consume massive memory)
TaxiTrips         
| join kind=inner VendorInfo on vendor_id
```

## 3. Materialized Views

Materialized views pre-compute and store the results of aggregations in the background. If you have a dashboard that constantly queries the total sales per day, querying a materialized view is significantly faster and uses less compute than running the aggregation against the raw table every time.

```kusto
.create materialized-view TripsByVendor on table TaxiTrips
{
	TaxiTrips
	| summarize trips = count(), total_revenue = sum(fare_amount) 
      by vendor_id, pickup_date = format_datetime(pickup_datetime, "yyyy-MM-dd")
}
```

## 4. KQL Functions

You can encapsulate complex logic into reusable functions, treating them like parameterized views.

```kusto
// Create the function
.create-or-alter function trips_by_min_passenger(num_passengers:long)
{
    TaxiTrips
    | where passenger_count >= num_passengers
    | project trip_id, pickup_datetime
}

// Call the function
trips_by_min_passenger(3) | count
```

## 5. Update Policies (Transform on Ingestion)

Update Policies are automated triggers. When raw data is ingested into Table A, an Update Policy automatically runs a KQL query to transform that data and saves the output to Table B. It acts like an ETL pipeline natively built into the database.

![[Pasted image 20260501170242.png]]

### Critical Limitations of Update Policies:
- The query **cannot** perform cross-eventhouse queries or access external data.
- **Join Restrictions:** If the update policy query uses a `join` operator, you **must disable streaming ingestion** on all upstream tables involved. 
- **Caution:** An incorrect or failing query in an Update Policy can block data ingestion into the source table entirely!

---

## 🧠 Knowledge Check

Test your understanding of KQL & RTI:

1. **Scenario:** You are writing a KQL query to join a massive `ServerLogs` table (1 billion rows) with a small `ServerRegions` reference table (50 rows). How should you structure the `join` operator for best performance?
   - *Answer:* Put the small table on the left: `ServerRegions | join kind=inner ServerLogs on server_id`.

2. **Question:** You want to transform JSON log data exactly at the moment it is ingested into your KQL database, extracting specific fields into a cleaner destination table. What KQL feature should you use?
   - *Answer:* An **Update Policy**.

3. **Question:** What is the rule regarding Update Policies that contain `join` operators?
   - *Answer:* You must disable "streaming ingestion" on the upstream tables.

---
**Next Topic:** [[08_PySpark_and_Notebooks]]
