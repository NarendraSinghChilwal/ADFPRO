
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

![Ingestion Pipeline](Images/ingestion_pipeline.png)

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

## 🔁 Router Pipeline



The **Router** pipeline serves as the central controller for all data ingestion workflows.  
It dynamically validates, enumerates, routes, and cleans up files using a fully metadata-driven design.

![Router Pipeline](Images/router_pipeline.png)

### Flow Overview
1. **Validation** – Confirms that the source path and configuration are valid before execution.  
2. **Get Metadata** – Extracts metadata (e.g., file names, folder hierarchy) from the defined container.  
3. **ForEach (ForEachFile)** – Iterates over each file returned from the metadata activity using:  
   ```text
   @activity('Get Metadata1').output.childItems
   Inside the loop, a SwitchFile activity determines the processing logic based on file type or source name.
4. **Delete** - Cleans up successfully processed files from the source container to maintain a clean workspace.

