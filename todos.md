# 🧩 Observability Enhancement – JIRA Task Breakdown

---

## **EPIC 1: Gateway-based Latency Tracking and Request Correlation**

### 🎯 Goal  
Measure request latency and identify slow services directly from Gateway and Istio logs using correlation IDs.

---

### **Story 1.1 – Implement Request Correlation via `x-request-id` and `traceparent`**

**Description:**  
Enhance the Gateway to inject both `x-request-id` and W3C `traceparent` headers into every incoming request and propagate them downstream.

**Acceptance Criteria:**
- Each request gets a unique `x-request-id`.
- The same ID appears in all downstream service logs.
- Gateway logs contain request start/end timestamps, status code, and both IDs.

---

### **Story 1.2 – Compute End-to-End Request Latency from Gateway Logs**

**Description:**  
Modify Gateway log processor to compute the total time taken per request using start and end log entries.

**Acceptance Criteria:**
- For every `x-request-id`, calculate `(response_timestamp - request_timestamp)`.
- Persist latency metrics in OpenSearch or metrics store.
- Provide visualization or dashboard for high-latency requests.

---

### **Story 1.3 – Detect Slow Services via Istio/Envoy Access Logs**

**Description:**  
Integrate Istio access logs (ALS) into OpenTelemetry/Data Prepper for latency analysis per service and route.

**Acceptance Criteria:**
- Capture service-level response times and status codes from Envoy sidecars.
- Identify top 10 slowest routes and services.
- Generate alerts if p95 or p99 latency exceeds threshold.

---

## **EPIC 2: End-to-End Trace + Span Enablement (Error Correlation)**

### 🎯 Goal  
Enable distributed tracing across services A → B → C → D → E to visualize the entire request flow and capture full trace details when errors occur.

---

### **Story 2.1 – Enable OpenTelemetry SDK/Agent in All Services**

**Description:**  
Add OpenTelemetry SDKs (or agents) in all microservices to generate spans and export traces to the OTel Collector / Data Prepper.

**Acceptance Criteria:**
- Each service exports spans for inbound/outbound calls.
- Trace IDs correlate across all services.
- Trace visualization in OpenSearch shows full A→E flow.

---

### **Story 2.2 – Configure Tail-Based Sampling for Failed or Slow Requests**

**Description:**  
Implement tail-based sampling in the OTel Collector or Data Prepper so that only failed or slow traces are retained.

**Acceptance Criteria:**
- Requests with `status=ERROR` or latency > threshold are sampled.
- Successful requests are dropped.
- Verify sampled traces contain full flow and durations.

---

### **Story 2.3 – Error Flow Logging Integration**

**Description:**  
Integrate error and warning logs with their corresponding trace IDs.

**Acceptance Criteria:**
- If a service logs an `ERROR`, the log contains both `x-request-id` and `trace_id`.
- In OpenSearch Dashboards, users can click from an error log → corresponding trace.

---

### **Story 2.4 – Trace-based Alerting**

**Description:**  
Create alerts for failed traces or latency breaches using OpenSearch monitors.

**Acceptance Criteria:**
- Alerts trigger when p95 or p99 latency per service exceeds limit.
- Alerts trigger when `status=ERROR` spans occur.
- Alerts sent via email/webhook/Slack channel.

---

## **EPIC 3: Log Level Control and Storage Optimization**

### 🎯 Goal  
Allow clients to dynamically control log levels and prune Info/Debug logs to optimize storage cost.

---

### **Story 3.1 – Implement Runtime Log Level Configuration**

**Description:**  
Introduce configuration (via ConfigMap or API) to dynamically enable/disable log levels (`INFO`, `DEBUG`, `WARN`, `ERROR`) per service.

**Acceptance Criteria:**
- Configurable through config file or REST API.
- Service reloads log level without restart.
- Log level state visible in dashboard.

---

### **Story 3.2 – Implement Log Retention and Pruning Mechanism**

**Description:**  
Introduce time-based and volume-based log pruning (FIFO deletion) for lower-priority logs (`INFO`, `DEBUG`).

**Acceptance Criteria:**
- Day-wise retention policy per log level.
- Volume threshold-based auto-deletion (FIFO).
- Configurable thresholds per client/environment.

---

### **Story 3.3 – Separate Log Storage for Error/Warn vs Info/Debug**

**Description:**  
Route `ERROR`/`WARN` logs to OpenSearch and `INFO`/`DEBUG` to local or cheaper storage.

**Acceptance Criteria:**
- Different sinks for different log levels.
- Error logs searchable in OpenSearch.
- Info/debug logs accessible locally or via object storage.

---

### **Story 3.4 – Dashboard for Log Volume & Retention**

**Description:**  
Create OpenSearch dashboard widgets for monitoring log ingestion, retention status, and storage usage.

**Acceptance Criteria:**
- Log volume trend per service.
- Alert when configured log storage exceeds threshold.
- Visualization of deletion/pruning metrics.

---

## **Summary of JIRA Hierarchy**

| **Epic** | **Purpose** | **Key Deliverables** |
|-----------|--------------|----------------------|
| **1. Gateway & Istio Latency Tracking** | Measure and alert on latency per request and service | Correlated logs, latency metrics, slow-service detection |
| **2. Tracing Enablement (Trace–Span)** | Full request flow visibility + error trace capture | Distributed traces, tail-based sampling, error correlation |
| **3. Log Management Optimization** | Reduce log cost & improve flexibility | Dynamic log levels, pruning, retention policy |
