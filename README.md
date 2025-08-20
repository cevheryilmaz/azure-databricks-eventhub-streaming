# Real-Time Data Processing with Azure Databricks, Event Hubs, and Delta Lake (Medallion Architecture)

This repository contains a Databricks notebook that demonstrates how to build a **real-time streaming pipeline** with **Azure Event Hubs** as the data source and **Azure Data Lake Storage Gen2 (ADLS)** as the sink, following the **Medallion architecture** (Bronze → Silver → Gold).

## Architecture

- **Bronze**: Raw, immutable ingestion of IoT telemetry events from Event Hubs.  
- **Silver**: Parsed JSON, schema applied, duplicates removed, and cleansed.  
- **Gold**: Aggregated KPIs (windowed metrics like average temperature/humidity per device/site).  

## Technologies Used
- Azure Event Hubs
- Azure Databricks (Structured Streaming, Delta Lake)
- Azure Data Lake Storage Gen2
- Python / PySpark

## Requirements
- Databricks Runtime 12+ with Delta Lake enabled
- Access to an Event Hubs namespace & hub
- Access to ADLS Gen2 (container + path permissions)
- (Optional) `azure-eventhub` Python package if you use the sample event generator

## How to Use

1. **Deploy prerequisites:**
   - Create an Azure Event Hubs namespace and hub.
   - Provision Azure Data Lake Storage Gen2 container(s).
   - Mount ADLS Gen2 to Databricks (or use `abfss://` URIs).

2. **Import notebook:**
   - Upload `azure-databricks-eventhub-streaming.ipynb` into your Databricks workspace.

3. **Configure parameters:**
   - Fill in widgets at the top of the notebook:
     - Event Hubs connection string
     - Consumer group
     - ADLS paths for Bronze, Silver, Gold
     - Checkpoint locations

4. **Run the streaming jobs:**
   - Start the Bronze stream (Event Hubs → Delta)
   - Start the Silver stream (Bronze → Silver cleansed)
   - Start the Gold stream (Silver → Gold aggregated KPIs)

5. **Explore results:**
   - Use the provided SQL queries to analyze Gold tables (`iot_kpi_gold`).
   - Example: find top “hottest” devices in the last 30 minutes.

## Example Gold Table Output

| window_start       | window_end         | site      | device_id | avg_temperature | avg_humidity | reading_count |
|-------------------|--------------------|-----------|-----------|-----------------|--------------|---------------|
| 2025-08-20 20:00  | 2025-08-20 20:01   | ams       | dev-1     | 22.5            | 0.45         | 12            |
| 2025-08-20 20:00  | 2025-08-20 20:01   | rotterdam | dev-3     | 24.1            | 0.38         | 15            |

## Sample Data
If you want to test without Event Hubs, you can use the provided sample data in `sample_data.json` and load it into the Bronze layer manually.

## Security Notes
- Do **not** commit your Event Hubs connection string to the repo. Use Databricks **Secret Scopes** or environment variables.
- Use separate checkpoint locations per stream (Bronze/Silver/Gold) to avoid state conflicts.



