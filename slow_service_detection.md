## 🧩 2️⃣ Slow-Service Detection Flow

```mermaid
flowchart TD
  A["Gateway receives request"] --> B["Generate & propagate traceparent + x-request-id"]
  B --> C["Services emit spans (client/server) with timing and attributes"]
  C --> D["Envoy access log captures start/end, route, status, and trace_id"]
  D --> E["OTel/Data Prepper enriches and ships logs/traces to OpenSearch"]
  E --> F{"Query p95/p99 per service or edge<br/>in last N minutes"}
  F -->|p95 > threshold| G["Trigger OpenSearch Monitor alert<br/>with offending service or edge"]
  F -->|OK| H["No action"]
  G --> I["Notify channel (email / webhook / PagerDuty)"]
