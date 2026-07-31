# 🛒 Supermarket Enterprise Data Warehouse & BI Analytics Platform

An end-to-end Data Engineering and Business Intelligence solution designed to transform raw supermarket operational point-of-sale (POS) data into an optimized Kimball Star Schema Data Warehouse, delivering executive-level insights via an interactive Power BI dashboard[cite: 7].

---

## 🏗️ Architecture & Pipeline Overview

```
[ Raw CSV / POS Data ] 
       │
       ▼
[ SQL Staging: StageSale ] ───( Deduplication via ROW_NUMBER() )
       │
       ▼
[ Enterprise Star Schema DW ] ───( Range Partitioning & Custom Indexing )
       │
       ▼
[ Power BI VertiPaq Engine ] ───( Import Mode & DAX Metrics ) ──► [ Executive Dashboard ]
```

---

## 📐 Dimensional Data Model (Kimball Methodology)

The analytical model is structured around a central **Fact Table** surrounded by five **Conformed Dimensions** at the individual line-item transaction grain[cite: 7, 8]:

* **Fact Table:**
  * `FactSales`: Fully additive metrics (`Sales`, `GrossIncome`, `COGS`, `Tax`, `Quantity`) and non-additive attributes (`Rating`, `GrossMarginPercentage`) alongside surrogate foreign keys[cite: 7, 8].
* **Dimension Tables:**
  * `DimDate`: Standard calendar time-intelligence hierarchy[cite: 7, 8].
  * `DimCustomer`: Demographics and customer loyalty membership attributes (`Member` vs `Normal`)[cite: 7, 8].
  * `DimProduct`: Product lines and unit price attributes[cite: 7, 8].
  * `DimBranch`: Store locations and cities (`Yangon`, `Naypyitaw`, `Mandalay`)[cite: 7, 8].
  * `DimPayment`: Checkout payment channels (`E-Wallet`, `Cash`, `Credit Card`)[cite: 7, 8].

---

## ⚡ T-SQL ETL & Performance Tuning

### 1. Staging & Data Cleaning
* Operational data is initially ingested into `StageSale` to isolate production systems from analytical workloads[cite: 7, 8].
* Automated deduplication purges duplicate point-of-sale entries using `ROW_NUMBER() OVER (PARTITION BY Invoice_ID ...)`[cite: 7, 8].
* Joins bridge staging operational text attributes to generate fast auto-incrementing integer surrogate identity keys across lookup tables[cite: 7, 8].

### 2. Database Optimization Highlights
* **Clustered Indexes:** Default primary key B-Tree indexes built on primary surrogate keys[cite: 7, 8].
* **Foreign Key Non-Clustered Indexes:** Applied to foreign keys (`IX_FactSales_DateKey`, `IX_FactSales_BranchKey`, etc.) to eliminate full table scans during star joins[cite: 7, 8].
* **Composite Indexing:** Built `IX_Date_Product` on `(DateKey, ProductKey)` to accelerate multi-dimensional date and product query aggregations[cite: 7, 8].
* **Table Partitioning:** Implemented `pfSalesYear` (Partition Function) and `psSalesYear` (Partition Scheme) on `YearNumber` for efficient partition pruning during date queries[cite: 7, 8].

---

## 📊 Analytics & Power BI Integration

Integrated into Power BI Desktop via **Import Mode**, utilizing VertiPaq dictionary encoding and bit-packing for fast visual responsiveness[cite: 7].

### Core DAX KPI Metrics
| Measure Name | DAX Formula | Description |
| :--- | :--- | :--- |
| **Total Sales** | `Total Sales = SUM(FactSales[Sales])` | Sum of total revenue generated[cite: 8]. |
| **Total Profit** | `Total Profit = SUM(FactSales[GrossIncome])` | Sum of gross profit income[cite: 8]. |
| **Total Quantity** | `Total Quantity = SUM(FactSales[Quantity])` | Total item units sold[cite: 8]. |
| **Average Rating** | `Average Rating = AVERAGE(FactSales[Rating])` | Mean customer feedback rating score[cite: 8]. |
| **Total Transactions** | `Transactions = COUNT(FactSales[InvoiceID])` | Total unique completed invoices[cite: 8]. |

---

## 💡 Key Business Insights Discovered

* **Branch Revenues:** Store Branch C (Naypyitaw) led total revenue generation, closely followed by Branch A (Yangon) and Branch B (Mandalay)[cite: 7].
* **Profit Drivers:** *Health & Beauty* and *Home & Lifestyle* categories generated high sales volumes, with *Health & Beauty* yielding the highest net profit contribution[cite: 7].
* **Customer Loyalty:** Registered **Member** shoppers generated **52.29%** of total revenue versus non-member **Normal** shoppers (47.71%)[cite: 7].
* **Payment Diversity:** Customer checkout choices were evenly split between Cash (34.72%), E-Wallet (33.61%), and Credit Card (31.67%)[cite: 7].

---

## 📂 Repository Structure

```text
supermarket-dw-bi-solution/
├── assets/
│   ├── dashboard_overview.png
│   ├── data_model_star_schema.png
│   └── etl_architecture.png
├── sql/
│   ├── 01_schema_ddl.sql          # Database, Tables, & Foreign Key Constraints
│   ├── 02_partitioning.sql        # Partition Functions & Partition Schemes
│   ├── 03_indexing.sql            # Clustered, Non-Clustered & Composite Indexes
│   ├── 04_staging_load.sql        # Staging tables setup
│   └── 05_etl_pipeline.sql        # Deduplication (ROW_NUMBER) & Key Mappings
├── powerbi/
│   ├── Supermarket_Analytics.pbix  # Desktop Power BI Report file
│   └── dax_measures.dax           # Extracted DAX code for documentation
├── data/
│   └── raw_supermarket_sales.csv  # Sample raw operational data
├── .gitignore
├── LICENSE
└── README.md
```

---

## 🚀 How to Deploy & Run

### Prerequisites
* Microsoft SQL Server 2019+ or Azure SQL Database
* SQL Server Management Studio (SSMS)
* Microsoft Power BI Desktop

### Installation Steps

1. **Clone the repository:**
   ```bash
   git clone [https://github.com/your-username/supermarket-dw-bi-solution.git](https://github.com/your-username/supermarket-dw-bi-solution.git)
   ```

2. **Setup Database & Execute ETL:**
   * Run `sql/01_schema_ddl.sql` to generate the staging, fact, and dimension tables[cite: 7, 8].
   * Run `sql/02_partitioning.sql` and `sql/03_indexing.sql` to configure performance optimizations[cite: 8].
   * Load `data/raw_supermarket_sales.csv` into `StageSale` and execute `sql/05_etl_pipeline.sql`[cite: 7, 8].

3. **Open Power BI Dashboard:**
   * Launch `powerbi/Supermarket_Analytics.pbix`.
   * Update the SQL Server data source credentials under **Transform Data > Data Source Settings**.
   * Refresh data connections.

---

## 🛠️ Technologies Used

* **Database Engine:** Microsoft SQL Server (T-SQL)
* **Modeling & Design:** Dimensional Modeling (Kimball Star Schema)
* **Visualization Engine:** Microsoft Power BI Desktop
* **Languages:** T-SQL, DAX
