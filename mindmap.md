
---

## 🧩 4️⃣ **Mindmap – Observability Plan**

```markdown
```mermaid
mindmap
  root((Observability Plan))
    Correlation
      W3C traceparent
      x-request-id at Gateway
      Propagation across SDKs
    Gateway Logs
      Envoy JSON fields
      Ship via OTel/Data Prepper
      Correlate to traces
    Tracing
      OTel SDKs client+server spans
      Istio Envoy tracing on
      Service map & edge latency
      Error spans with logs
    Alerting
      p95/p99 per service & per edge
      Error rate monitors
      SLO (availability/latency)
    Log Cost Control
      Runtime level toggle per service
      error/warn → OpenSearch
      info/debug → file rotation & FIFO
      OpenSearch ISM rollover/delete
    Governance
      Config via ConfigMap/CRD
      Per-tenant/per-service policies
      Dashboards & runbooks
