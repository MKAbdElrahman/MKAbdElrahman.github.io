---
title: "A Short Note on Software Architecture Terminology"
date: "2024-06-18"
description: ""
tags: [Architecture Style]
categories: ["technology"]
---

Unlike other fields of science and engineering, software engineering is replete with terms that describe the same or very similar concepts. Often, these terms are created to distinguish themselves from other concepts, inadvertently obscuring their true nature and strong interrelations.

One of these terms is the "layered and modular monolith."

Other similar concepts include Clean Architecture, Onion Architecture, Ports and Adapters, and Hexagonal Architecture...

For someone new to software development, this can be very intimidating. I felt the same way when I started.

In reality, all these different architectural styles are variations of the same fundamental principles.

For example, "layered" and "modular monolith" do not imply that the layered approach lacks modularity. Instead, they signify that the modularization unit is a technical layer, such as the UI, business logic, or database.

The modular monolith adds another dimension of modularization, recognizing that business logic is often complex and requires explicit boundaries between different business domains.

This confusion extends to other architectural terms like Clean Architecture, Hexagonal Architecture, and more — names that often add to the complexity rather than clarify it.

At their core, all these concepts aim to protect business logic from infrastructure concerns. While their techniques may vary, their goals are remarkably similar.

We should emphasize the importance of modularizing and setting clear boundaries between components rather than getting lost in the plethora of variations that can muddy the field and provide little real benefit.

I have observed many of my peers rushing to decouple everything according to the ideas behind a particular style or framework, only to find it too late to untangle the resulting complexity.
