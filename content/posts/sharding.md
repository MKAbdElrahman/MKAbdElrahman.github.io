---
title: "Sharding: A Coffee Shop Analogy"
date: "2024-03-19"
description: ""
tags: [Distrubuted Systems]
categories: ["Distrubuted Storage"]
---

Remember our last blog post where we tamed the rush of order requests with a team of baristas dedicated to just reading orders? Well, get ready for another challenge! Our beloved coffee shop is brewing with excitement thanks to new customers and delicious new drinks. This means a flood of updates and sign-ups, and our trusty "master barista" who handles all these changes is feeling the pressure.

Just like throwing more baristas at a long line wouldn't be the smartest idea, we need a well-thought-out plan to tackle this avalanche of updates. In this post, we'll explore two main ways to improve our service – making our master barista stronger and spreading the work around. We'll explain everything using the fun example of our coffee shop.

## Vertical Scaling 
![alt text](/images/vertical-scaling.png)
Imagine your master barista, the one who carefully records everyone's coffee preferences, is about to reach their limit. Making them stronger at their job is what vertical scaling is all about.

Vertical scaling is a quick solution. We could train our master barista to work smarter and faster, or give them better tools like a faster tablet or a more powerful computer. This will help them handle more requests at once. However, there's a limit to how many orders one person can take. If our popularity keeps growing, vertical scaling will eventually hit a wall. 

##  Horizontal Scaling
![alt text](/images/horizental-scaling.png)
The other approach, horizontal scaling, admits that one barista can't do it all. Here's where teamwork comes in! Imagine a more advanced technique called sharding. Think of dividing customer names into sections (A-M, N-Z) and assigning each section (shard) to a specific barista. This way, update requests are automatically distributed, making things much more manageable.

## Behind the Scenes

* **Finding the Right Barista:** Our system needs to smoothly direct customers to the barista who handles their updates.

* **Sharding Efficiency:** As long as a customer's updates and requests for information (like their preferences and those of others with the same last name) go to the same barista, everything runs smoothly. But what if we need to know, for example, how many people love lattes? 

* **Keeping Everyone Busy:** If some names are more common, some baristas will be busier than others. We could use techniques like hashing to spread the work around more evenly. However, this might make it trickier to answer specific questions like "how many latte lovers have names that start with 'L'?"

* **Growing Our Team:** We can always add more baristas (resharding) as needed. But remember, the key is to find a balance between spreading out the work and making sure we can answer the questions our customers ask most often (data access patterns).

## Replication and Sharidng
![alt text](/images/replication-and-sharding.png)
We can add a replica set behind each shard. This will make handle the heavy read requests. 

Imagine each barista in our spread-out system (handling a specific customer name shard) has a team of helpers. These helpers are like trainee baristas, but they can only look up information, not change it. Their information is always up-to-date (almost) because it's constantly copied from the master barista. This allows for:

* **Faster Information for Customers:** Customers can ask about their preferences or browse the menu (read requests) without slowing down the master barista. These requests can be handled quickly by the trainee baristas, freeing up the master barista to focus on new sign-ups and updates (write requests).

* **Staying Open Even When Busy:** If a master barista (or their computer) has a problem, a trainee barista can take over seamlessly, causing minimal disruption to service.