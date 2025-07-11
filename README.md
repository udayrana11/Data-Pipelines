```markdown
# Data Pipelines Comparison, read attached files 

Three patterns: **Batch**, **Event-Driven**, **Real-Time Streaming**.

---

## 1. Batch Pipeline
- **Use Case:** End-of-day ETL and reporting
- **Latency:** 30–60 min
- **Throughput:** GBs/hr
- **Cost:** \$300–\$1,000/mo
- **Pros/Cons:** Simple, predictable vs. high latency, resource spikes

---

## 2. Event-Driven Pipeline
- **Use Case:** Order processing & inventory sync
- **Latency:** ≤ 500 ms
- **Throughput:** 100s–1,000s evt/sec
- **Cost:** \$200–\$1,000/mo
- **Pros/Cons:** Low latency, loose coupling vs. complexity, DLQs

---

## 3. Real-Time Streaming
- **Use Case:** Recommendations, fraud scoring, dashboards
- **Latency:** ≤ 1 s
- **Throughput:** 10,000s evt/sec
- **Cost:** \$1,000–\$2,500/mo
- **Pros/Cons:** Ultra-low latency analytics vs. high ops effort

---

## Trade-offs & Selection
| Pattern         | Latency   | Cost (mo)        | When to choose                              |
|-----------------|-----------|------------------|----------------------------------------------|
| **Batch**       | 30–60 min | \$300–\$1,000    | Non-real-time, simple ETL                   |
| **Event-Driven**| ≤ 500 ms  | \$200–\$1,000    | Near-real-time workflows, decoupled services |
| **Streaming**   | ≤ 1 s     | \$1,000–\$2,500  | Continuous analytics, live dashboards        |

---

