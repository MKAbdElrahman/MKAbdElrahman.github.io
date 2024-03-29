---
title: "Software Architecture: Architectural Characteristics"
date: "2024-03-29"
description: "Architectural characteristics are fundamental to making informed decisions throughout the software development process."
tags: [Software Architecture]
categories: ["Software Architecture"]
---

## Why Architectural Characteristics

Architectural characteristics are fundamental to making informed decisions throughout the software development process. Here's why they're important:

- **Guiding Architectural Decisions:** Without a clear understanding of these characteristics, it's difficult to make informed choices about your application's structure.

- **Selecting an Architectural Style:** Different architectural styles are better suited for specific needs. Analyzing characteristics helps you choose the most appropriate style for your project.

## What are Architectural Characteristics?

Let's say you're building software to solve a problem. This problem domain is the core of your application and will influence much of the design. However, architects need to consider more than just the domain. Here's where architectural characteristics come in.

In a previous post we defined the architectural characteristics dimension as the essential capabilities your system needs to possess. They determine how the system will perform, like speed (performance) and uptime (availability). These are also known as "ilities" for the suffix they often share (scalability, reliability, etc.).

We can identify architectural characteristics by these three factors:

1. **Non-domain considerations:** These are design aspects beyond the core functionality.
2. **Structural influence:** They impact the overall structure of your application.
3. **Critical factors:** They significantly affect the application's success.

Understanding which characteristics are critical helps us avoid unnecessary complexity. As architects, we should strive for a focused approach, prioritizing essential characteristics.

**Why Prioritize?**

Architectural characteristics interact and influence each other. For example, enhancing security might slow down performance (due to encryption). A common pitfall is over-engineering by including too many characteristics, leading to an overly complex design. Prioritizing critical characteristics helps us avoid this trap.

## Explicit vs. Implicit Characteristics

Architectural characteristics can be categorized into two main groups: explicit and implicit.

### **Explicit Characteristics:**

These are the clear and documented requirements of the application. They are typically outlined in documents like Software Requirement Specifications or System Design Documents. Here are some examples of explicit characteristics:

**Performance:** The application needs to handle a specific number of concurrent users or respond within a defined timeframe.

**Scalability:** The system needs to be easily expandable to accommodate future growth in data or users.

**Availability:** The application must be highly available with minimal downtime.

**Interoperability:** The system needs to seamlessly integrate with existing infrastructure or external services.

### **Implicit Characteristics:**

These are the unstated but influential factors that shape the application's structure. They are often inherent to the nature of the application or industry best practices. While not explicitly documented, they are essential considerations for the architect. Here are some examples of implicit characteristics:

- **Security:** Even if security isn't explicitly mentioned in the requirements, it's a fundamental consideration for most applications. Architects need to design systems that protect against unauthorized access, data breaches, and other security threats.
- **Maintainability:** The long-term maintainability of the codebase is crucial. While not always explicitly stated, architects should choose design patterns and technologies that promote clean, modular code that can be easily understood and modified in the future.
- **Usability:** While user experience might not be a formal requirement, designing an intuitive and user-friendly interface is often implicit.

Implicit characteristics can sometimes be inferred from the domain of the application. For example, a financial services application would have a strong implicit need for high security, while a social media platform might prioritize scalability and user experience.

## Reference

Head First Software Architecture: A Learner's Guide to Architectural Thinking by Mark Andrew Richards, Neal Ford, and Raju Gandhi

**Note: I took these note while reading "Head First Software Architecture: A Learner's Guide to Architectural Thinking by Mark Andrew Richards, Neal Ford, and Raju Gandhi". I also used AI to clean it.**
