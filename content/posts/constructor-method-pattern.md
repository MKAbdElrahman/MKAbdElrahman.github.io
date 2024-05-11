---
title: "Coding Best Practices: The Constructor Method"
date: "2024-05-11"
description: ""
categories: ["Coding Best Practices", "Go"]
---

One way to construct an object in GoLang is by first creating an object of the type and then directly accessing the fields to put the object in a valid state.

```go
user := &User{}
user.Name = "Mohamed"
user.Age = 29
```

The problem with this approach includes:

- Forcing clients to delve into the source code to understand which fields are essential for initialization.
- Lack of validation.
- Exposing implementation details.
- Increasing the surface area of our type, creating more potential points of coupling.

We can improve this by using a constructor function:

```go
func NewUser(name string, age int) *User {
    // validate the input parameters
    // return the object in a valid state
}
```

Here, it's clear what parameters are needed to create an instance of the `User` type. Additionally, we ensure we don't start with corrupted objects.

An additional improvement is to hide the field members from external callers by giving them private package scope:

```go
type User struct {
    name string
    age  int
    // many other fields
}
```

In Go, if the field member starts with a lowercase letter, it can't be accessed outside the package it was created in. This way, we completely hide the internal representation of the object.

Moreover, there could be multiple ways to create an instance. We can define a constructor for each way. We can also use the functional options pattern:

```go
func NewUser(name string, age string, opts ...Options) {
}
```

These options give us tools to fine-tune the instantiated object and provide users a way to modify the fields which shouldn't or are not recommended to be accessed directly.
