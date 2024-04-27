---
title: "EDA Patterns: Idempotent Integration with a Third-Party REST API"
date: "2024-04-26"
description: ""
tags: [Event-driven Architecture]
categories: ["Software Architecture"]
---


![IdempotentIntegrationwithaThird-PartyRESTAPI](/images/idempotent-integration.png)

**Scenario:**

You have an event-driven application that needs to integrate with a third-party REST API (Service B) without modifying it. To ensure reliable message processing, you want to introduce Service A as a mediator that consumes events and interacts with Service B in an idempotent manner.

**Idempotency Requirement:**

* **Service A:**  Service A must be idempotent to prevent duplicate event processing. This means if the same event is received multiple times, Service A should only process it once.
* **Service B:** Ideally, the calls from Service A to Service B should also be idempotent. This ensures that even if a request is retried due to network issues, it won't cause unintended side effects on Service B.

**Solution:**

Here's how Service A can achieve idempotency:

1. **Event Deduplication:**
    * Implement a mechanism to track processed events. This can be achieved using a in-memory set or a database table depending on your application's requirements and scalability needs.
    * When a new event arrives, check if its unique identifier (e.g., event ID) exists in the chosen storage mechanism.
    * If the ID is found, the event has already been processed, and Service A can safely skip further actions.
    * If the ID is not found, store it for future reference and proceed with processing the event.

2. **Idempotent Calls to Service B:**
   * If Service B supports idempotent operations (check its API documentation), leverage the recommended approach.


```go
// Track processed events (replace with database for persistence)
var processedEvents map[string]bool

// Handle an event
func handleEvent(eventID string) {
  // Check if already processed
  if processedEvents[eventID] {
    // skip duplicate event
    return
  }

  // Mark as processed
  processedEvents[eventID] = true
  
  // Process the event data

  // ...
}

// Simulate calling Service B (idempotency logic on Service B's side)
func callServiceB(eventID string) {
  // ... (idempotent call logic)
}
```