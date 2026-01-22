# E-Commerce Data Engineering & BI Analytics

![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white)
![Tableau](https://img.shields.io/badge/Tableau-E97627?style=for-the-badge&logo=Tableau&logoColor=white)
![SQL](https://img.shields.io/badge/SQL-00758F?style=for-the-badge&logo=mysql&logoColor=white)

## 📌 Project Overview
This project demonstrates the construction of a modern Data Lakehouse architecture, successfully transitioning over 1.5GB of raw, e-commerce behavior logs into a high-performance executive dashboard. Beyond traditional sales reporting, the system powers an interactive Tableau dashboard that provides a comprehensive analysis of brand performance by deep-diving into Customer Behavior. By distinguishing between pure Volume (traffic) and Engagement (stickiness), the platform is designed to help stakeholders identify "market leaders," "niche favorites," and critical gaps in the conversion funnel. This scalable, cloud-native approach bridges the gap between complex data engineering—utilizing AWS S3, Glue, and Athena—and actionable business strategy, ensuring that data is not just stored, but transformed into a roadmap for growth.

## 🏗 System Architecture & Workflow
The project follows a **"Schema-on-Read"** architecture, leveraging AWS serverless tools for maximum scalability and cost-efficiency.

![Architecture Diagram](Architecture%20diagram.gif)

* **Storage (Amazon S3)**: Raw .csv datasets containing millions of rows of user behavior data (views, cart additions, purchases) were ingested into S3 buckets.

* **Schema Discovery (AWS Glue Crawler)**: Configured a Crawler to automatically scan the raw data. This inferred data types (identifying Decimals, Strings, and Timestamps) and mapped the initial schema.

* **Data Transformation (AWS Glue ETL)**: Developed a specialized Glue Job, ETL_Ecommerce_CSV_to_Parquet, to convert raw CSVs into Apache Parquet format. This step optimized the architecture by utilizing columnar storage, significantly reducing query costs and increasing speed.

* **Metadata Management (AWS Glue Data Catalog)**: The transformed data was registered in the Data Catalog, creating a centralized metadata repository that allows Athena to treat S3 files as structured SQL tables (Schema-on-Read).

* **Query Engine (Amazon Athena)**: Leveraged serverless SQL to perform complex aggregations, calculating metrics like "Stickiness" and "Conversion Rates" directly against the S3 Data Lake.

* **BI Visualization (Tableau)**: Established a secure connection via JDBC and IAM Access Keys to translate SQL insights into a high-fidelity, interactive dashboard.

---

## 📊 Dashboard Insights & Analysis
![Tableau Visualization](tableau%20viz.gif)
### 1. Executive KPI Layer
* **Total Revenue:** High-level sales performance at a glance.
* **Active Shoppers:** Measuring the total reach and scale of the platform.
* **Market Share %:** Calculated as `SUM(Brand Revenue) / TOTAL(Revenue)`.

### 2. Competitive Benchmarking
* **Revenue vs. Popularity:** Compares brands by Total Sales against Total Shoppers. 
* *Insight:* Brands with high shopper volume but lower revenue rank highlight a lower **Average Order Value (AOV)** opportunity.

### 3. Brand Exploration Depth (Stickiness)
* **Metric:** `COUNT([Event Type]) / COUNTD([User Session])`
* **Concept:** Measures how many actions (view, cart, click) a user takes per session. High stickiness indicates strong brand engagement.

### 4. Performance Matrix (Efficiency)
* **Metric:** `Purchase Count / Total Sessions` (Conversion Rate).
* *Insight:* Categorizes brands into quadrants: **Market Leaders** (High Traffic/High Conversion) vs. **Niche Gems** (High Efficiency/Low Traffic).

---

## 🛠 Technical Implementation

### Data Source
> **Dataset Availability:** To optimize cloud storage costs, the 1.5GB raw dataset is hosted externally.
> 🔗 [Access the Dataset on Kaggle](https://www.kaggle.com/datasets/saumyasinha0510/e-commerce-data-engineering-bi-analytics)

### AWS Configuration
* **IAM Security:** Implemented "Least Privilege" access by creating a specific IAM user for Tableau connection.
* **S3 Partitioning:** Organized data to optimize Athena query costs and performance.

### Tableau Calculated Fields (Logic)
sql
  ** Brand Stickiness (Window Shopping Depth): COUNT([Event Type]) / COUNTD([User Session])
  ** Conversion Rate: COUNT(IF [Event Type] = 'purchase' THEN [Event Type] END) / COUNTD([User Session]


## 📈 Key Findings & Business Impact

The analysis revealed critical insights into market dynamics and consumer behavior that drive strategic decision-making:

* **🚀 The Market Pareto Effect:** Analysis confirmed a "Winner-takes-all" environment where **71% of total market share** is dominated by a small fraction of top-tier brands.
* **🔍 Engagement vs. Sales Paradox:** While "Giant" brands like Samsung and Apple lead in raw traffic, they don't always lead in **Stickiness**. Several mid-tier brands showed higher "Exploration Depth," suggesting a more loyal or deeply engaged researcher base.
* **💡 Growth Opportunity Mapping:** By cross-referencing conversion rates with engagement, I identified **"High Stickiness / Low Conversion"** brands. These represent significant untapped revenue; if checkout friction is reduced, these brands are primed for rapid sales growth.

---

## 🛠 Challenges & Troubleshooting

Building in the cloud rarely goes perfectly on the first try. Below are the technical hurdles overcome during development:

### 1. The "Schema (0)" Issue
**Challenge:** The Glue Crawler ran successfully, but Athena showed 0 columns for the table.  
**Resolution:** Discovered the Glue ETL Job was saving data to a default `asset/` folder. I manually corrected the **S3 Target Path** in Glue Studio to the designated `processed/` folder and re-ran the crawler.

### 2. S3 Permission (403 Forbidden)
**Challenge:** The Glue Job failed during the "Write" phase.  
**Resolution:** Identified that the IAM Role lacked `PutObject` rights. Attached the necessary policy to give Glue the "pen" it needed to write Parquet files to S3.


