---
title: "Observability: Introduction"
date: "2024-05-16"
description: ""
categories: ["Observability"]
---

Observability is the ability to understand the internal workings of a system by examining the signals it emits externally. This means you can gain insights into the system's health and performance without needing to directly access its internal components.

There are several reasons why observability is important. It allows you to troubleshoot problems more effectively by providing you with the data you need to identify the root cause of issues. It also enables you to answer questions about how your system is behaving and to proactively identify potential problems before they occur. This allows you to perform preventative maintenance and avoid outages.

Observability is a broader concept than traditional Application Performance Monitoring (APM) which is typically used for monolithic applications. Observability is specifically designed to handle the challenges of distributed systems, such as microservices, by using tools like traces to track the flow of requests across different services.

Telemetry is the process of collecting and transmitting data from a system. It's one of the key techniques used to achieve observability. Telemetry data comes in the form of signals, which can be events, metrics, or logs.

An event is a structured record with a timestamp that describes something that happened in the system. Events have cardinality, which refers to the number of unique values for a particular attribute within the event. Traces, for example, have high cardinality because the uniqueness of each trace is important for tracking individual requests. Metrics, on the other hand, have low cardinality because we're more interested in overall trends in the data rather than the uniqueness of each data point.

Events also have dimensionality, which refers to the number of attributes associated with the event. Events with a large number of attributes are considered to be high-dimensionality events.

Traces are a specific type of event that track the journey of a single request as it flows across different services in a distributed system. Traces have both high cardinality and high dimensionality.

Logs are structured or unstructured messages emitted by a service, typically containing timestamps and details about what happened. While logs can be useful, they often have shortcomings such as being noisy, lacking context, or poorly formatted. However, logs can be valuable when they are correlated with traces to provide additional details about specific events.

Metrics capture aggregate numerical data about a system. They have low cardinality because the focus is on understanding trends over time rather than individual data points. Metrics are essential for monitoring the health of your application and identifying trends. They are most effective when used in conjunction with traces for deeper analysis.

Metrics are also crucial for establishing Service Level Objectives (SLOs), which are defined targets for the system's reliability, such as availability or success rate. By monitoring metrics, you can track your progress towards meeting your SLOs and identify areas for improvement.

## Observability Backends

Observability backends are the workhorses behind the scenes in any observability platform. They are responsible for ingesting the telemetry signals (events, metrics, and logs) emitted by your applications and services. These signals are then processed, stored, and presented in a way that allows you to analyze and understand the health and performance of your system.

**Jaeger:** A popular open-source tool for tracing distributed systems. It allows you to visualize the flow of requests across different services.

**ServiceNow Cloud Observability:** A cloud-based observability platform that provides comprehensive monitoring and visualization for your applications and infrastructure.
