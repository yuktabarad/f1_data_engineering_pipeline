# 🏎️ Formula 1 Data Engineering Pipeline — Azure Databricks

<p align="center">
  <img src="https://img.shields.io/badge/Azure%20Databricks-FF3621?style=for-the-badge&logo=databricks&logoColor=white"/>
  <img src="https://img.shields.io/badge/PySpark-E25A1C?style=for-the-badge&logo=apachespark&logoColor=white"/>
  <img src="https://img.shields.io/badge/Delta%20Lake-00ADD8?style=for-the-badge&logo=delta&logoColor=white"/>
  <img src="https://img.shields.io/badge/Azure%20Data%20Lake-0078D4?style=for-the-badge&logo=microsoftazure&logoColor=white"/>
  <img src="https://img.shields.io/badge/Unity%20Catalog-5C2D91?style=for-the-badge&logo=databricks&logoColor=white"/>
</p>

<p align="center">
  A production-grade, end-to-end Data Engineering pipeline built on <strong>Azure Databricks</strong> using the <strong>Medallion Architecture</strong> — ingesting raw Formula 1 datasets, transforming them through structured layers, and producing analytics-ready Gold tables for business intelligence.
</p>

---

## 🚀 Project Overview

This project implements a fully automated, scalable ETL pipeline for Formula 1 historical data using the **Medallion Architecture (Bronze → Silver → Gold)** on **Azure Databricks** with **Delta Lake** as the storage format and **Unity Catalog** for enterprise-grade data governance.

The pipeline ingests raw CSV and JSON datasets, applies data quality transformations, and produces aggregated analytics tables covering driver standings, constructor performance, race results, pit stop analysis, and more — ready for dashboards or further BI tooling.

| Attribute | Details |
|---|---|
| **Cloud Platform** | Microsoft Azure |
| **Compute** | Azure Databricks (Spark Cluster) |
| **Storage** | Azure Data Lake Storage Gen2 (ADLS Gen2) |
| **Table Format** | Delta Lake |
| **Governance** | Unity Catalog |
| **Language** | Python (PySpark) + Spark SQL |
| **Architecture** | Medallion (Bronze / Silver / Gold) |

---

## 🏗️ Architecture

### Medallion Architecture Overview

The pipeline follows the industry-standard **Medallion Architecture** — a three-layer data organisation pattern that progressively improves data quality from raw ingestion to business-ready analytics.

```
┌──────────────────────────────────────────────────────────────────────────┐
│                         AZURE DATA LAKE GEN2                             │
│                                                                          │
│  ┌─────────────────┐    ┌─────────────────┐    ┌─────────────────────┐  │
│  │   BRONZE LAYER  │───▶│   SILVER LAYER  │───▶│     GOLD LAYER      │  │
│  │  (Raw Ingestion)│    │  (Cleaned Data) │    │  (Business Tables)  │  │
│  │                 │    │                 │    │                     │  │
│  │  • circuits     │    │  • circuits     │    │  • driver_standings │  │
│  │  • constructors │    │  • constructors │    │  • constructor_std  │  │
│  │  • drivers      │    │  • drivers      │    │  • yearly_perf      │  │
│  │  • races        │    │  • races        │    │  • driver_wins      │  │
│  │  • results      │    │  • results      │    │  • fastest_laps     │  │
│  │  • pit_stops    │    │  • pitstops     │    │  • pitstop_analysis │  │
│  │  • lap_times    │    │  • laptimes     │    │  • race_results     │  │
│  │  • qualifying   │    │  • qualifying   │    │                     │  │
│  └─────────────────┘    └─────────────────┘    └─────────────────────┘  │
│         ▲                                                                │
│         │  Raw CSV/JSON files                                            │
│  ┌──────┴──────┐                                                         │
│  │  demo/      │  (abfss://demo@storageaccountyukta.dfs.core.windows.net)│
│  └─────────────┘                                                         │
└──────────────────────────────────────────────────────────────────────────┘
                                    │
                       ┌────────────▼────────────┐
                       │   UNITY CATALOG (f1_catalog)  │
                       │   ├── bronze schema      │
                       │   ├── silver schema      │
                       │   └── gold schema        │
                       └─────────────────────────┘
```

### Layer Responsibilities

| Layer | Purpose | Format | Key Operations |
|---|---|---|---|
| **Bronze** | Raw data as-is from source | Delta | Schema enforcement, initial load |
| **Silver** | Cleaned, validated, enriched | Delta | Deduplication, null removal, renaming, derived columns |
| **Gold** | Aggregated, analytics-ready | Delta | Joins, GROUP BY, business KPIs |

---

## ⚙️ Tech Stack

| Technology | Role |
|---|---|
| **Azure Databricks** | Unified analytics platform / compute engine |
| **Apache Spark (PySpark)** | Distributed data processing |
| **Spark SQL** | SQL-based transformations and aggregations |
| **Delta Lake** | ACID-compliant table format with versioning |
| **ADLS Gen2** | Scalable cloud object storage (abfss protocol) |
| **Unity Catalog** | Centralised metadata, access control, and lineage |
| **Python** | Notebook scripting and pipeline orchestration |

---

## 📂 Project Structure

```
f1-data-engineering-pipeline/
│
├── 📓 external_locations_and_catalog_creation.py   # Setup: Unity Catalog, external locations, schemas
├── 📓 01_bronze.py                                  # Bronze layer: raw ingestion notebooks
├── 📓 02_silver.py                                  # Silver layer: cleaning & transformation
├── 📓 gold.py                                       # Gold layer: aggregations & analytics
│
├── 📁 raw/                                          # Raw source data files
│   ├── circuits.csv
│   ├── races.csv
│   ├── laptime/          (folder — multiple CSV files)
│   ├── qualifying/       (folder — multiple JSON files)
│   ├── constructors.json
│   ├── drivers.json
│   ├── results.json
│   └── pit_stops.json
│
└── 📄 README.md
```

### File Descriptions

**`external_locations_and_catalog_creation.py`** — Infrastructure setup notebook. Creates the `f1_catalog` Unity Catalog, defines external locations pointing to ADLS Gen2 containers (`originalbronze`, `originalsilver`, `originalgold`), and provisions the Bronze, Silver, and Gold schemas with managed storage locations.

**`01_bronze.py`** — Ingestion notebook. Reads 8 raw datasets from ADLS Gen2 using the `abfss://` protocol, applies explicit schema definitions, and writes Delta tables into `f1_catalog.bronze`.

**`02_silver.py`** — Transformation notebook. Reads Bronze Delta tables, applies data quality rules, renames columns to snake_case, creates derived fields, and saves cleaned Delta tables to `f1_catalog.silver`.

**`gold.py`** — Analytics notebook. Executes Spark SQL to produce 7 Gold-layer aggregated tables in `f1_catalog.gold` — ready for BI tools and reporting.

---

## 🔄 Data Pipeline Flow

```
[Raw Files on ADLS Gen2]
        │
        ▼  (Step 1 — Infrastructure)
  Unity Catalog Setup
  External Locations + Schemas Created
        │
        ▼  (Step 2 — Bronze Ingestion)
  Schema Enforcement → Read CSV/JSON → Write Delta (Bronze)
        │
        ▼  (Step 3 — Silver Transformation)
  Rename Columns → Remove Nulls → Deduplicate
  → Derive Columns → Add Ingestion Timestamp → Write Delta (Silver)
        │
        ▼  (Step 4 — Gold Aggregation)
  JOIN Silver Tables → GROUP BY → Business KPIs → Write Delta (Gold)
        │
        ▼  (Step 5 — Consumption)
  Power BI / Databricks SQL / Notebooks
```

### Step-by-Step ETL Breakdown

**Step 1 — Infrastructure Setup**
External locations are registered in Unity Catalog using a storage credential (`yuktacredential`), linking Databricks to the ADLS Gen2 containers. Separate managed locations are created for Bronze, Silver, and Gold schemas, ensuring data isolation and governance at the storage layer.

**Step 2 — Bronze Ingestion**
Each dataset is read from ADLS Gen2 using `spark.read` with explicitly defined `StructType` schemas. This enforces correct data types at the point of ingestion (e.g., `IntegerType` for IDs, `DoubleType` for coordinates) and prevents silent schema drift. All tables are persisted as Delta format using `mode('overwrite')`.

**Step 3 — Silver Transformation**
Bronze tables are read and cleaned using PySpark DataFrame operations. Transformations include column renaming to snake_case conventions, deduplication using `dropDuplicates()` on primary keys, null filtering with `filter(col().isNotNull())`, creation of derived columns such as `driver_name` (concatenated from nested struct fields) and `race_timestamp` (built from separate date and time columns using `try_to_timestamp`), and appending an `ingestion_date` metadata column.

**Step 4 — Gold Aggregation**
Spark SQL `CREATE OR REPLACE TABLE ... AS SELECT` statements join Silver tables and compute business-level aggregations. Joins are performed on normalised keys (e.g., `driver_id`, `constructor_id`, `race_id`), and results are grouped and ordered to produce ranked leaderboards and performance metrics.

---

## 📊 Data Processing Details

### 🥉 Bronze Layer — Raw Ingestion

| Table | Source Format | Key Schema Fields |
|---|---|---|
| `circuits` | CSV | circuitId, lat, lng, alt, country |
| `constructors` | JSON | constructorId, nationality |
| `drivers` | JSON (nested) | driverId, name.forename, name.surname |
| `races` | CSV | raceId, year, round, circuitId, date, time |
| `results` | JSON | resultId, points, position, fastestLapTime |
| `pit_stops` | JSON (multiline) | raceId, driverId, stop, milliseconds |
| `lap_times` | CSV (folder) | raceId, driverId, lap, milliseconds |
| `qualifying` | JSON (multiline, folder) | qualifyId, q1, q2, q3 |

Notable ingestion details:
- `drivers.json` uses a **nested struct** for the `name` field (forename + surname)
- `pit_stops.json` and `qualifying/` require `multiline=True` due to multi-line JSON structure
- `lap_times/` and `qualifying/` are ingested from **folders** containing multiple files

### 🥈 Silver Layer — Transformations Applied

| Transformation | Tables Affected |
|---|---|
| Column rename to snake_case | All tables |
| Null filtering on primary key | All tables |
| `dropDuplicates()` on primary key | All tables |
| Nested struct flattening (`name.forename + name.surname`) | drivers |
| `race_timestamp` derived from date + time using `try_to_timestamp` | races |
| `ingestion_date` metadata column added | All tables |
| Column selection (drop unnecessary fields) | drivers |

### 🥇 Gold Layer — Analytics Tables

| Gold Table | Description | Key Columns |
|---|---|---|
| `driver_standings` | All-time total points per driver | driver_name, total_points |
| `constructor_standings` | All-time total points per constructor | constructor_name, total_points |
| `driver_yearly_performance` | Points earned by driver per season | driver_name, year, total_points |
| `driver_wins` | Total race wins per driver | driver_name, total_wins |
| `fastest_laps` | Best lap time per driver across all races | driver_name, best_lap_time |
| `pitstop_analysis` | Average pit stop duration per driver (ms) | driver_name, avg_pitstop_time |
| `race_results` | Full race-by-race results with constructor | race_name, year, driver_name, position, points |

---

## 📈 Key Insights / Analytics

The Gold layer enables the following business questions to be answered directly:

- 🏆 **Who are the all-time highest points scorers?** → `driver_standings`
- 🏭 **Which constructor dominated across all seasons?** → `constructor_standings`
- 📅 **How did a driver's performance evolve year-on-year?** → `driver_yearly_performance`
- 🥇 **Who has the most race wins?** → `driver_wins`
- ⚡ **Which driver set the fastest lap ever recorded?** → `fastest_laps`
- 🔧 **Which team/driver has the fastest pit crew?** → `pitstop_analysis`
- 📋 **Full race-by-race result lookup with constructor** → `race_results`

These tables serve as the foundation for Power BI dashboards, Databricks SQL queries, or any downstream BI/ML consumption.

---

## 🧠 Concepts Used

### Unity Catalog
Databricks' centralised **data governance layer**. This project creates a dedicated `f1_catalog` with three schemas (`bronze`, `silver`, `gold`), each backed by separate managed locations on ADLS Gen2. Unity Catalog provides fine-grained access control, table lineage, and metadata management across all layers.

### Delta Lake
All tables across all three layers are stored in **Delta format** — a columnar storage format that adds ACID transaction support, schema enforcement, and time-travel capabilities on top of Parquet. `mode('overwrite')` with Delta ensures idempotent pipeline re-runs.

### External Locations & Storage Credentials
ADLS Gen2 containers are connected to Databricks via **external locations** registered against a storage credential (`yuktacredential`). This is the Unity Catalog-native way to securely grant Databricks access to cloud storage without embedding keys in notebooks.

### Schema Enforcement
`StructType` schemas are defined explicitly before ingestion in the Bronze layer. This prevents incorrect type inference (e.g., numeric IDs being read as strings) and ensures consistent data contracts from day one.

### Medallion Architecture
A layered data organisation pattern where data progressively gains quality: Bronze holds raw data as-is, Silver holds cleaned and validated data, and Gold holds aggregated, business-ready data. This separation ensures raw data is always preserved while downstream consumers get clean, reliable tables.

### PySpark DataFrame API
Transformations use PySpark's DataFrame API: `withColumnRenamed`, `dropDuplicates`, `filter`, `withColumn`, `select`, `concat`, and `expr` for SQL-style expressions within the Python API.

### Spark SQL (DDL + DML)
The Gold layer uses `CREATE OR REPLACE TABLE ... AS SELECT` — a clean, declarative way to rebuild analytics tables on every run, ensuring the Gold layer is always up to date.

---

## 💻 How to Run the Project

### Prerequisites

- An active **Azure Subscription**
- **Azure Databricks Workspace** (Standard or Premium tier)
- **Azure Data Lake Storage Gen2** account with containers: `demo`, `originalbronze`, `originalsilver`, `originalgold`
- A configured **Storage Credential** in Unity Catalog (named `yuktacredential`)
- A Databricks cluster with Spark 3.x runtime

### Setup Steps

**1. Upload Raw Data**
Upload the raw files from the `raw/` folder to your ADLS Gen2 `demo` container:
```
circuits.csv, races.csv → root of demo/
constructors.json, drivers.json, results.json, pit_stops.json → root of demo/
laptime/ folder → demo/laptime/
qualifying/ folder → demo/qualifying/
```

**2. Run Infrastructure Setup**
Open `external_locations_and_catalog_creation.py` in your Databricks workspace and execute all cells. This creates the catalog, external locations, and schemas.

```sql
-- Verify setup
SHOW EXTERNAL LOCATIONS;
SHOW SCHEMAS IN f1_catalog;
```

**3. Run Bronze Ingestion**
Execute `01_bronze.py` to ingest all raw files into Delta tables under `f1_catalog.bronze`.

```sql
-- Verify
SHOW TABLES IN f1_catalog.bronze;
```

**4. Run Silver Transformations**
Execute `02_silver.py` to clean and transform all Bronze tables into `f1_catalog.silver`.

```sql
-- Verify
SELECT COUNT(*) FROM f1_catalog.silver.drivers;
```

**5. Run Gold Aggregations**
Execute `gold.py` to build all analytics tables in `f1_catalog.gold`.

```sql
-- Spot check
SELECT * FROM f1_catalog.gold.driver_standings ORDER BY total_points DESC LIMIT 10;
```

### Execution Order

```
external_locations_and_catalog_creation.py
        ↓
01_bronze.py
        ↓
02_silver.py
        ↓
gold.py
```

---

## 📌 Future Improvements

- **Incremental Loads** — Replace full `overwrite` with merge-based incremental ingestion using `MERGE INTO` (Delta Lake upserts) to handle new race seasons without reprocessing historical data.
- **Pipeline Orchestration** — Automate execution order using **Databricks Workflows** or **Apache Airflow**, with dependency management and failure alerting.
- **Data Quality Framework** — Integrate **Great Expectations** or Databricks' native **Delta Live Tables expectations** for automated data quality checks at each layer.
- **Streaming Ingestion** — Extend Bronze ingestion to support real-time race telemetry using **Spark Structured Streaming** from Event Hub or Kafka.
- **BI Dashboard** — Connect the Gold layer to **Power BI** or **Databricks SQL Dashboard** for interactive championship standings and race visualisations.
- **Parameterised Notebooks** — Convert hardcoded paths and table names to **Databricks Widgets** for reusability across different seasons or catalogs.
- **CI/CD Pipeline** — Add version control via **Databricks Repos (GitHub integration)** with automated testing and deployment using GitHub Actions.
- **Cost Optimisation** — Apply **Z-ordering** and **OPTIMIZE** on frequently queried columns (e.g., `driver_id`, `race_id`) in Silver tables to improve query performance and reduce scan costs.

---

## ⭐ Conclusion

This project demonstrates the implementation of a real-world, production-style data engineering pipeline on Azure Databricks — covering the full lifecycle from raw data ingestion to business analytics. By combining the **Medallion Architecture** with **Delta Lake's** ACID guarantees, **Unity Catalog's** governance capabilities, and **PySpark's** distributed processing power, the pipeline is built to scale, auditable, and maintainable.

The structured three-layer approach ensures raw data is always preserved in Bronze, reliable clean data is always accessible in Silver, and high-quality analytics are always ready in Gold — reflecting the same patterns used in enterprise data lakes at scale.

---

<p align="center">
  Built with ❤️ using Azure Databricks · PySpark · Delta Lake · Unity Catalog
</p>
