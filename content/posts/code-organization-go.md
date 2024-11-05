---
title: "How to Organize Your Codebase in Go (No MVCs, Onions, or Hexagons...)"
date: "2024-11-04"
description: ""
tags: [Go, Architecture]
---

Organizing code is all about how you break down your application logic into components, which typically translates to packages in Go. Early in my career, I was always searching for the “ideal” structure — where to put services, handlers, etc. There are countless articles out there recommending specific ways to organize code, each with its own approach.

Deciding on a code structure is important, but it’s often done poorly. Developers can fall into the trap of overusing patterns encouraged by frameworks, applying them in contexts that may not be suitable. For instance, someone might choose Model-View-Controller (MVC) or Ports and Adapters, then feel pressured to arrange their code to fit that pattern just to satisfy the framework. This approach skips over the real challenge: decomposing your application logic into a set of cohesive, interacting components.

Your folder structure should reflect the logical decomposition of your application. By this, I mean that all code related to a specific functionality should be kept together within the same package, with packages containing related functionality grouped closely together. The breakdown should align with the business domains and subdomains in your application.

Earlier on, I would jump straight into setting up a folder structure, only to end up with a lot of unnecessary complexity. Now, I never add a folder unless I feel the pain of not having it. Every new folder adds complexity, and it should be introduced only when necessary.

Besides logical organization, the folder structure should reflect architectural constraints. By “architectural constraints,” I’m referring to rules developers need to respect when adding or modifying behavior. The most crucial constraint is defining clear boundaries between components — deciding which components can access which. 

In a layered architecture, for example, you want to prevent the UI layer from directly accessing the database. In hexagonal architecture, dependencies should point towards the business core. An application’s architecture is shaped by its logical components and the boundaries between them.

I once worked in a layered architecture that enforced a “closed” structure, meaning the presentation layers couldn’t bypass the service layers to access the database directly. This setup ensured that repositories were hidden from handlers by making the data access layer internal to the service layer. In languages like Java, there are tools like ArchUnit that enforce similar constraints. In Go, adding static checks for dependency directions isn’t too hard and can be integrated into your CI/CD pipeline to avoid the dreaded “big ball of mud.”

Another popular approach today is the modular monolith. Unlike the layered architecture, where top-level partitioning is technical, the modular monolith partitions code by domain (e.g., inventory, users, products). This approach is excellent if you’re considering a future transition to microservices since a module can be separated out and turned into a microservice. While this requires some effort due to data model and transaction complexities, it’s still much easier than beginning with a layered architecture. 

To facilitate this approach, we can use Go’s internal package mechanism to hide dependencies between components, or even isolate them entirely so they communicate only through an external message broker like Kafka. This level of decoupling works well for large teams working on a monolith, especially if they don’t yet see the benefits of microservices or are unwilling to pay the complexity cost of a distributed system.

In the end, the logical decomposition of your application is critical. It’s difficult to change later and has a significant impact on maintainability. Aim to align your codebase and team organization with the business. It’s not about finding the best architecture; it’s about understanding trade-offs and creating a solution that fits your specific business needs. The architectures out three are designs with patterns that fit them. Find yours. 