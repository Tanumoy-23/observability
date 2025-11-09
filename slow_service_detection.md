
---

## 🧩 2️⃣ **Slow-Service Detection Flow**

```markdown
```mermaid
flowchart TD
  A["Gateway receives request"] --> B["Generate & propagate traceparent + x-request-id"]
  B --> C["Services emit spans (client/server) with timing + attributes"]
  C --> D["Envoy access log captures start/end, route, status, trace_id"]
  D --> E["OTel/Data Prepper enrich & ship logs/traces to OpenSearch"]
  E --> F{"Query p95/p99 per service or edge<br/>in last N mins"}
  F -->|p95 > threshold| G["Trigger OpenSearch Monitor alert<br/>with offending service/edge"]
  F -->|OK| H["No action"]
  G --> I["Notify channel (email/webhook/PagerDuty)"]
