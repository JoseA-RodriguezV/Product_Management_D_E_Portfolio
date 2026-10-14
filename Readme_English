# **Honduras Basic Food Basket Analytics**

## **Description & Objectives**

An end-to-end data pipeline was developed using price monitoring data for the basic food basket in Honduras. The primary data source consists of monthly documents published by the SDE (*Secretaría de Desarrollo Económico* - Ministry of Economic Development), which measures prices across markets in the country's two main cities. AI was leveraged to process the monthly PDF documents and serve the extracted data via an API, implementing a Medallion Architecture for data processing.

## **Hypotheses**

- Are product prices influenced by factors such as city, location, or external events?
- Is quarterly product price inflation tied to seasonal events or climate factors?

### **Objectives**

- Implement an automated monitoring system using a **Weekly Variation Dashboard** to track the prices of key basic food basket products in Honduras. This enables the identification and alerting of atypical price variations (exceeding a threshold weekly percentage) across major national markets throughout 2025–2026.
- Establish a predictive and historical analysis model to evaluate inflationary and seasonal behavior in the Honduran basic food basket, automating medium-term trend reports integrated with Databricks Medallion Architecture during 2025–2026.

## **Architecture**

To ensure data reliability, scalability, and cleanliness, the pipeline was built following a modern layered architecture (**Medallion Architecture**).

Best practices implemented include:
- Exploratory Data Analysis (EDA)
- Idempotency
- Unity Catalog
- Optimization via Liquid Clustering

<img width="2152" height="487" alt="Arch-Medallion" src="https://github.com/user-attachments/assets/80062b78-346f-427d-ac5f-a1716493ba85" />

- **Landing Layer:** The initial stage where raw data lands in Databricks. Data is received via API from DeepSeek for each PDF file sourced.
- **Bronze Layer:** Stores data in `STRING` format ingested from the Landing layer. EDA is performed here to assess data quality and apply initial treatments ensuring information integrity, cleanliness, and traceability.
- **Silver Layer:** Holds cleaned and transformed data standardized by data types. Idempotency is applied to prevent redundant records during migration from Bronze.
- **Gold Layer:** Hosts the dimensional model built using a **STAR SCHEMA**. It features 3 dimension tables (`Dim_Time`, `Dim_Product`, and `Dim_Market`) simplified and linked to the Fact table (`Fact_Products`) to store historical product price data. Slowly Changing Dimension Type 1 (**SCD_1**) logic is applied to `Fact_Products`, `Dim_Market`, and `Dim_Product`, while **SCD_0** is applied to `Dim_Time`.
- **Semantic Layer:** Contains created views to simplify queries and make data readily accessible for business stakeholders and analytical teams.

Data record counts across layers:
- 1. Bronze Layer: ==> 6,571
- 2. Silver Layer: ==> 5,733
- 3. Gold Layer: ==> 5,578

A **Databricks Job** was integrated to run bi-weekly, orchestrating the end-to-end process from the Bronze ETL to the Gold layer with an average execution time of ~2.30 minutes.

<img width="802" height="274" alt="JOB
