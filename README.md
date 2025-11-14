🧠 Media Content Analytics Platform


🎯 Project Overview

The Media Content Analytics Platform is a cloud-based end-to-end data pipeline built using Python, Google BigQuery, and Colab.
It ingests raw YouTube analytics data, cleans and transforms it, applies data quality rules, preserves historical changes (SCD Type-2), and builds a Star Schema for visualization-ready dashboards.

📊 Architecture Overview
CSV Files → Staging (Raw) → Bronze (Typed) → Silver (Curated) → Gold (Analytics)
                                               ↓
                                         dim_video (SCD2)


🔹 Technologies Used
Component	Technology
Language	Python (Pandas, BigQuery SDK)
Cloud Data Warehouse	Google BigQuery
Development Environment	Google Colabs
Data Source	YouTube Analytics CSVs, Kaggle News Dataset
Modeling	Star Schema (Fact + Dimension)
Historical Tracking	Slowly Changing Dimension Type-2
⚙️ Data Pipeline Layers
1️⃣ Staging Layer

Goal: Load raw CSVs into BigQuery with minimal cleaning.

Table	Source	Description
staging_aggregated_video	Aggregated_Metrics_By_Video.csv	Video-level performance metrics
staging_aggregated_country	Aggregated_Metrics_By_Country_And_Subscriber_Status.csv	Views by country and subscription type
staging_all_comments	All_Comments_Final.csv	YouTube comments dataset
staging_video_performance	Video_Performance_Over_Time.csv	Daily video performance trends

Cleaning Performed

Trim and normalize column names

Drop rows with missing primary keys

Fix malformed quotes in CSVs

Remove null or malformed rows

2️⃣ Bronze Layer

Goal: Structure and standardize staging data with proper data types.

Table	Description
bronze_aggregated_video	Parsed timestamps, cast numeric columns, converted duration strings to seconds
bronze_country_metrics	Cleaned and standardized subscription flags, fixed types
bronze_comments	Removed spam/invalid comments, converted dates

UDF Used:

CREATE OR REPLACE FUNCTION parse_duration(d STRING)
RETURNS INT64
LANGUAGE js AS
"""
  if (!d || d.trim() === '') return 0;
  const parts = d.split(':').map(Number);
  if (parts.length === 3) return parts[0]*3600 + parts[1]*60 + parts[2];
  if (parts.length === 2) return parts[0]*60 + parts[1];
  return parts[0] || 0;
"""

3️⃣ Silver Layer

Goal: Apply business logic and derived metrics for analytical use.

Filter invalid or spam comments

Aggregate subscriber vs non-subscriber metrics

Add derived KPIs (engagement %, average watch time, etc.)

Join across datasets for enrichment

4️⃣ Gold Layer (Analytics-Ready)

Goal: Final clean, partitioned, and clustered tables for dashboards.

Gold Table	Description	Partition / Cluster
gold_video_metrics	Core video-level KPIs	Partition: video_publish_date, Cluster: video_id
gold_country_metrics	Metrics per video & country	Cluster: video_id, country_code
gold_comments_clean	Filtered comments with spam detection	Partition: comment_date, Cluster: video_id
gold_creator_dashboard	Creator-centric summary dashboard	No partition/cluster
🧮 Dimensional Modeling
⭐ Schema Type: Star Schema
Type	Tables	Description
Fact Tables	gold_video_metrics, gold_country_metrics, gold_comments_clean	Contain measurable KPIs
Dimension Table	dim_video	Holds descriptive attributes of videos (supports SCD Type-2)
Example Relationship:
                  dim_video (SCD2)
                         |
     -------------------------------------------------
     |                      |                       |
gold_video_metrics   gold_country_metrics   gold_comments_clean

🔁 SCD Type-2 Implementation

The dim_video table maintains historical versions of each video’s metadata (e.g., title changes).

Column	Description
surrogate_key	UUID Primary Key
video_id	Natural key (from YouTube)
video_title	Video name
effective_from	When record became active
effective_to	When record was replaced (NULL = active)
is_current	TRUE = active version

MERGE Logic:

Detect changed video titles

Expire old record (is_current=FALSE, set effective_to)

Insert new current record with updated title

➡️ Ensures full historical traceability of metadata

🧩 Data Quality (DQ) Rules Applied
Check Type	Rule	Purpose
Schema Consistency	SAFE_CAST used for all numeric fields	Prevent load failure
Primary Key Validation	Drop rows with null video IDs	Ensure data integrity
Spam Filtering	Flag comments containing links or too short	Data cleanliness
Duplicate Handling	Group by unique video ID	Prevent double-count
Date Parsing	SAFE.PARSE_TIMESTAMP / SAFE.PARSE_DATE	Handle malformed dates
🚀 Execution Flow

Authenticate Google account in Colab

Load and clean CSVs → upload to BigQuery staging tables

Run ETL transformation notebook → build Bronze → Gold layers

Run SCD2 merge script → maintain historical dimension

Validate sample data and explore via dashboards (Streamlit / Looker / Power BI)

📈 Sample Output
Gold Table	Rows	Description
gold_video_metrics	223	Video performance KPIs
gold_country_metrics	54,905	Country-wise engagement
gold_comments_clean	8,252	Clean comments dataset
gold_creator_dashboard	223	Aggregated creator summary
💡 Key Highlights (Hidden Gems)

✅ Automated ETL with clean column normalization

✅ UDF-based duration parsing using JavaScript in BigQuery

✅ Data Quality checks embedded in every step

✅ SCD Type-2 dimension — preserves video title history

✅ Star Schema modeling for optimized analytics

✅ Cloud-native (serverless, scalable, low maintenance)

📂 Folder Structure Example
media-content-analytics/
│
├── notebooks/
│   ├── 01_staging_load.ipynb
│   ├── 02_bronze_gold_etl.ipynb
│   └── 03_scd2_dim_video.ipynb
│
├── data/
│   ├── Aggregated_Metrics_By_Video.csv
│   ├── Aggregated_Metrics_By_Country_And_Subscriber_Status.csv
│   ├── All_Comments_Final.csv
│   └── Video_Performance_Over_Time.csv
│
├── README.md
└── requirements.txt



Ankala Santhkumar
Data Engineering Enthusiast | Cloud & Analytics | BigQuery | Python | Streamlit

🏁 Future Enhancements

Add Streamlit Dashboard for real-time exploration

Integrate Sentiment Analysis on gold_comments_clean

Create automated DQ alert system

Schedule pipeline with Cloud Composer / Airflow