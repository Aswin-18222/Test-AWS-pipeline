**Building an Analytics Pipeline for E-Commerce Transactions on AWS**

This project demonstrates an end-to-end data ingestion → transformation → storage → analytics pipeline built using AWS serverless services.
It processes daily e-commerce transaction files, transforms them into optimized formats, and generates analytics for business insights.

**Architecture Overview**

S3 Raw Zone → Lambda Trigger → Airflow DAG → AWS Glue ETL → S3 Curated Zone → Athena → QuickSight

**Architecture Highlights**

1.Event-driven ingestion using AWS Lambda

2.Workflow orchestration using Apache Airflow

3.Distributed ETL processing using AWS Glue (PySpark)

4.Optimized data storage in Parquet + partitioned format

5.Serverless querying with Amazon Athena

6.Interactive dashboards using Amazon QuickSight

**Dataset Description**

Daily CSV files arrive in: [s3://ecommerce-raw-data/]

Columns include:

1.transaction_id

2.store_id

3.product_id

4.quantity

5.amount

6.payment_type

7.timestamp

**Pipeline Steps**

1. Data Ingestion (S3 Raw Zone)

Daily CSVs uploaded from multiple online stores

Stored in raw zone for audit and reproducibility

2. Trigger (AWS Lambda)

Detects new file arrival

Triggers the Airflow DAG

Passes file metadata to the workflow

3. Orchestration (Airflow DAG)

S3 sensor waits for new file

Executes Glue ETL job

Triggers Athena summary query

Sends completion notifications

4. Data Transformation (AWS Glue PySpark)

Cleans and validates records

Handles schema evolution

Adds derived fields (date, year, month, day)

Converts CSV → Parquet

Writes partitioned dataset into curated zone

5. Curated Storage (S3)

Optimized, query-ready Parquet data

Partitioned by year/month/day

Integrated with Glue Data Catalog

6. Query Layer (Athena)

    SELECT
    store_id,
    date(timestamp) AS sales_date,
    SUM(amount) AS total_sales,
    SUM(quantity) AS total_quantity,
    COUNT(transaction_id) AS total_transactions
    FROM ecommerce_curated.transactions
    WHERE date(timestamp) = current_date - interval '1' day
    GROUP BY store_id, sales_date;

7. Visualization (QuickSight)

Dashboards include:

Daily sales per store

Weekly revenue trend

Top-selling products

Payment-type distribution

**Airflow DAG (Summary)**

Waits for new file using S3Sensor

Runs Glue job

Executes Athena SQL

Sends notifications

**Design Decisions**

Why these services?

**S3**: Cheap, scalable, schema-agnostic storage

**Lambda**: Fully serverless trigger mechanism

**Airflow**: Mature orchestration & monitoring

**Glue**: Serverless ETL with schema evolution support

**Athena**: Pay-per-query analytics

**QuickSight**: Business reporting tool

**Optimization choices**

Parquet format

Date-based partitioning

Catalog integration for Athena

Automated workflows end to end

**How to Run This Project**

Upload CSV files to the raw S3 bucket

Lambda detects new file and triggers Airflow

Airflow runs Glue ETL → writes curated Parquet

Athena updates summary tables

QuickSight dashboard refreshes with new data

**Conclusion**

This project implements a complete, scalable, fully automated AWS analytics pipeline that processes daily e-commerce files and 
generates actionable insights for business decision-making.
