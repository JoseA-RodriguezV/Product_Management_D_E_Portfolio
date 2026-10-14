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

<img width="802" height="274" alt="JOB" src="https://github.com/user-attachments/assets/d234fae2-e105-465c-8d85-e49d6a9245e6" />

<img width="970" height="527" alt="Job_2" src="https://github.com/user-attachments/assets/fa685582-d3bb-4c7a-9dbf-42b0d409c618" />

## **Technologies Used**

* **Processing Engine:** Databricks / Apache Spark
* **Languages:** SQL (Spark SQL), Python
* **Storage:** Delta Lake & Databricks
* **Orchestration:** Databricks Workflows (Bi-weekly Schedule)
* **Version Control:** Git / GitHub
* **Reporting:** PowerBI connected to Databricks
* **Other Technologies:** DeepSeek API

## **Data Modeling**

The Gold Layer uses a **Star Schema** design to preserve data granularity while optimizing queries and relationships with the Fact table.

### **Dimensional Tables:**
* **`Dim_Time` (SCD_0):** Stores time periods for price analysis; static due to zero historical modification.
* **`Dim_Product` (SCD_1):** Contains the catalog of basic food basket items.
* **`Dim_Market` (SCD_1):** Contains market locations where product price monitoring takes place.
* **`Fact_Products` (Fact Table):** Designed with optimal granularity, linking Foreign Keys (FK) to Primary Keys (PK) in the dimension tables to store historical product pricing.

## **Analytical Dashboard**

The dashboard was built in Power BI by connecting directly to Databricks to consume semantic views.

It features 4 reports with custom KPIs:
1. **Cost of Living Comparison:** Compares product prices and living costs across different cities and departments for a selected week.
2. **Weekly Variation Monitoring:** Tracks week-over-week percentage changes in prices by product, city, and department, instantly detecting anomalies or critical price spikes.
3. **Historical Trend Analysis:** Evaluates long-term price fluctuations across weeks and years on a chronological axis.
4. **Macroeconomic Cycle Evaluation:** Analyzes quarterly macroeconomic cycles to support decision-making based on recurring patterns.

<img width="879" height="516" alt="DS_1" src="https://github.com/user-attachments/assets/2a065b62-7845-42e8-b5cb-794bbad5f00c" />

> **Dashboard #1:** Cost of living and product price comparisons across cities and departments for the target week.

<img width="878" height="521" alt="DS_2" src="https://github.com/user-attachments/assets/226009e2-68ba-4b7f-983a-bf1427e31582" />

> **Dashboard #2:** Week-over-week percentage variation tracking to flag anomalies and severe price increases.

<img width="875" height="521" alt="DS_3" src="https://github.com/user-attachments/assets/044c8041-8b0d-4653-9ecd-fe21c19eaf61" />

> **Dashboard #3:** Historical price behavior analysis over time across weeks and years.

<img width="881" height="520" alt="DS_4" src="https://github.com/user-attachments/assets/18a83a5c-05a7-4283-b3fc-9f4158003a2f" />

> **Dashboard #4:** Quarterly macroeconomic trend assessment for pattern-driven decision-making.

## **Data Source**

Data is sourced from PDF bulletins published by the Honduran Ministry of Economic Development (SDE) on their portal: [SDE Consumer Protection Bulletins](https://sde.gob.hn/proteccion-al-consumidor/boletines/).

Bulletins are published bi-weekly or monthly, tracking 30 basic food basket products across approximately 8 markets in Honduras's two largest departments.

## **Repository Structure**

This GitHub repository is structured into the following directories, each containing notebook files and layer-specific scripts:

- **`DDL/`**: Schema, Delta table, and view creation DDL scripts.
- **`EDA/`**: Notebooks dedicated to exploratory data analysis.
- **`ETL/`**: Data extraction, transformation, and loading pipelines for each Medallion layer.
- **`Dashboard/`**: Backups and files for Power BI report integration.
- **`Jobs/`**: Workflow job definitions, schedules, and parameter configurations.
- **`PDF_Files/`**: Raw PDF files storing monthly price reports, ingested by the automated job to feed DeepSeek for parsing.
