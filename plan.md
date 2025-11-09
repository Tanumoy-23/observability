graph TD

  subgraph Client
    U["User / Caller"]
  end

  subgraph Edge
    GW["Envoy/Istio Gateway<br/>injects x-request-id + traceparent<br/>JSON access log"]
  end

  subgraph Mesh
    SA["Service A (OTel SDK)"]
    SB["Service B (OTel SDK)"]
    SC["Service C (OTel SDK)"]
    SD["Service D (OTel SDK)"]
    SE["Service E (OTel SDK)"]
    IST["Envoy sidecars<br/>(ALS + Tracing)"]
  end

  subgraph Telemetry
    OTel["OpenTelemetry Collector / Data Prepper"]
    DP["Data Prepper<br/>(trace/log pipelines)"]
    OS["OpenSearch<br/>Logs + Trace Analytics"]
    Dash["OpenSearch Dashboards<br/>Alerts + SLOs"]
  end

  U -->|"HTTP/gRPC"| GW -->|"traceparent + x-request-id"| SA --> SB --> SC --> SD --> SE
  IST -.->|"ALS (access logs)"| OTel
  SA -.->|"OTLP traces/logs"| OTel
  SB -.->|"OTLP traces/logs"| OTel
  SC -.->|"OTLP traces/logs"| OTel
  SD -.->|"OTLP traces/logs"| OTel
  SE -.->|"OTLP traces/logs"| OTel
  GW -.->|"Access Logs"| OTel
  OTel --> DP --> OS --> Dash
  Dash -->|"Alert monitors"| U
