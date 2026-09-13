# 🏥 PM-JAY Fraud, Waste & Abuse (FWA) Detection System

> **End-to-end healthcare fraud analytics pipeline built on Snowflake + Power BI, simulating real-world abuse detection for India's Ayushman Bharat PM-JAY scheme.**

![Snowflake](https://img.shields.io/badge/Snowflake-Data%20Warehouse-29B5E8?style=for-the-badge&logo=snowflake&logoColor=white)
![Power BI](https://img.shields.io/badge/Power%20BI-Dashboard-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![SQL](https://img.shields.io/badge/SQL-Advanced-4479A1?style=for-the-badge&logo=database&logoColor=white)
![Healthcare](https://img.shields.io/badge/Domain-Healthcare%20Analytics-00C49F?style=for-the-badge)

---

## 📊 Key Findings at a Glance

| Metric | Value |
|---|---|
| Total Claims Analyzed | 5,48,001 |
| Fraud Records Detected | 37,848 |
| Fraud Rate | **6.91%** |
| Total Billed Amount | ₹231.83 Crores |
| States Covered | Pan-India |
| Hospital Types | Private, Government, Trust, Charitable, Teaching |

---

## 🎯 Project Objective

PM-JAY (Pradhan Mantri Jan Arogya Yojana) is one of the world's largest government-funded health insurance schemes, covering 50 crore+ beneficiaries. Fraud, Waste & Abuse costs the scheme hundreds of crores annually through:

- **Overbilling** — hospitals charging above approved NHA rates
- **Ghost procedures** — procedures billed but never performed
- **Zero-day admissions** — claims with 0 length of stay
- **Duplicate claims** — same beneficiary, same procedure, same window

This project builds a full FWA detection pipeline from raw data ingestion to an executive fraud dashboard, using tools standard in GCC and hospital analytics environments.

---

## 🛠️ Tech Stack

| Layer | Tool | Purpose |
|---|---|---|
| Data Warehouse | **Snowflake** | Storage, compute, security |
| Query Language | **SQL (Advanced)** | All analytics, fraud logic |
| BI / Reporting | **Power BI** | Executive dashboard |
| Data Source | NHA HBP 2.1 codes | Real Health Benefit Package reference |
| Schema Design | Star Schema | Optimized for analytics queries |

---

## 🗂️ Project Architecture

```
Raw Claims Data (CSV)
        │
        ▼
┌─────────────────────────────────────────┐
│           SNOWFLAKE PIPELINE            │
│                                         │
│  Stage → COPY INTO → Star Schema        │
│                                         │
│  DIM_HOSPITALS   DIM_BENEFICIARIES      │
│  DIM_PACKAGES    DIM_DOCTORS            │
│        └──────► FACT_CLAIMS ◄───────┘  │
│                                         │
│  Fraud Analytics → Views → RBAC        │
│  Row Access Policy → Dynamic Masking   │
│  Time Travel → Audit Trail             │
└─────────────────────────────────────────┘
        │
        ▼
┌─────────────────────────────────────────┐
│         POWER BI DASHBOARD              │
│                                         │
│  KPI Cards  │  Fraud Rate by Hospital   │
│  Donut Chart│  Fraud Claims by Type     │
└─────────────────────────────────────────┘
```

---

## 📁 SQL Pipeline — 9 Sections

### Section 1 — Database & Schema Setup
Created `PMJAY_FWA_DB` database with `CLAIMS` schema. Defined warehouse `PMJAY_WH` with auto-suspend.

### Section 2 — External Stage & File Format
Configured Snowflake Stage with CSV file format (header skip, null handling) for bulk data loading.

### Section 3 — COPY INTO & Data Ingestion
Loaded 5,48,001 raw claim records into `FACT_CLAIMS` and all dimension tables using `COPY INTO` with error handling.

### Section 4 — Data Validation
Row count checks, NULL audits, referential integrity validation across all dimension-fact joins. Confirmed zero orphan records.

### Section 5 — Fraud Analytics (Core Logic)
Multi-rule fraud detection using SQL window functions, CASE logic, and aggregations:
- Overbilling detection (BILLED_AMOUNT > APPROVED_RATE)
- Zero length-of-stay flagging
- Duplicate claim identification using `ROW_NUMBER() OVER PARTITION`
- Hospital-level fraud rate calculation
- State-wise exposure summary

### Section 6 — RBAC (Role-Based Access Control)
Created roles `PMJAY_AUDITOR` and `PMJAY_ANALYST` with principle of least privilege. SYSADMIN intentionally blocked from sensitive claim data.

### Section 7 — Row Access Policy
State-level data partitioning — each state auditor sees only their state's claims. `PMJAY_AUDITOR` role granted full cross-state visibility for central audit.

### Section 8 — Dynamic Data Masking
Sensitive PII fields (Aadhaar hash, phone, address) masked by default. Masking policy grants unmasked view only to `PMJAY_AUDITOR` role.

Two analytical views created:
- `VW_FRAUD_SUSPECTS` — All flagged/overbilled claims with hospital and beneficiary detail
- `VW_PROVIDER_SUMMARY` — Hospital-level fraud rate, claim volume, and billing aggregates

### Section 9 — Time Travel & Audit Trail
Demonstrated Snowflake Time Travel (`DATA_RETENTION_TIME_IN_DAYS = 1`) for point-in-time query and accidental deletion recovery — critical for regulatory audit trails in healthcare.

---

## 📈 Power BI Dashboard

**Dashboard: PM-JAY FWA Executive View**

| Visual | Insight |
|---|---|
| KPI — Total Claims | 50K (fraud suspects subset) |
| KPI — Fraud Records | 2 (filtered view) |
| KPI — Fraud Rate % | 6.97% |
| KPI — Total Billed (Lakhs) | ₹200.58K |
| Bar Chart | Fraud Rate % by Hospital (Top 10 by volume) |
| Donut Chart | Claims distribution by Hospital Type |
| Column Chart | Fraud Claims count by Hospital Type — Private sector leads (18.9K) |

**Design choices:** Dark theme (`#0D1117` canvas), amber KPIs for volume metrics, red (`#FF4444`) for fraud counts — color meaning is consistent throughout.

> *Dataset: Synthetic PM-JAY claims data generated for portfolio demonstration. Procedure codes reference real NHA HBP 2.1 package codes.*

---

## 🔐 Security Features Demonstrated

- ✅ Role-Based Access Control (RBAC) with 3 privilege tiers
- ✅ Row Access Policy (state-level data isolation)
- ✅ Dynamic Data Masking (PII protection)
- ✅ Time Travel (audit trail & recovery)
- ✅ Principle of Least Privilege (SYSADMIN blocked from claim data)

---

## 🚀 How to Reproduce

1. Clone this repository
2. Open `PM-JAY FWA Detection.sql` in Snowflake Worksheet
3. Execute sections 1–9 in order
4. Export `VW_FRAUD_SUSPECTS` and `VW_PROVIDER_SUMMARY` as CSV using role `PMJAY_AUDITOR`
5. Load CSVs into Power BI via Text/CSV connector
6. Apply dark canvas theme and rebuild visuals as documented

> **Note:** Snowflake trial accounts suspend the warehouse frequently. Run `ALTER WAREHOUSE PMJAY_WH RESUME;` if queries time out.

---

## 👤 Author

**Vikas Tarikere**
Healthcare Data Analyst | 9+ Years in Healthcare Domain

📍 Bengaluru, India
🔗 [LinkedIn](https://www.linkedin.com/in/) ← *(add your profile URL)*

---

## 📌 Tags

`#Snowflake` `#PowerBI` `#HealthcareAnalytics` `#FraudDetection` `#PMJAY` `#AyushmanBharat` `#SQL` `#DataEngineering` `#GCC` `#PortfolioProject`
