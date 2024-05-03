---
title: "EDA Patterns: Thin and Thick Events"
date: "2024-05-02"
description: ""
tags: [Event-driven Architecture]
categories: ["Software Architecture"]
---

![thin and thick events](/images/thin-thick-event.svg)

Event-driven systems (EDS) rely on exchanging events for communication. Other services **subscribe** to these events and react accordingly. This loose coupling keeps services unaware of each other, with the only connection being the event's data format. Since events act as contracts, careful design and consideration for future changes are crucial.

**Scenario:** Imagine an e-commerce application. When a customer submits an order, the order service emits an "order-requested" event. Both the payment service and inventory service are interested in this event. The question is, what format should this event take?

**Thin Event:**

```json
{
  "type": "order-requested",
  "order_id": 123
}
```

This format includes only the essential data - the order ID. The payment service, needing more details, would make an additional database query to retrieve the full order information.

**Thick Event:**

```json
{
  "type": "order-requested",
  "order_id": 123,
  "customer": {
    "username": "mohamed",
    "email": "mohamed@example.com",
    "phone": "+1234567890"
  },
  "items": [
    {
      "id": 100,
      "name": "Plastic Chair",
      "quantity": 2,
      "price": 19.99
    },
    // ... additional items if applicable
  ],
  "shipping_address": {
    "street": "123 Main St",
  },
  "billing_address": { // Include if different from shipping address
    "street": "456 Elm St",

  },
  "payment_method": {
    "type": "credit_card", // Or other payment type
  },
  "promo_code": "SUMMER10" // Optional, include if used
}
```


This format includes all the order details within the event itself.

**Trade-off Analysis:**

- **Scalability and Performance:**
  - **Thick Events:** Reduce database load, improving scalability and performance.
  - **Thin Events:** Increase database load, potentially hindering scalability.
- **Contract Management:**
  - **Thin Events:** No contract management needed, as services rely on the database for details.
  - **Thick Events:** Require complex contract management. Downstream services expect specific fields with defined names and types within the event. Versioning this contract becomes an additional concern.
- **Bandwidth:**
  - **Thin Events:** Less bandwidth consumption due to smaller event size.
  - **Thick Events:** Consume more bandwidth due to larger event size.
- **Data Exposure:**
  - **Thin Events:** Services only see the data they need through database queries.
  - **Thick Events:** Services might see fields they don't care about, potentially exposing unnecessary data. Exposing excessive data can pose security risks. 
- **Consistency:**
  - **Thin Events:** All services access the database for a single source of truth, ensuring consistency.
  - **Thick Events:** Introduce eventual consistency, as multiple records representing the same data may exist temporarily across different services.


**Choosing Wisely:**

While thick events can reduce database load, their downsides like complex contract management and bandwidth consumption can outweigh the benefits. In most cases, **thin events** are preferred due to their simplicity, scalability, and loose coupling. However, the optimal choice depends on your specific needs. Weigh the trade-offs carefully to ensure a well-designed systems.


