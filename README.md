# ADFPRO

**ADFPRO** is a modular Azure Data Factory project designed to demonstrate real-world data engineering practices using fully parameterized and metadata-driven pipelines.

The goal of this repository is to build and version-control a complete ADF solution covering:

- Dynamic ingestion pipelines for multiple file formats (CSV, JSON, Parquet)
- Incremental and CDC-ready data ingestion into ADLS Gen2
- REST API integration with pagination and scheduling
- Schema validation and metadata auditing
- Delta Lake–ready data landing and curation
- CI/CD integration with GitHub for version-controlled ADF deployments

## Tech Stack

- Azure Data Factory (ADF)
- Azure Data Lake Storage Gen2
- Azure SQL Database
- GitHub (ADF integration and CI/CD)
- Power Query and Data Flows

## 🔄 Ingestion Pipeline

The **Ingestion** pipeline automates dynamic data ingestion into **Azure Data Lake Storage Gen2**, supporting both full and incremental (CDC) loads through metadata-driven control.

### Flow Overview
1. **Lookup (last_cdc)** – Fetches the last processed CDC or watermark value.  
2. **Script (Total_Count)** – Runs a SQL query to get the latest CDC value or record count.  
3. **If Condition (If New Rows)** – Compares CDC values:  
   - **True branch:** Loads new rows, updates max CDC, and commits the new checkpoint.  
   - **False branch:** Skips load when no changes are found.

### Parameters
| Name | Type | Default | Description |
|------|------|----------|-------------|
| `schema` | String | `source` | Source schema name |
| `table` | String | `Orders` | Target table for ingestion |
| `backdate` | String | `Value` | Optional for backdated loads |

**Highlights:**  
- Parameterized for multiple sources and tables.  
- Uses metadata tables for CDC tracking.  
- Integrates easily with the **Scheduled** orchestration pipeline.

![Ingestion Pipeline](images/ingestion_pipeline.png)
