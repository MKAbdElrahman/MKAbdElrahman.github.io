
---
title: "A Dependency Injection Framework is Against Go Philosophy"
date: "2024-11-01"
description: ""
tags: [Go, Architecture]
---


If you’re a Java or .NET developer, you’re probably used to dependency injection being a core idea in web frameworks. Let's first clarify what dependency injection is: it’s simply the practice of passing dependencies as arguments. If an object requires services from another object, instead of hardwiring it, you inject it through a factory method, constructor, or even a setter method. In Go, we don’t have constructors, and setting dependencies through setters is generally discouraged in most cases. Typically, we’ll have something like this:

```go
func NewObject(dependencies...) Object {
    // Implementation here
}
```

Frameworks in languages like Java or .NET automate the wiring of dependencies for you. Although the methods vary, they often use annotations to help the compiler manage the wiring, set priorities, and resolve conflicting dependencies. Without these frameworks, you would have to handle the wiring manually somewhere—often in the `main` function. There, you instantiate all dependencies in order and manually wire each component according to the dependency tree.

Broadly, there are two main approaches:

- **Automatic wiring**
- **Manual wiring**

From my experience, automatic wiring tends to feel magical; dependencies are wired implicitly. While this is great for bootstrapping new projects, it adds a lot of complexity when it comes to understanding how system components interact. Hiding the wiring essentially hides the documentation of your system design.

Is wiring so complex that we need a framework to handle it? I don’t think so. With manual dependency injection, you start by wiring the dependencies manually at the entry point of your application—usually in the `main` function. I prefer centralizing all wiring in one place because it lets you see your architecture as code, explicitly! For instance, if you have multiple implementations of a repository, you can easily switch out one for another that implements the same interface. The complexity of wiring objects doesn’t grow over time. Initially, there may be a lot of manual wiring as you think through your application's logical design and decomposition. But once the wiring is done, the complexity is confined to the components, not the wiring itself. 

In other frameworks, you carry the burden of the framework throughout the life of the project, and refactoring to another framework can be very costly.

My advice is to avoid heavy abstractions, recommended architectures, and folder structures imposed by frameworks. In Go, understand what you are doing. Programming in Go is not like Java or C#; developers in those languages often deal with the cost of historical design mistakes. Go is modern and simple but allows you to introduce complexity only when you truly need it.