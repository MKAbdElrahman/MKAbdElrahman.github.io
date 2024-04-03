---
title: "Software Architecture: Partitioning Strategies"
date: "2024-04-02"
description: ""
tags: [Software Architecture]
categories: ["Software Architecture"]
---


## Distributed or Single Process

![alt text](/images/single-distributed.png)

**Single Process Application:** A self-contained program that executes on a single computer. It handles all its processing tasks, data storage, and retrieval using the local machine's resources (memory and storage). Examples include offline games and mobile apps without network functionality.

**Distributed System:** A software program designed to function across multiple computers connected over a network. These applications are broken down into smaller components that run on different machines. These components communicate with each other to achieve a common goal.

A basic distributed system can consist of just two components like a Backend API and a Database.

A microservices architecture is a more complex form of a distributed system. Here, the application is broken down into many smaller, independently deployable services, each with a specific function. These services communicate with each other using well-defined APIs.

## Domain Partitoning
![alt text](/images/domain-partitioning.png)

The application is divided into modules or components, each aligned with a specific business subdomain.  This means modules are responsible for the functionality and data related to their assigned subdomain.

**Example:**

Imagine an e-commerce application. Domain partitioning could create separate modules for:

- **Product Catalog:** Manages product information, images, and descriptions.
- **Shopping Cart:** Handles adding and removing items from the cart, calculating totals, and applying promotions.
- **Payment Processing:** Securely processes customer payments and interacts with payment gateways.
- **Order Fulfillment:** Tracks orders, manages shipping, and interacts with shipping carriers.
- **Customer Account Management:** Allows customers to create accounts, manage profiles, and track order history.


**Implementation Styles:**

Domain partitioning can be implemented in different ways depending on the overall architecture of your application:

* **Distributed Systems (Microservices):**  In microservices, each domain partition becomes a separate, independent service.  These services run on their own processes, potentially on different machines, and communicate with each other via APIs.

* **Single Process (Modular Monolith):**  Even within a single process application (monolith), domain partitioning can be beneficial.  The application code can be organized into modules that align with business subdomains.


## Technical Partitioning
![alt text](/images/technical-partitioning.png)
Unlike domain partitioning, which focuses on business functionalities, technical partitioning divides the system based on technical characteristics. 

Imagine a web application for managing customer accounts.  Technical partitioning could create separate layers:

* **Presentation Layer:** Handles the web user interface for creating, editing, and viewing customer accounts.
* **Business Logic Layer:** Contains the application logic for validating user input, processing account information, and interacting with the data layer.
* **Data Access Layer:**  Talks to the database to store and retrieve customer account data.

**Implementation Styles:**

Similar to domain partitioning, technical partitioning can be applied in both distributed and single-process applications:

* **Distributed Systems:** In a distributed system, each technical layer could be implemented as a separate service running on its own process.

* **Single Process Applications:** Even within a single process, technical layers can be used to organize the codebase around technical concerns. 


## Monolith
 
I avoided using the word "monolith" because it can refer to both single-process and distributed applications. As long as application components cannot be deployed independently, they exhibit characteristics of a monolithic architecture. The common term used to describe such applications is a two-tier or three-tier architecture.