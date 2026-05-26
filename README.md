# End-to-End HIV Longitudinal Cohort Retention Data Warehouse

## Project Overview
This project establishes a production-grade relational data warehouse pipeline designed to monitor public health retention outcomes within the HIV/TB care cascade over extended evaluation milestones (**Baseline Diagnosis → ART Initiation → 6-Month Retention → 12-Month Retention**). 

Unlike flat-file internship projects, this system addresses complex, messy, and real-world data engineering scenarios including relational database schemas, referential integrity constraints, automated data auditing, and multi-table star-schema analytical dimensional modeling.

---

## Architecture & Data Pipeline Flow

1. **Extraction (Data Generation):** Simulates raw, decentralized clinic reporting data featuring mixed string-numeric fields, mismatched primary/foreign keys, nested classifications, and logical boundary anomalies.
2. **Transform (Python / Pandas Engine):** Programmatic string sanitization, programmatic database schema normalization, handling out-of-bound variables, data imputation, and programmatic case conversion to match RDBMS standards.
3. **Load (PostgreSQL Storage):** Deployment of an automated `TRUNCATE ... CASCADE` data-purging protocol and bulk loading data engines while maintaining relational Primary Key (PK) and Foreign Key (FK) constraints.
4. **Model & Visual Analytics (Power BI / DAX):** Development of an automated, relational Star Schema ($1 \rightarrow *$) model, optimized cohort tracking metrics via explicit DAX measures, and a business intelligence executive performance dashboard.

---

## Database Schema Model (Star Schema)

The data warehouse implements an optimized Star Schema modeling pattern, ensuring complete analytical granularity while maintaining optimal database performance:

* **`dim_facilities` (Dimension Table):** Master clinic registry tracking facility unique codes (`fac_id`), facility names, regional sub-counties, and implementing/funding partners (e.g., CDC-Palladium, USAID-Afya).
* **`fact_patient_cohorts` (Fact Table):** Tracks transactional cohort metric counts over time, linked directly to the facility dimension through a strict Foreign Key relationship (`mfl_code` $\rightarrow$ `dim_facilities.fac_id`). Includes structured columns for demographics (`age_group`, `gender`) and longitudinal outcomes.

---

## Key Core Performance Metrics (DAX Engine)

The following explicit operational expressions were built into the Power BI analytical engine to assess retention and treatment drop-off rates across varying demographic cohorts:

* **ART Initiation Rate:** Calculates the proportion of diagnosed individuals successfully linked to and initiated on antiretroviral therapy.
  ```dax
  ART Initiation Rate = 
  DIVIDE(SUM('public fact_patient_cohorts'[initiated_on_art]), SUM('public fact_patient_cohorts'[diagnosed_count]), 0)
