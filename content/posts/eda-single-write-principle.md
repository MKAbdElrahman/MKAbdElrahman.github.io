---
title: "EDA Patterns: The Single Writer Principle"
date: "2024-04-29"
description: ""
tags: [Event-driven Architecture]
categories: ["Software Architecture"]
---

The Single Writer Principle states that only one service should be responsible for making changes to a specific type of data or event.

Let's consider an e-commerce system with separate services for Orders, Payments, and Shipments. In this scenario, the Order service serves as the single writer for all order-related data. Here's how it works:

1. When a customer clicks "buy," the Basket service triggers an "Order Requested" event.
2. The Order service receives this event, validates the order details, and publishes an "Order Confirmed" event if successful.
3. Other services, such as Payments and Shipments, subscribe to relevant events and take necessary actions upon receiving them (e.g., processing payment or preparing shipment).

The Order service orchestrates these actions, ensuring that all changes to order data are coordinated and consistent.

![Single Writer Per Topic](/images/writer-per-topic.svg)
**Figure: all order state changes are managed by the orders service. The payment service manages payments topic and can't write directly to the orders topic.**

### Command and Entity Topics: A Flexible Approach

The Single Writer Principle can be implemented using two topics per entity: Command and Entity.

**Command Topic**

This topic allows any service to initiate actions related to the entity, functioning like a trigger or a suggestion box.

- **Example:** In an e-commerce system, the "OrderRequested" event might be published to the Command topic by the Basket service when a customer clicks "buy."

**Entity Topic**

Only the the single writer can publish updates to this topic, serving as the official record book maintained by a single authority.

- **Example:** The Order service, as the single writer for order data, would publish updates like "OrderConfirmed" to the Entity topic (e.g., "OrdersTopic").

## Single Writer Per Transition

This approach relaxes the concept of a single writer owning all updates within a topic. Instead, services might own specific transitions (stages) within the data lifecycle.

**Example: Order Processing with Payment Integration**

Consider an e-commerce system where a "Payment" topic is owned by a dedicated Payment service. In this variant:

- The Order service remains responsible for most order-related transitions (e.g., "Order Placed," "Shipped").
- Instead of owning a separate topic, the Payment service adds a "Payment" section to the existing order message.
- The Payment service then manages the transition of updating the "Payment" section within the order message (e.g., "Payment Confirmed").

![Single Writer Per Transition](/images/writer-per-transition.svg)

**Figure: Each transition is owned by a single writer.**
