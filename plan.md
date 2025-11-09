graph TD
  subgraph Client
    U[User / Caller]
  end

  subgraph Edge
    GW[Envoy/Istio Gateway\ninjects x-request-id + traceparent\nJSON access log]
  end

  subgraph Mesh
    A[Service A (OTel SDK)]
    B[Service B (OTel SDK)]
    C[Service C (OTel SDK)]
    D[Service D (OTel SDK)]
    E[Service E (OTel SDK)]
    Istio[Envoy sidecars\n(ALS + Tracing)]
  end

  subgraph Telemetry
    OTel[OpenTelemetry Collector / Data Prepper]
    DP[Data Prepper\n(trace/log pipelines)]
    OS[(OpenSearch\nLogs + Trace Analytics)]
    Dash[OpenSearch Dashboards\nAlerts + SLOs]
  end

  U -->|HTTP/gRPC| GW -->|traceparent + x-request-id| A --> B --> C --> D --> E
  Istio -.->|ALS (access logs)| OTel
  A -.->|OTLP traces/logs| OTel
  B -.->|OTLP traces/logs| OTel
  C -.->|OTLP traces/logs| OTel
  D -.->|OTLP traces/logs| OTel
  E -.->|OTLP traces/logs| OTel
  GW -.->|Access Logs| OTel
  OTel --> DP --> OS --> Dash
  Dash -->|Alert monitors| U
