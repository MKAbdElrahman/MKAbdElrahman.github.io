---
title: "Method Sets in Go"
date: "2025-01-13"
description: ""
tags: ["Go"]
---

#### **Introduction to Method Sets**
In Go, the method set of a type defines the methods that can be called on a variable of that type. This concept is fundamental when working with methods, interfaces, and type implementations. Method sets vary depending on whether the type is a value, a pointer, or an interface.

---

#### **Method Set of a Value Type**
The method set of a value type includes only those methods that have a **value receiver**. Methods with pointer receivers are **not** part of the method set of a value type.


**Example:**
```go
type Counter int

// Method with a value receiver
func (c Counter) Increment() Counter {
    return c + 1
}

// Method with a pointer receiver
func (c *Counter) Reset() {
    *c = 0
}
```
In this example:
- `Increment` is part of the method set of `Counter` because it has a value receiver.
- `Reset` is **not** part of the method set of `Counter` because it has a pointer receiver.

**Why This Matters:**
Go cannot guarantee that every value of a type will have a valid memory address. For instance, a literal value like `42` cannot have its address taken. As a result, Go restricts the method set of value types to methods with value receivers.

```go
var c Counter = 10
c.Increment() // Works
c.Reset()     // Error: Cannot call Reset on a value type
```

---

#### **Method Set of a Pointer Type**
The method set of a pointer type includes **all methods** associated with the type, regardless of whether they have a value receiver or a pointer receiver. This makes pointer types more flexible when implementing interfaces.

**Example:**
```go
type Resettable interface {
    Reset()
}

var c Counter = 10
var r Resettable = &c // Works because &c is a pointer
r.Reset()             // Works
```
Here, assigning the address of `c` to a variable of type `Resettable` works because Go can always dereference the pointer to access the underlying value.

**Key Point:**
When you pass a pointer, Go can always derive both the pointer and the value. However, passing a value does not guarantee the ability to derive a pointer, as seen in the previous example.

---

#### **Method Set of an Interface Type**
The method set of an interface type is the set of methods declared in the interface. If the interface embeds other interfaces, its method set includes all methods from the embedded interfaces as well.

**Example:**
```go
type Reader interface {
    Read() string
}

type Writer interface {
    Write(string)
}

type ReadWriter interface {
    Reader
    Writer
}
```
In this case:
- The method set of `Reader` is `{Read}`.
- The method set of `Writer` is `{Write}`.
- The method set of `ReadWriter` is `{Read, Write}`.

**Formal Definition:**
The method set of an interface is the union of:
1. Methods directly declared in the interface.
2. Methods from all embedded interfaces (recursively).

**Interface Implementation:**
A concrete type implements an interface if its method set includes all methods in the interface's method set. Go performs this check at compile time without requiring explicit declarations.

---

#### **Why Method Sets Matter in Practice**
Understanding method sets helps explain why certain code behaves the way it does. For example:
- If a method has a pointer receiver, it cannot be called on a value type.
- Returning a pointer type from a constructor ensures that all methods defined on `T or *T` are part of the method set.


---

#### **Summary of Method Sets**
1. **Value Type**: Includes only methods with value receivers.
2. **Pointer Type**: Includes all methods (both value and pointer receivers).
3. **Interface Type**: Includes all declared methods and methods from embedded interfaces.

