## 🧩 Tail-Based Sampling Flow

```mermaid
graph LR
  A["Service A / OTel SDK"] -->|Spans| B["OTel Collector / Data Prepper"]
  B -->|"Evaluates trace result (status)"| C{"Tail-based Sampler"}
  C -->|"status = ERROR or latency > threshold"| D["Export trace to OpenSearch"]
  C -->|"status = OK"| E["Drop trace"]
