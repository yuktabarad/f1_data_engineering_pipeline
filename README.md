# 🏎️ Formula 1 Data Engineering Pipeline

An end-to-end data engineering project that transforms Formula 1 data into structured analytical datasets using **Databricks, PySpark, SQL and Delta Lake**.

The project follows the **Medallion Architecture (Bronze → Silver → Gold)** to build a data pipeline and generate insights into driver performance, constructor standings, race results and pitstop efficiency.

## 🏗️ Architecture

**Bronze → Silver → Gold → Visualizations**

| Layer         | Description                                                                              |
| ------------- | ---------------------------------------------------------------------------------------- |
| Bronze        | Ingested and stored raw Formula 1 datasets.                                              |
| Silver        | Cleaned data, handled missing values, removed duplicates and added ingestion timestamps. |
| Gold          | Created 10 analytical tables containing business-level KPIs.                             |
| Visualization | Developed five visualizations using Matplotlib and Seaborn.                              |

## 🛠️ Tech Stack

* **Platform:** Databricks Free Edition
* **Languages:** Python, SQL
* **Processing:** PySpark
* **Storage:** Delta Lake
* **Architecture:** Medallion Architecture
* **Visualization:** Matplotlib, Seaborn
* **Version Control:** Git, GitHub
* **Orchestration:** Databricks Jobs

## 📊 Gold Layer – Key Analytics

Developed 10 analytical tables covering:

* Driver and constructor standings
* Yearly driver and constructor performance
* Driver wins and fastest laps
* Pitstop performance analysis
* Individual race results
* Season-level and race-level summaries

**Key KPIs:** Total points, race wins, podium finishes, average points per race, win percentage, fastest lap times, pitstop duration and championship rankings.

## 📈 Visualizations

Created five visualizations to explore:

1. Top 10 F1 drivers by total points.
2. Top 10 constructors by total points.
3. Yearly driver points trends.
4. Average pitstop duration by constructor.
5. Number of races across seasons.

## ⚙️ Pipeline Orchestration

Created and successfully executed a Databricks Job to automate the complete notebook workflow.

**Task dependencies:**

`Bronze Ingestion → Silver Transformation → Gold Analytics → Data Visualizations`

All four tasks completed successfully in approximately **3 minutes 17 seconds** during the recorded execution.

The exported Job configuration is available in the `job/` directory. Screenshots of the successful execution and task dependencies are available in the `screenshots/` directory.

## 🎯 Key Outcomes

* Implemented a multi-layer data engineering pipeline.
* Applied data cleaning and validation using PySpark.
* Developed 10 Gold Layer analytical datasets.
* Generated business-oriented KPIs and visualizations.
* Orchestrated four dependent notebook tasks using Databricks Jobs.
* Successfully executed the end-to-end pipeline.

---

**Project Focus:** Data Engineering | ETL | PySpark | SQL | Databricks | Delta Lake
