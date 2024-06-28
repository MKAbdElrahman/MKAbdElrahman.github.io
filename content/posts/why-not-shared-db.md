---
title: "Microservices: Why Not a Shared Monolithic Database?"
date: "2024-06-28"
description: ""
tags: [Software Architecture]
categories: ["Software Architecture","Microservices"]
---




![alt text](/images/microservices/shared-db.png)

In microservices, the idea of using a shared monolithic database is always rejected. There are several reasons why this approach is not advisable. Below are the key considerations that highlight the drawbacks of a shared monolithic database.

### Change Control

Managing changes in a shared monolithic database is a complex task. Any schema change necessitates coordination across all dependent services, which can be cumbersome and time-consuming. This coordination is required to ensure that all services are redeployed simultaneously to prevent schema mismatch errors, which can cause significant downtime and service disruption.

### Database Connections

Each microservice typically has its own connection pool to the database. As the number of microservices grows, the number of database connections increases correspondingly. This can lead to database timeouts and performance degradation due to the sheer volume of simultaneous connections. 

In contrast, a monolithic application typically requires fewer connections because a single query can handle operations that span multiple functions within the same application. However, in a microservices architecture, a single business transaction might involve multiple services, each initiating its own database connection. This multiplies the number of connections required and can strain the database's capacity.

### Scalability

Scalability is another significant concern. In a microservices architecture, individual services can be scaled independently based on their load and resource requirements. However, if all these services rely on a single shared database, the database becomes a bottleneck. As you scale the number of service instances, you must also scale the database to handle the increased load. This can be challenging and often requires sophisticated sharding and partitioning strategies to distribute the load effectively.

### Fault Tolerance

A shared monolithic database represents a single point of failure. If the database experiences downtime or performance issues, it impacts all the services that depend on it. This can lead to widespread outages and significantly affect the overall reliability of your system. In contrast, microservices architecture aims to isolate failures within individual services, so issues in one service do not necessarily bring down the entire system. Using a shared database undermines this principle of fault isolation and can lead to cascading failures.

### Conclusion

While a shared monolithic database might seem convenient, especially during the initial stages of transitioning to a microservices architecture, the long-term drawbacks are substantial. Challenges in change control, database connection management, scalability, and fault tolerance make it clear that relying on a shared database can hinder the advantages offered by microservices. It is generally more effective to adopt a database per service approach, allowing each microservice to manage its data independently. This approach aligns better with the principles of microservices and ensures a more resilient, scalable, and manageable architecture.