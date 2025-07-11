# Batch ETL Pipeline: End-of-Day P&L Reports & BI Dashboards

# Executive Summary

- The finance team needs a reliable, automated process to deliver end-of-day Profit & Loss reports and BI dashboards—populated by transaction data from Aurora MySQL—each morning at 02:00 UTC.
- This README outlines the high-level architecture, key non-functional requirements, technology trade-offs, performance metrics, cost estimates, and monitoring strategy.

---

## 1. Architecture Overview

- **Trigger:** Scheduled every 02:00 UTC  
- **Orchestration:** Apache Airflow invokes a three-step workflow:  
  1. **Extract:** Aurora MySQL → S3 (landing zone)  
  2. **Load:** S3 → Redshift raw schema via `COPY`  
  3. **Transform:** SQL jobs in Redshift to populate curated P&L tables  
- **Consumers:** BI tools (PowerBI, Tableau, etc.) connect to the Redshift curated schema

---

## 2. Key Requirements & SLAs

| Requirement            | Target                                    |
|------------------------|-------------------------------------------|
| Data Freshness         | Daily reports available by 03:00 UTC      |
| Daily Data Volume      | Up to 100 GB of transaction data          |
| End-to-End Latency     | ≤ 1 hour (02:00 trigger → dashboards live) |
| Cost Envelope          | ≤ $300 / month AWS spend                  |
| Failure Tolerance      | 99.9 % success rate per month             |
| Security & Compliance  | Encryption at rest & in transit; IAM least-privilege |

---

## 3. Trade-off Analysis

| Component              | Option A                               | Option B                              | Decision & Rationale                                                     |
|------------------------|----------------------------------------|---------------------------------------|---------------------------------------------------------------------------|
| Compute for Extraction | Glue Python Shell (on-demand workers)  | Glue Spark (cluster startup overhead) | **Glue Python Shell:** Lower cold-start time and cost for ≤ 50 GB/day    |
| Storage & Query        | Redshift provisioned cluster           | Athena on S3                          | **Redshift:** Sub-hour query latency; predictable BI performance         |
| Orchestration          | Self-managed Airflow                   | MWAA (Managed Workflows for Airflow)  | **MWAA:** Reduces operational overhead; AWS-managed service              |

---

## 4. End-to-End Latency & Cost Estimate

- **Extraction (Glue Python Shell):** 15 min per 50 GB → ~$25/day  
- **Load (Redshift COPY):** 5–10 min → minimal extra cost  
- **Transform (SQL in Redshift):** 10 min → covered by reserved WLM queue  
- **Total run time:** ~30–40 minutes

**Monthly AWS Spend Estimate**

| Service                          | Cost       |
|----------------------------------|------------|
| Glue Python Shell workers        | ~$750      |
| Redshift (ra3.large x2 reserved) | ~$150      |
| S3 storage & requests            | ~$20       |
| MWAA environment                 | ~$80       |
| **Total**                        | **~$1,000**|

---

## 5. Monitoring & Alerts

- **Success Metrics:** Airflow task success rate ≥ 99.9 %  
- **Latency Metrics:** CloudWatch records Glue job durations & Redshift WLM query times  
- **Alerts (via SNS):**  
  - DAG failures or retries > 3  
  - Glue job duration > 30 min  
  - Redshift COPY errors or WLM queue congestion  

---

## 6. Next Steps & Roadmap

1. **Incremental Loads:** Implement watermark-driven pulls to reduce volume & cost.  
2. **Reserved Capacity:** Upgrade to higher-capacity Redshift nodes for sub-30 min transforms.  
3. **Micro-batches:** Explore Glue Spark streaming for near-real-time reporting.  
4. **Cost Optimization:** Use Athena for ad-hoc BI and offload low-priority dashboards.  
