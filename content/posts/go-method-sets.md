---
title: "Method Sets in Go"
date: "2025-01-13"
description: ""
tags: ["Go"]
---
### **Method Sets in Go**

#### **Introduction to Method Sets**
The method set of a type defines the methods that can be called on a variable of that type. This concept is crucial for working with methods, interfaces, and type implementations in Go. Method sets differ based on whether the type is a value, a pointer, or an interface.

---

#### **Method Set of a Value Type**
The method set of a value type includes only methods with **value receivers**. Methods with pointer receivers are **not** part of the method set of a value type. However, if the value is **addressable**, Go allows calling pointer receiver methods by implicitly taking the address of the value.

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
- `Increment` is part of the method set of `Counter` because it has a value receiver.
- `Reset` is **not** part of the method set of `Counter` but can still be called on an addressable value.

**Behavior with Addressable Values:**
```go
func main() {
    var c Counter = 10
    c.Increment() // Works: Increment has a value receiver
    c.Reset()     // Works: c is addressable, so Go implicitly takes its address
}
```

**Behavior with Non-Addressable Values:**
```go
func main() {
    Counter(4).Reset() // Fails: Counter(4) is a non-addressable value
}
```
- `Counter(4)` is a temporary value and does not have an address.
- Attempting to call `Reset()` fails because Go cannot take the address of `Counter(4)`.

---

#### **Method Set of a Pointer Type**
The method set of a pointer type includes **all methods** associated with the type, regardless of whether they have a value receiver or a pointer receiver. This makes pointer types more flexible when implementing interfaces.


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
- If a method has a pointer receiver, it cannot be called on a non-addressable value.
- Returning a pointer type from a constructor ensures that all methods (including those with pointer receivers) are part of the method set, enabling interface implementation.


#### **Summary of Method Sets**
1. **Value Type**:
   - Includes only methods with value receivers.
   - Can call methods with pointer receivers **only if the value is addressable**.

2. **Pointer Type**:
   - Includes all methods (both value and pointer receivers).

3. **Interface Type**:
   - Includes all declared methods and methods from embedded interfaces.

**Key Rules:**
- Addressable values (e.g., variables) can call methods with both value and pointer receivers.
- Non-addressable values (e.g., literals, type conversions) can only call methods with value receivers.
- Pointer types are more flexible and can always call all methods.

---

#### **Practical Implications**
1. **Constructors and Return Types**:
   - Returning a value type may prevent calling methods with pointer receivers if the value is not assigned to a variable.
   - Returning a pointer ensures that all methods are callable.

**Example:**
```go
func NewCounter() Counter {
    return Counter(0)
}

func main() {
    NewCounter().Reset() // Fails: NewCounter() returns a non-addressable value
}
```
To fix this, return a pointer:
```go
func NewCounter() *Counter {
    return new(Counter)
}

func main() {
    NewCounter().Reset() // Works: NewCounter() returns a pointer
}
```

2. **Interface Implementation**:
   - Ensure that the method set of a concrete type is a superset of the interface's method set.
   - Use pointer receivers if the method needs to modify the receiver.

