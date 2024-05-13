---
title: "Coding Best Practices: How to work with constructors with long list of parameters"
date: "2024-05-13"
description: ""
categories: ["Coding Best Practices", "Go"]
---

The fourth pattern in Kent Beck's book 'Smalltalk Best Practice Patterns' is called the shortcut constructor method. It was hard for me to understand anything other than the problem it's trying to solve, which is constructors with long lists of parameters. This time, I will not mention how Kent solves this because I don't understand the solution he is proposing, or what I understood looks very stupid to me because I grew up in modern languages, and these old solutions were the best at their own time and context.

Regardless, the problem itself remains for anyone writing a constructor for a type they have defined. I will give my thinking on this.

First of all, why do we have this long list of parameters? Do all these parameters have cohesion in the sense that they are moved and processed together? If so, we need to define a new type and pass a single instance containing all the parameters. In Go, it's common that some parameters are essential, and some parameters are on the configuration side, so we group the configuration parameters into their own Configuration type.

```go
type Configuration struct {
    Param1 string
    Param2 int
    // Add more parameters as needed
}

type Type struct {
    Config Configuration
    // Other fields
}

func NewType(config Configuration) *Type {
    return &Type{
        Config: config,
    }
}

```

A second concern is that the type has many responsibilities, and many parameters are needed to initialize all fields for the tasks it can perform. This is a design problem. We can split the type into multiple types. This is also a form of increasing cohesion by decoupling unnecessary coupled responsibilities.

```go

```

A third alternative is using the builder pattern or the functional options pattern. In both cases, we supply defaults and provide convenient options or methods to override the defaults. But this works if the constructor parameters are not critical to be explicitly provided to the constructor. Remember, in Go, we don't

**Builder**

```go
type TypeBuilder struct {
    param1 string
    param2 int
    // Add more parameters as needed
}

func NewTypeBuilder() *TypeBuilder {
    return &TypeBuilder{}
}

func (builder *TypeBuilder) WithParam1(param string) *TypeBuilder {
    builder.param1 = param
    return builder
}

func (builder *TypeBuilder) WithParam2(param int) *TypeBuilder {
    builder.param2 = param
    return builder
}

func (builder *TypeBuilder) Build() *Type {
    // Apply defaults if needed
    return &Type{
        Param1: builder.param1,
        Param2: builder.param2,
        // Initialize other fields
    }
}
```

**Functional Options**

```go
type Option func(*Type)

func WithParam1(param string) Option {
    return func(t *Type) {
        t.Param1 = param
    }
}

func WithParam2(param int) Option {
    return func(t *Type) {
        t.Param2 = param
    }
}

func NewType(options ...Option) *Type {
    t := &Type{}
    for _, option := range options {
        option(t)
    }
    // Apply defaults if needed
    return t
}
```
