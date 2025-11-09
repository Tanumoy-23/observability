## 🧭 Service-wise Timing in a Trace

```mermaid
sequenceDiagram
  participant Client
  participant Gateway
  participant Auth
  participant Order
  participant Payment

  Client->>Gateway: Request /checkout
  Note right of Gateway: 50 ms
  Gateway->>Auth: Validate token
  Note right of Auth: 20 ms
  Gateway->>Order: Create order
  Note right of Order: 100 ms
  Order->>Payment: Process payment
  Note right of Payment: 350 ms
  Payment-->>Order: OK
  Order-->>Gateway: 200 OK
  Gateway-->>Client: Response (520 ms total)
