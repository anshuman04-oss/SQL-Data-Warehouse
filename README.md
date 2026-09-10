# Serverless AWS Medallion Data Warehouse

An automated, event-driven data warehouse implementing the Medallion Architecture (Bronze, Silver, Gold). This system extracts raw transactional data, models it into star-schema dimension and fact tables, and orchestrates business intelligence ingestion for real-time reporting.

## Architecture Pipeline

[Raw Data] -> [Amazon S3 (Bronze)] -> [AWS Glue / Athena] -> [Amazon S3 (Silver)] -> [Athena CTAS] -> [Amazon S3 (Gold)] -> [Amazon QuickSight (SPICE)]

The entire workflow is orchestrated via **AWS Step Functions** utilizing the **JSONata** query language for state payload manipulation.

## Key Technical Implementations

* **Serverless ETL Processing:** Ingests and processes 350K+ records across Bronze (raw landing), Silver (filtered and deduplicated), and Gold (analytical star schema) data layers.
* **Parallel Orchestration:** Step Functions coordinate table generation by running dimension transformations in parallel state branches, utilizing sequential barriers for fact tables to guarantee strict referential integrity.
* **Idempotent Execution:** Incorporates automated pre-execution cleanup of the AWS Glue Data Catalog and S3 prefixes. Integrates custom error catchers for `Glue.EntityNotFoundException`, allowing safe, repeated pipeline executions without data duplication.
* **Automated BI Synchronization:** Utilizes Step Functions SDK integrations to dynamically trigger `quicksight:CreateIngestion` via JSONata `$uuid()` generation, refreshing QuickSight SPICE datasets instantly upon Gold view materialization.
* **Security & IAM:** Secures cross-service invocations between Step Functions, Athena, Glue, S3, and QuickSight using strict least-privilege IAM roles and custom inline policies.

## Tech Stack

* **Storage & Catalog:** Amazon S3, AWS Glue Data Catalog
* **Query Engine:** Amazon Athena (Presto/Trino SQL)
* **Orchestration:** AWS Step Functions (JSONata)
* **Business Intelligence:** Amazon QuickSight (SPICE)

## Acknowledgments

The foundational pipeline architecture was inspired by the [Data with Baraa](https://www.youtube.com/@DataWithBaraa) channel. This project extends that architecture by introducing JSONata-based Step Functions orchestration, automated idempotent error handling, and dynamic QuickSight SPICE ingestion triggers.
