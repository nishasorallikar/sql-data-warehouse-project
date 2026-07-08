# 🗂️ Data Warehouse Layers & Lifecycle

An overview of the Medallion Architecture data layers, their individual lifecycles, and guidelines for source system ingestion.

---

## 📊 Medallion Layers Comparison

| Property | 🟫 Bronze Layer (Raw) | 🥈 Silver Layer (Cleaned) | 🥇 Gold Layer (Curated) |
| :--- | :--- | :--- | :--- |
| **Definition** | Raw, unprocessed data as-is from source systems. | Cleaned, standardized, and integrated data. | Business-ready, modeled data for reporting & BI. |
| **Objective** | Preserves history, enables data traceability & debugging. | Serves as the single source of truth for downstream analysis. | Delivers optimized, consumer-ready datasets. |
| **Object Type** | Physical Tables | Physical Tables | Database Views (or virtualized layer) |
| **Load Method** | Full Load (Truncate & Insert) | Full Load (Truncate & Insert) | None (Semantic abstraction over Silver) |
| **Data Transformation** | None (Ingested completely as-is) | • Data Cleaning<br>• Standardization<br>• Normalization<br>• Derived Columns<br>• Data Enrichment | • Data Integration<br>• Data Aggregation<br>• Business Logic & Rules |
| **Data Modeling** | None (Matches source schema) | None (Cleaned staging/flat layout) | • Star Schema (Facts/Dimensions)<br>• Aggregated Objects<br>• Flat Tables |
| **Target Audience** | Data Engineers | Data Analysts, Data Engineers | Data Analysts, Business Users |

---

## 🔄 Layer Implementation Workflow

Each layer follows a structured lifecycle to ensure quality, maintainability, and traceability.

### 1. 🟫 Bronze Layer Workflow
```mermaid
flowchart LR
    A["🔍 **1. Analysing**<br>Interview source system experts to understand schemas."]
    B["💻 **2. Coding**<br>Develop raw data ingestion pipelines."]
    C["✅ **3. Validating**<br>Perform completeness & schema constraint checks."]
    D["📁 **4. Docs & Version**<br>Document tables and track scripts in GIT."]

    A --> B --> C --> D
    style A fill:#FDF2E9,stroke:#E67E22,stroke-width:2px;
    style B fill:#FDF2E9,stroke:#E67E22,stroke-width:2px;
    style C fill:#FDF2E9,stroke:#E67E22,stroke-width:2px;
    style D fill:#FDF2E9,stroke:#E67E22,stroke-width:2px;
```

### 2. 🥈 Silver Layer Workflow
```mermaid
flowchart LR
    A["🔍 **1. Analysing**<br>Explore & understand raw bronze datasets."]
    B["💻 **2. Coding**<br>Build data cleansing and normalization scripts."]
    C["✅ **3. Validating**<br>Validate data correctness & quality rules."]
    D["📁 **4. Docs & Version**<br>Document data lineage & version in GIT."]

    A --> B --> C --> D
    
    style A fill:#F2F3F4,stroke:#7F8C8D,stroke-width:2px;
    style B fill:#F2F3F4,stroke:#7F8C8D,stroke-width:2px;
    style C fill:#F2F3F4,stroke:#7F8C8D,stroke-width:2px;
    style D fill:#F2F3F4,stroke:#7F8C8D,stroke-width:2px;
```

### 3. 🥇 Gold Layer Workflow
```mermaid
flowchart LR
    A["🔍 **1. Analysing**<br>Explore & understand business objects & KPI requirements."]
    B["💻 **2. Coding**<br>Code Star Schema relations and business logic."]
    C["✅ **3. Validating**<br>Validate dimensional integrity and logic checks."]
    D["📁 **4. Docs & Version**<br>Document data models, catalog, & version in GIT."]

    A --> B --> C --> D
    
    style A fill:#FEF9E7,stroke:#F1C40F,stroke-width:2px;
    style B fill:#FEF9E7,stroke:#F1C40F,stroke-width:2px;
    style C fill:#FEF9E7,stroke:#F1C40F,stroke-width:2px;
    style D fill:#FEF9E7,stroke:#F1C40F,stroke-width:2px;
```

---

## 📋 Source System Interview Checklist

Before ingesting any new source system, data engineers must conduct a discovery interview covering these three critical areas:

```mermaid
mindmap
  root((Source System Ingestion))
    Business Context & Ownership
      Who owns the data?
      What business processes are supported?
      Is there existing documentation?
      Is there a data model or catalog?
    Extract & Load Parameters
      Incremental vs. Full load strategy
      Data scope and historical depth needed
      Expected dataset size per extraction
      Volume limitations or bandwidth caps
      Source system performance impact mitigation
      Security & auth (Tokens, SSH, VPN, IP limits)
    Architecture & Tech Stack
      Storage mechanism (SQL Server, Oracle, AWS, Azure, etc.)
      Integration interfaces (REST API, Kafka, File export, DB link)
```

### 🔑 Key Inquiries & Details

> [!IMPORTANT]
> **Business Context & Ownership**
> - **Data Ownership:** Identify the system owner and key point-of-contact for schema changes or data quality issues.
> - **Business Processes:** Document which applications or dashboards rely on this dataset to assess criticality.

> [!TIP]
> **Extract & Load Strategy**
> - **Performance Impact:** Schedule high-volume extractions during off-peak hours.
> - **Load Strategy:** Prefer incremental extraction using Change Data Capture (CDC) or timestamp filters over daily full loads for large datasets.

> [!WARNING]
> **Security & Compliance**
> - Ensure all connection strings, API keys, and credentials are stored securely and never hardcoded in ingestion scripts.
