## 🧩 3️⃣ End-to-End Request Sequence (A → E)

```mermaid
sequenceDiagram
  participant Client
  participant Gateway
  participant A
  participant B
  participant C
  participant D
  participant E
  participant OTel as "OTel/Data Prepper/OpenSearch"

  Client->>Gateway: HTTP request (no trace)
  Note over Gateway: Set x-request-id & traceparent
  Gateway->>A: Forward with headers
  A->>B: Call with context (child span)
  B->>C: Call with context (child span)
  C-->>B: 500 + error log (span status = ERROR)
  B-->>A: Propagate error (latency recorded)
  A-->>Gateway: 500 to client
  Gateway-->>Client: Response (500)

  Note over A: OTel SDK exports spans + logs
  Note over Gateway: Envoy ALS access logs to OTel
  A->>OTel: OTLP traces / logs
  OTel->>OTel: Correlate & route
  OTel->>OpenSearch: Traces + Logs
