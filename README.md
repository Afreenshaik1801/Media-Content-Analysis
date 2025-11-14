<h1 align="center">🧠 Media Content Analytics Platform</h1>

<p align="center">
An end-to-end <b>cloud-native data engineering platform</b> for YouTube analytics.  
This project ingests, transforms, and models YouTube datasets into <b>analytics-ready tables</b> enabling dashboards and actionable insights.
</p>

---

<h2>📘 Table of Contents</h2>

<ul>
  <li><a href="#overview">⭐ Overview</a></li>
  <li><a href="#architecture">🏗️ Architecture</a></li>
  <li><a href="#features">⚙️ Pipeline Layers</a></li>
  <li><a href="#data-source">📈 Data Sources</a></li>
  <li><a href="#stack">🛠 Technology Stack</a></li>
  <li><a href="#structure">📂 Project Structure</a></li>
  <li><a href="#setup">⚙️ Setup & Execution</a></li>
  <li><a href="#modeling">🌟 Dimensional Modeling</a></li>
  <li><a href="#dq">🧪 Data Quality Rules</a></li>
  <li><a href="#enhancements">🚀 Future Enhancements</a></li>
  <li><a href="#author">👤 Author</a></li>
</ul>

---

<h2 id="overview">⭐ Overview</h2>

<p>
The <b>Media Content Analytics Platform</b> is a cloud-first data engineering solution for YouTube analytics.  
It supports end-to-end data processing, from raw ingestion to analytics-ready outputs with historical tracking via <b>SCD Type-2</b>.
</p>

<ul>
  <li>📊 Load and clean YouTube analytics CSV datasets</li>
  <li>🔄 Apply multi-layer transformations (Raw → Bronze → Silver → Gold)</li>
  <li>🧮 Track historical changes in metadata (SCD Type-2)</li>
  <li>📈 Deliver dashboards and KPI tables for insights</li>
</ul>

---

<h2 id="architecture">🏗️ Architecture</h2>

<pre>
CSV Inputs
↓
Staging (Raw Layer)
↓
Bronze (Typed & Cleaned)
↓
Silver (Business Logic Applied)
↓
Gold (Analytics & Star Schema)
↓
dim_video (SCD Type-2)
</pre>

<ul>
  <li><b>Raw Layer:</b> Original YouTube CSV datasets</li>
  <li><b>Bronze:</b> Standardized, typed, and cleaned</li>
  <li><b>Silver:</b> Derived metrics, spam filtering, enriched data</li>
  <li><b>Gold:</b> Analytics-ready fact tables for dashboards</li>
</ul>

---

<h2 id="features">⚙️ Pipeline Layers</h2>

<h3>1️⃣ Staging (Raw Layer)</h3>
<ul>
  <li>Minimal processing, load datasets as-is</li>
  <li>Tables: <code>staging_aggregated_video</code>, <code>staging_aggregated_country</code>, <code>staging_all_comments</code>, <code>staging_video_performance</code></li>
  <li>Cleaning: trim columns, drop null keys, fix CSV issues</li>
</ul>

<h3>2️⃣ Bronze Layer</h3>
<ul>
  <li>Standardize formats and data types</li>
  <li>Parse durations, cast timestamps and numerics</li>
  <li>Remove invalid comment records</li>
</ul>

<h3>3️⃣ Silver Layer</h3>
<ul>
  <li>Apply business logic</li>
  <li>Spam detection, subscriber vs non-subscriber analysis</li>
  <li>Derived KPIs: engagement %, avg watch time, CTR</li>
  <li>Join datasets for enriched insights</li>
</ul>

<h3>4️⃣ Gold Layer (Analytics Ready)</h3>
<ul>
  <li>Dashboard-ready fact tables with partitions and clustering</li>
  <li>Tables: <code>gold_video_metrics</code>, <code>gold_country_metrics</code>, <code>gold_comments_clean</code>, <code>gold_creator_dashboard</code></li>
</ul>

---

<h2 id="data-source">📈 Data Sources</h2>

<table>
  <tr><td><b>Source Files:</b></td><td>YouTube Analytics CSVs, Kaggle</td></tr>
  <tr><td><b>Tables:</b></td><td>Aggregated metrics, comments, video performance</td></tr>
</table>

---

<h2 id="stack">🛠 Technology Stack</h2>

<table>
  <tr><th>Component</th><th>Technology</th></tr>
  <tr><td>Programming</td><td>Python (Pandas, BigQuery SDK)</td></tr>
  <tr><td>Data Warehouse</td><td>Google BigQuery</td></tr>
  <tr><td>Development</td><td>Google Colab</td></tr>
  <tr><td>Modeling</td><td>Star Schema, SCD Type-2</td></tr>
</table>

---

<h2 id="structure">📂 Project Structure</h2>

<pre>
media-content-analytics/
│
├── Raw_datasets/
├── Transformed_datasets/
├── source_code/
│   ├── 3_Gold_Dashboard_&_DQ.ipynb
│   ├── Complete_code.ipynb
├── Presentation.pdf
├── Requirements.txt
└── README.md
</pre>

---

<h2 id="setup">⚙️ Setup & Execution</h2>

<ol>
  <li>Authenticate Google account in Colab</li>
  <li>Load & preprocess CSVs → upload to BigQuery</li>
  <li>Run ETL notebooks to build Bronze → Silver → Gold</li>
  <li>Execute SCD Type-2 merge logic</li>
  <li>Validate data & explore insights via dashboards (Looker/Power BI/Streamlit)</li>
</ol>

---

<h2 id="modeling">🌟 Dimensional Modeling (Star Schema)</h2>

<ul>
  <li>Fact Tables: <code>gold_video_metrics</code>, <code>gold_country_metrics</code>, <code>gold_comments_clean</code></li>
  <li>Dimension Table: <code>dim_video</code> (SCD Type-2)</li>
</ul>

<pre>
dim_video (SCD2)
      │
      ├── gold_video_metrics
      ├── gold_country_metrics
      └── gold_comments_clean
</pre>

---

<h2 id="dq">🧪 Data Quality Rules</h2>

<table>
  <tr><th>Check</th><th>Description</th><th>Purpose</th></tr>
  <tr><td>Schema Consistency</td><td>SAFE_CAST for numeric fields</td><td>Prevent load failures</td></tr>
  <tr><td>Primary Key Validation</td><td>Drop null keys</td><td>Integrity guarantee</td></tr>
  <tr><td>Spam Filtering</td><td>Remove comments with links/low quality</td><td>Clean analytics</td></tr>
  <tr><td>Duplicate Handling</td><td>Deduplicate by video_id</td><td>Prevent skewed metrics</td></tr>
  <tr><td>Date Parsing</td><td>SAFE.PARSE_* functions</td><td>Robust handling</td></tr>
</table>

---

<h2 id="enhancements">🚀 Future Enhancements</h2>

<ul>
  <li>Streamlit dashboard for real-time insights</li>
  <li>Sentiment analysis on comments dataset</li>
  <li>Automated DQ alert system</li>
  <li>Airflow/Cloud Composer for scheduled ETL</li>
  <li>Expand star schema with additional dimensions</li>
</ul>

---

<h2 id="author">👤 Author</h2>

<p>
👨‍💻 <b>Afreen Shaik</b><br>
📧 <i>shaikafreen1801@gmail.com</i><br>
🔗 <a href="https://github.com/Afreenshaik1801" target="_blank">GitHub Profile</a>
</p>
