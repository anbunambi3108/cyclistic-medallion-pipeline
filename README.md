# Cyclistic Bike Share — Databricks Notebooks

**PySpark · Delta Lake · Medallion Architecture · Databricks**

---

## Overview

This version rebuilds the Cyclistic Bike Share analysis using Databricks with a structured data engineering approach.

The core business question:

**How do annual members and casual riders use Cyclistic bikes differently, and how can that support membership conversion?**

The key shift is in execution.
Instead of a local analysis workflow, this version implements a scalable pipeline using PySpark and Delta Lake, aligned with industry practices.

---

## Architecture

The pipeline follows a **Bronze → Silver → Gold medallion architecture**.

* **Bronze** → raw data ingestion
* **Silver** → cleaned and enriched data
* **Gold** → aggregated data for analysis

Each layer has a clearly defined responsibility, avoiding overlap between ingestion, transformation, and aggregation.

### Why this approach

* Simplifies debugging by isolating each stage
* Preserves raw data for reprocessing if needed
* Supports reuse of intermediate datasets across use cases

---

## Tech Stack

* **Platform:** Databricks Free Edition
* **Processing:** PySpark
* **Storage:** Delta Lake (Unity Catalog)
* **Query Engine:** Spark SQL
* **Visualisation:** Matplotlib, Seaborn
* **Data Source:** Divvy Tripdata (S3)

---

## Pipeline Structure

The pipeline is organised into **4 notebooks**, each representing a stage in the workflow.

---

### 1. `00_download_data`

Handles data ingestion from source.

* Downloads 12 months of trip data directly from S3
* Stores data in a Unity Catalog volume
* Eliminates dependency on local environment

---

### 2. `01_bronze` — Raw Layer

Loads raw data into Delta format.

* Reads all CSV files using a wildcard pattern
* Uses schema inference for column types
* Applies no transformations

**Output:**

* `bronze_rides` (~5.5M rows)

This layer serves as the immutable source dataset.

---

### 3. `02_silver` — Clean Layer

Performs data cleaning and feature engineering.

**Cleaning steps:**

* Removes invalid ride durations
* Filters records with missing station data
* Validates coordinate fields
* Ensures consistency across key columns

**Feature engineering:**

* Ride duration (minutes)
* Hour of day
* Weekday and month
* Weekend indicator
* Season classification
* Holiday flag

**Output:**

* `silver_rides` (~3.7M rows)

This dataset is structured for downstream analysis.

---

### 4. `03_gold` — Business Layer

Focuses on analytical outputs using Spark SQL.

* Creates multiple aggregated Delta tables
* Covers trends by rider type, time, stations, and routes
* Generates visualisations within the notebook

This layer directly supports business insights.

---

## Key Improvements Over Version 1

This version transitions from a local analysis setup to a structured pipeline.

| Area            | Version 1  | Version 2        |
| --------------- | ---------- | ---------------- |
| Storage         | CSV        | Delta Lake       |
| Processing      | Pandas     | PySpark          |
| Query Engine    | SQLite     | Spark SQL        |
| Architecture    | None       | Medallion        |
| Scalability     | Limited    | Scalable         |
| Reproducibility | Local only | Cloud-based      |
| Visualisation   | None       | 6 charts         |
| Output          | Temporary  | Persisted tables |

---

## Limitations

While structured, this version is still manually driven.

* Notebooks must be executed in sequence
* No scheduling or orchestration
* No formal data quality monitoring
* No lineage or dependency tracking

---

## How to Run

1. Create a Databricks Free account
2. Set up a Unity Catalog volume
3. Execute notebooks in order:

```
00_download_data
01_bronze
02_silver
03_gold
```

---

## What This Version Demonstrates

* Understanding of medallion architecture
* Distributed data processing using PySpark
* Use of Delta Lake for reliable storage
* Querying at scale with Spark SQL
* Building reproducible pipelines in a cloud environment
