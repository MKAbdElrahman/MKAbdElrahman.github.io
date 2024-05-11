---
title: "Coding Best Practicies: The Composed Method"
date: "2024-05-10"
description: ""
tags: [Coding Best Practicies]
categories: ["Coding Best Practicies"]
---

After reading Kent Beck's book Tidy First, I decided to check out another one by him. This time it's about the best practices in Smalltalk. Even though the book seems old, its ideas are relevant. Let me introduce you to the first pattern from the book, called the Composed Method pattern. This pattern might have different names nowadays, but its main idea is to encourage using smaller functions with names that clearly say what they do.

Later, Martin Fowler talked about this idea in his book on refactoring code, calling it "Extract Method."

Kent Beck first explains why some developers like to write long functions:

- They might think that calling functions takes overhead. But with today's fast computers, even if we need to make our code super fast, it's better to start with well-organized code and then make it faster later.
  
- Beginner Smalltalk programmers often say they can't figure out where the 'real' work is happening. But as you get better, you won't need to understand how every little piece works together. Good function names should tell you enough.


## The How

Then Beck explains how to use the pattern, giving two main ways:

- Top Down: First, you think about the main steps, and each step becomes a function you write later. The step should be in the same level of abstraction.

- Bottom Up: This is like the "Extract Method" idea, where you find a group of cohesive steps that tend to go together and turn them into a function.



