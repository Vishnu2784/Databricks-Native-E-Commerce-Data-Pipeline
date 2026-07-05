# Databricks-Native-E-Commerce-Data-Pipeline

# 🚀 Databricks-Native E-Commerce Data Pipeline (Medallion Architecture)

![Databricks](https://img.shields.io/badge/Databricks-FF3621?style=for-the-badge&logo=databricks&logoColor=white)
![Apache Spark](https://img.shields.io/badge/Apache_Spark-E25A1C?style=for-the-badge&logo=apache-spark&logoColor=white)
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white)

## 📌 Project Overview
A production-grade, end-to-end Data Engineering pipeline **architected and executed 100% natively within Databricks**. 

This project bypasses traditional UI-based ETL tools, utilizing pure Python and PySpark to ingest, flatten, and aggregate high-volume e-commerce clickstream data. It strictly adheres to the **Medallion Architecture** (Bronze ➔ Silver ➔ Gold) and leverages **Databricks Unity Catalog Volumes** for secure, permanent cloud storage.

---

## 🧠 Design Philosophy: Why a Custom Mock API?
Most data engineering portfolios rely on static CSV files downloaded from external sites. This project takes a completely different approach by engineering a **Custom Python Data Generator** to act as the source. 

Why? Because real-world Data Engineering is about managing constraints, chaos, and scale. 

* **Simulating Memory Constraints (OOM Prevention):** Standard data generation scripts load everything into memory and crash at scale. This generator utilizes Python iterators (`yield`) to simulate API pagination, allowing the system to generate and write millions of records while only ever holding a small batch (e.g., 1,000 records) in memory at a time.
* **Architectural Chaos (Nested JSON):** Real web-log APIs do not return flat tables; they return heavily nested, unpredictable JSON strings. Using Python's `random` module, this script dynamically generates chaotic nested structs (e.g., deeply nested `user_data` and `transaction` blocks) to rigorously test the pipeline's PySpark flattening logic.
* **Simulating Incremental Arrival:** The script stamps each batch with a Unix timestamp and writes it as an independent JSON-Lines file, perfectly simulating how external services like AWS Kinesis or Kafka drop continuous, incremental data batches into a Bronze data lake.

---

## 🏗️ Architecture & Data Flow

```mermaid
graph TD
    A[Mock API Engine<br/>Python Generator] -->|Yields JSON Batches| B
    
    subgraph Databricks Unity Catalog Volumes
        B[(🥉 Bronze Layer<br/>Raw JSON-Lines)] -->|PySpark Read| C[Silver Processing Engine<br/>Serverless Compute]
        C -->|Flattens Structs & Casts Types| D[(🥈 Silver Layer<br/>Clean Parquet)]
        D -->|PySpark SQL| E[Gold Aggregation Engine<br/>Temp Views]
        E -->|Aggregates Metrics| F[(🥇 Gold Layer<br/>Business Parquet)]
    end

    style A fill:#3776AB,stroke:#fff,stroke-width:2px,color:#fff
    style B fill:#CD7F32,stroke:#fff,stroke-width:2px,color:#fff
    style C fill:#E25A1C,stroke:#fff,stroke-width:2px,color:#fff
    style D fill:#C0C0C0,stroke:#fff,stroke-width:2px,color:#fff
    style E fill:#E25A1C,stroke:#fff,stroke-width:2px,color:#fff
    style F fill:#FFD700,stroke:#fff,stroke-width:2px,color:#000
