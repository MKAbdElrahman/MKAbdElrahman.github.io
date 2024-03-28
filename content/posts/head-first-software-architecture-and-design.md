---
title: "Software Architecture Vs. Design"
date: "2024-03-27"
description: "The line between architecture and design can sometimes be blurry.  Understanding the difference, however, is crucial for determining who should make key decisions throughout a project."
tags: [Software Architecture]
categories: ["Software Architecture"]
---

Architecture is primarily concerned with the structure of the system, including its services, databases, and how they communicate with each other. Design, on the other hand, focuses more easy to change later decesions like choosing a design pattern or splitting or renaming a class.

The line between architecture and design can sometimes be blurry. Understanding the difference, however, is crucial for determining who should make key decisions throughout a project. The development team handles decisions related to implementing features, such as designing classes. Architectural decisions, on the other hand, focus on the system's overall structure, like choosing an architectural style. Finally, some decisions require collaboration from both sides, such as breaking down or merging services within the system.

Most decisions you encounter won't fall neatly into one category or the other. They'll likely exist on a spectrum between pure architecture and pure design. To determine who should make a particular decision, consider several factors. Strategic decisions, which are long-term and have a lasting impact on future actions, lean more towards the architectural side of the spectrum. Conversely, tactical decisions, which are short-term and independent of other actions, tend to be more design-related. Another factor to consider is the effort involved. Architectural decisions typically require more effort to make or change compared to design decisions. Decisions with significant trade-offs, requiring careful evaluation of pros and cons, also tend to be more architectural in nature. Conversely, decisions with less significant trade-offs can be made more quickly and with less analysis, leaning more towards the design side.
