# azure-medallion-pipeline

An end-to-end data engineering pipeline built on Azure, implementing the **Medallion Architecture** (Bronze → Silver → Gold) for structured data ingestion, transformation, and serving.

Built to gain hands-on experience with Azure data services.

---

## Architecture
<img width="716" height="403" alt="image" src="https://github.com/user-attachments/assets/0ff4933b-eeda-4291-8109-21ab9feb307f" />


```
GitHub (CSV files)
      |
      | HTTP
      v
Azure Data Factory (ADF)
  - Lookup activity reads config JSON from ADLS
  - ForEach loop dynamically ingests each file
      |
      v
Bronze Layer (ADLS Gen2)
  - Raw CSV files stored as-is
      |
      | Service Principal (Entra ID)
      v
Azure Databricks
  - PySpark transformations
  - Data cleaning and structuring
      |
      v
Silver Layer (ADLS Gen2)
  - Transformed data stored as Parquet
      |
      | Service Principal (Entra ID)
      v
Synapse Analytics
  - OPENROWSET() used to query Silver layer
  - Filtered and aggregated data saved as Parquet
      |
      v
Gold Layer (ADLS Gen2)
  - Curated external tables ready for consumption
```

---

## Tech Stack

| Layer | Tool |
|---|---|
| Ingestion | Azure Data Factory (ADF) |
| Storage | Azure Data Lake Storage Gen2 (ADLS) |
| Transformation | Azure Databricks (PySpark) |
| Serving | Azure Synapse Analytics |
| Authentication | Azure Entra ID (Service Principal) |
| Source | GitHub (HTTP) |

---

## Pipeline Flow

1. **ADF Ingestion** — A Lookup activity reads a config JSON file from ADLS containing file metadata. A ForEach loop dynamically ingests each CSV file from GitHub via HTTP linked service and lands them in the **Bronze layer**.

2. **Databricks Transformation** — Connects to ADLS using a Service Principal. Reads raw data from Bronze, applies PySpark transformations, and writes cleaned data as Parquet to the **Silver layer**.

3. **Synapse Serving** — Uses `OPENROWSET()` to query the Silver layer, filters relevant data, and saves the final output as Parquet in the **Gold layer** as external tables ready for analyst consumption.

---

## Setup

### Prerequisites
- Azure subscription
- Azure Data Factory
- Azure Data Lake Storage Gen2
- Azure Databricks workspace
- Azure Synapse Analytics workspace
- Azure Entra ID App Registration (Service Principal)

> **Note:** In production, use [Databricks Secrets](https://docs.databricks.com/security/secrets/index.html) to manage credentials securely instead of hardcoding them.

---

## Repository Structure

```
azure-medallion-pipeline/
│
├── adf/
│   └── pipeline1.json         # ADF pipeline definition
│
├── databricks/
│   └── silverlayer.ipynb   # PySpark transformation notebook
│
├── data/
│   └── sample.csv             # Sample source data
│
└── README.md
```

---

## Key Learnings

- Implementing medallion architecture (Bronze/Silver/Gold) on Azure
- Dynamic ADF pipelines using Lookup + ForEach activities
- Authenticating Azure services using Service Principals (Entra ID)
- PySpark transformations in Databricks connected to ADLS Gen2
- Serving curated data via Synapse Analytics using `OPENROWSET()`
